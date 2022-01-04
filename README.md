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

charts folder
- if this chart depends on any other chart to get its job done. Those other charts will be pulled and stored in this folder
- 

Templates
- All templates
- helpers.tpl , hpa.yaml , NOTES.txt , serviceaccount.yaml , deployment.yaml , ingress.yaml  service.yaml files are created

Values.yml
- values for template







