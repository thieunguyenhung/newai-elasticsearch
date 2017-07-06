elastic Search Dockerfile
===================

Dockerfile to build elastic search server (v5.4.3)

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
- Clone this repository
```shell
git clone https://github.com/thieunguyenhung/newai-elasticsearch.git
cd newai-elasticsearch/elasticsearch/
```
- Build image from Dockerfile, we host it in **Asia** and call it **my-elastic** with Tag **v1**
```shell
docker build -t asia.gcr.io/${PROJECT_ID}/my-elastic:v1 .
```
- Push it to Google Cloud [Container Registry](https://cloud.google.com/container-registry/docs/pushing-and-pulling)
```shell
gcloud docker -- push asia.gcr.io/${PROJECT_ID}/my-elastic:v1
```
- Deploy image to pod, the deployment named elastic-service and has port 9200
```shell
kubectl run elastic-service --image=asia.gcr.io/${PROJECT_ID}/newai-elastic:v1 --port 9200
```
- Expose the deployment named elastic-service to port 9200
```shell
kubectl expose deployment elastic-service --type=LoadBalancer --port 9200
```
Visit this [tutorial](https://cloud.google.com/container-engine/docs/tutorials/hello-node) for more information

### Configure elasticsearch
- Get elastic-service external IP, in this case it is 133.212.253.221
```shell
$ kubectl get service
NAME              CLUSTER-IP     EXTERNAL-IP       PORT(S)          AGE
elastic-service   10.11.252.26   133.212.253.221   9200:32747/TCP   2m
kubernetes        10.11.240.1    <none>            443/TCP          1d
```
- Get elastic-service pod name (that you just deployed it)
```shell
$ kubectl get pods
NAME                               READY     STATUS    RESTARTS   AGE
elastic-service-1897604083-ggwk0   1/1       Running   0          3m
```
- SSH to pod by pod name above
```shell
kubectl exec -it elastic-service-1897604083-ggwk0 bash
```
- Edit elasticsearch.yml file and change **network.host** to your external IP then un-comment **http.port** and **network.host**
```shell
nano /etc/elasticsearch/elasticsearch.yml
```
- Restart elasticsearch
```shell
sudo service elasticsearch restart
```
- Check if service run successfully in both localhost and external IP
```shell
curl -X GET 'http://localhost:9200'
curl -X GET 'http://133.212.253.221:9200'
```

Note
-------------
- Remember that elastic does **NOT** support authentication connection to your deployed server
- You can edit the Dockerfile in anyway you like **BUT** do **NOT** remove this line **ENV vm.max_map_count 262144** in Dockerfile or else the deployment process will be failed

License 
-------------
- [elastic Open Source license](https://www.elastic.co/subscriptions)