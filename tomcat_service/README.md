Tomcat service
===================

As a consequence of none secure connection in Elastic search server, this Tomcat RESTful service is made to process a request to Elastic server with a secure connection (HTTP Basic Auth is required)

Download
-------------
Clone

Usage
-------------
### Deploy to Google Cloud
- Get credentials from cluster
```shell
gcloud container clusters get-credentials [CLUSTER_NAME] --zone [CLUSTER_ZONE] --project [PROJECT_ID]
```
- Add your project ID to environment variable
```shell
PROJECT_ID="$(gcloud config get-value project)"
```
- Clone this repository (Username and Password are required)
```shell
git clone https://github.com/thieunguyenhung/newai-elasticsearch.git
cd /newai-elasticsearch/tomcat_service
```
- Build image from Dockerfile, we host it in **Asia** and call it **my-elastic-tomcat** with Tag **v1**
```shell
docker build -t asia.gcr.io/${PROJECT_ID}/my-elastic-tomcat:v1 .
```
- Push it to Google Cloud [Container Registry](https://cloud.google.com/container-registry/docs/pushing-and-pulling)
```shell
gcloud docker -- push asia.gcr.io/${PROJECT_ID}/my-elastic-tomcat:v1
```
- Deploy image to pod, the deployment named tomcat-service and has port 8080
```shell
kubectl run tomcat-service --image=asia.gcr.io/${PROJECT_ID}/my-elastic-tomcat:v1 --port 8080
```
- Expose the deployment named tomcat-service to port 8080
```shell
kubectl expose deployment tomcat-service --type=LoadBalancer --port 8080
```
Visit this [tutorial](https://cloud.google.com/container-engine/docs/tutorials/hello-node) for more information

### Deploy WAR file to Tomcat webapps
- Get tomcat-service pod name (that you just deployed it)
```shell
$ kubectl get pods
NAME                                READY     STATUS    RESTARTS   AGE
tomcat-service-1551901680-38sgk     1/1       Running   0          3m
```
- SSH to pod by pod name above
```shell
kubectl exec -it tomcat-service-1551901680-38sgk bash
```
- Clone this repository again in directory */home/workspace/*
```shell
cd /home/workspace/
git clone https://github.com/thieunguyenhung/newai-elasticsearch.git
```
- Copy WAR file to Tomcat webapps folder
```shell
cp /home/workspace/newai-elasticsearch/tomcat_service/newai-spelling-api.war /usr/local/tomcat/webapps/
```

### Sending a request to service
- Send a POST request with JSON body
```shell
{
    "text":"[Text need to check]"
}
```
- Remember to add Authorization field in the request header with username:password (must be encoded by Base64) that matches with string in *auth_string.txt*

Note
-------------
- You can change authentication string in */usr/local/tomcat/webapps/newai-spelling-api/conf/auth_string.txt*
- URL to connect to Elastic search server in */usr/local/tomcat/webapps/newai-spelling-api/conf/elasticsearch_url.txt*. In case you want to edit this file, remember just only one line, **NO** new-line or end-line is allowed. Use the below command to edit the file without unexpected new-line
```shell
nano [FILE_PATH] --nonewlines
```

Source
-------------
You can find the source code in this repository [newai-spelling-api](https://github.com/thieunguyenhung/newai-spelling-api)

License 
-------------
- [Apache license 2.0](https://www.apache.org/licenses/LICENSE-2.0)

### © 2017 NewAI Team