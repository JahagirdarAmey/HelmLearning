# HelmLearning

Install helm 
- `choco install kuberenetes-helm`

Helm help 
- `helm --help`

To check helm repo is used 
- `helm repo list`
- Output - `bitnami https://charts.bitnami.com/bitnami`

To add helm repo 
- `helm repo add bitnami https://charts.bitnami.com/bitnam` 

To seach apache from repo 
- `helm search repo apache`

To search apache with all versions 
- `helm search repo apache --versions`

To remove helm repo 
- `helm repo remove bitnami`

To install mysql helm chart 
-`helm install mydb bitnami/mysql`

To get status log 
-`helm status mydb`

To list all charts from k8s cluster 
-`helm list`

To delete specific helm chart 
-`helm delete mysqldb`

To uninstall  specific helm chart 
-`helm uninstall mysqldb --keep-history` 
- #TODO Difference between delete & uninstall. keep-history to keep ability to rollback

To update helm repo 
-`helm repo update`

To update helm chart 
-`helm upgrade ${COMMANDS}`

To update helm chart with some values 
-`helm upgrade ${COMMANDS} --values ${helm-chart}/values.yml`
- Upgrade bumps up the REVISION
- `--resuse-values` to reuse the value
- If you do not provide `--values` or `--set` helm will pick up default values
- Helm persists release history in kubernetes secrets 

Helm release workflow
- Load chart
- Parses values.yml
- Generate the yml. Replace values from values.yml & generates new files 
- Parse yaml to kube objects and validates against k8s schema 
- Generate yml and send to kube


Dry run
- Just loads chart but does not run it
- Prints config files
- `helm install mypiyudb bitnami/mysql --dry-run`
- Mostly used in template
- Can contain nonk8s syntax

helm template
- Similar to dry-run but uses k8s only syntax
- Does not communicate with k8s cluster. Great for cicd where pipelines does not have access

helm get
- To just get release notes - `helm get notes mydb`
- To get values we used - `helm get values mydb`
- To get all values - `helm get values mydb --all`
- To get values for perticular revision - `helm get values mydb --revision 1`
- To get manifest - `helm get manifest mydb`

helm history 
- History of installations and upgrades - `helm history mydb`

To rollback 
- `helm rollback mydb 1` # while unistalling keep flag -> `--keep-history`

Namespace
- `helm install mydb bitnami/apache --namespace prod --create-namespace` # This creates namespace and uses it

Upgrade or install 
- `helm upgrade --install mydb bitnami/apache`
- Very usefull in CICD


Chart name with templating
- `helm install bitnami/apache --generate-name` # random 
- `helm install bitnami/apache --generate-name --name-template "mywebserver-{{randAlpha 7 | lower}}"` # webserver with 7 aphabets in lower

Wait
-`helm install bitnami/apache --wait` # waits for pods up and running then only considers helm installation successful . Time 5 minutes
-`helm install bitnami/apache --wait --timeout 5m10s` # waits for pods up and running then only considers helm installation successful for specified time

Atomic
-`helm install bitnami/apache --atomic` # rollsback to prev successful release if helm installation fails. --wait is inclusive. 
-`helm install bitnami/apache --atomic --timeout 5m0s` # rollsback to prev successful release if helm installation fails. 

When you want to forcefully start the pods when you do upgrade. Helm will delete the deployment 7 will re-create the deployment 
- `helm upgrade mydb bitnami/apache --force`
- Downtime as all pods are restarted

Cleanup on failure 
- `helm upgrade mydb bitnami/apache --force`
- When ugrade fails, cleanup all the resources that were created. 
- We dont want them angling out there 
- Dont use if you want to debug


### Create charts 

To create chart
- `helm create firstchart`
- By default helm uses nginx chart to create our chart but we can use different type of starter packs to create helm charts 
- `firstchart` folder is created 
  - `charts` folder is created
  - `templates` folder is created
    - `tests` folder is created
    - helpers.tpl , hpa.yaml , NOTES.txt , serviceaccount.yaml , deployment.yaml , ingress.yaml  service.yaml files are created
  - values.yml, ignore file, chart.yml 


chart.yml
- metadata about chart
- Feilds
  - apiVersion
  - name
  - version - version of the chart
  - type - Either application or library   
  - appVersion - version of the application whch is being packeged in the chart
  - icon - JPEG, SVG
  - keywords - json list to desc project
  - home - home url
  - sources - from where project can be downloaded
  - maintainers - list of maintainers along with email 

charts folder
- if this chart depends on any other chart to get its job done. Those other charts will be pulled and stored in this folder

Templates
- All templates
- helpers.tpl , hpa.yaml , NOTES.txt , serviceaccount.yaml , deployment.yaml , ingress.yaml  service.yaml files are created
- If you think, your project does not require service account, You can get rid of it. 
- helpers.tp -> helper file. Any template method you want to use across , Use this file. define / include etc

Values.yml
- values for template
- It will have default values. Can be overridden 


To install created chart
- `helm install firstapp firstchart`
- firstapp is release name 
- prints enough information to access nginx pods from our local machine 
- chart folder -> templet -> notes.txt 
- As this is nginx, by default clusterIp as serviceType is used. As this is clusterIp, Only accesible from cluster. You can do port forwarding
- When you do port forwarding, Welcome to nginx is disbaled on browser (Open URL first)

To package helm chart
- `helm package firstchart\`
- Packages and saves it to .tgz directory 
- helm install can work with .tgz files
- `helm package firstchart\ -u` This will pull the latest dependencies 
- `helm package firstchart\ -d` This is to specify the destination. 

helm lint
- `helm lint firstchart`
- scans the folder and validates it. Can take multiple chrts as input

### Template Deep Drive
- helm uses Go programming language text templating engine 
- Actions
  - starts with & ends wih `{{ }}`
  - We can use variables, loops, conditions , invoking methods    
  - Most of the action starts with -. This is to remove any trailing or leading whitespaces
  - . => All the information template can use. .Values =? contains all the information from values.yml
    - .Values, .Chart, .Release, .Template

- Pipelines
- Allows us to pass out of LHS to RHS
- `{{ .Values.myval | default "mytest"}}` => If .Values.myval does not contain any value, default will be used
- `{{ .Values.myval | upper}}` => to upper case
- `{{ .Values.myval | quotes}}` => to add quotes
- `{{ .Values.myval | default "mytest" | upper | quotes}}` => Chain of commands


Functions
- default, upper, quotes, lower 
- nindent 4 => Adds new line and 4 spaces
- `{{- toYaml . }}` => We get values as object. Convert object to yaml
- Seach helm template function list 

Conditional 
- 
```{{- if .Value.mybooleanval }}
 put whatever 
{{- end}}```
-
```{{- if .Value.mybooleanval }}
 put whatever 
{{- else}}
put whatever 
{{- end}}```
-
`{{- if not .Value.mybooleanval }}`
`{{- if and .Value.mybooleanval .Value.mybooleanval2 }}`
`{{- if or .Value.mybooleanval .Value.mybooleanval2 }}`


with
- Values.yml
`
myvals
  - India
  - Germany
  - UK
  - US
`

```
{{-with .Values.myvals}}
{{ - toYaml . | nindent 2}}
{{- end}}
```
- This will print all myvals with 2 space indentation 
- . here points to object returnd by -with
- If you want to point to root object, use $.

Variables
- `{{ $myFLAG := true }}`
- `{{ $myVALUE := "Val" }}`

Loop
```
{{-range .Values.myvals}}
  {{. | upper}}
{{- end }}
```


Looping dictnory types
```
{{-range $key,$value := .Values.myvals}}
  -{{$key}} : {{$value | quote}}
{{- end }}
```

Template file
- Extension - `.tpl`
- Cooments
- `{{/* */}}`
- 
```{{-define "firstchat.name" -}}

{{- end}}
``` 
  - Defines name of the template
  - dirstname => namespace
  - name => name of the function 
- To use thi
`
{{ include "firstchart.name"}}
`












