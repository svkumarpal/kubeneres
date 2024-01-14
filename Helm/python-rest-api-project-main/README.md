## 1. Build Docker image 
```commandline
docker build -t python-project .
```
## 2. Build and tag according to docker hub
```commandline
docker tag python-project svkumarpal/python-rest-api-project-main:python-project
```
## 3. Docker push
```Commandline
docker push svkumarpal/python-rest-api-project-main:python-project
```
## 4. Docker pull.
```Commandline
docker pull svkumarpal/python-rest-api-project-main:python-project
```
## 5. Run Docker image
```commandline
docker run -p 9001:9001 python-project
```

## Note: Please login on docker hub, Before executing 2nd step
```commandline
docker login -u <username>
```

## Steps to configure and install new created python project through helm.
1- Create helm
```
$ helm create python-flask-rest-api-project     #<it could be any name you like>
$ cd python-flask-rest-api-project
```
## A. Change value in Chart.yaml
```
$ vim Chart.yaml                          #search appVersion and comment that line like below then save & exit from file
# appVersion: "1.16.0"
```
## Modify variable in value.yaml file.
```
$ cat values.yaml |egrep -A 2 "service:|image:"
image:
  repository: svkumarpal/python-rest-api-project-main:python-project #
  pullPolicy: IfNotPresent
------------------------------------------------------
service:
  type: NodePort
  port: 9001
```

```commandline
$ vim values.yaml                       # search repository: under image and put image path with tag like below.
image:
  repository: svkumarpal/python-rest-api-project-main:python-project

Here: 
    git path is: svkumarpal/python-rest-api-project-main
    tag is: python-project

# Now search type: & port: under service: in value.yaml file and change type and port value as per requirement

service:
  type: NodePort        {Service.Type: ClusterIP, NodePort, LoadBalancer, ...etc.}
  port: 9001            { Ports depent as per your application need }
$ save and exit from value.yaml file.
```

## Worke with templates/deployment.yaml
```
vim template/deployment.yaml        # search image: will get below value
image: "{{ .Values.image.repository }}"

Remove this : :{{ .Values.image.tag | default .Chart.AppVersion }}

Final value should be like below:
image: "{{ .Values.image.repository }}

Do Save and exit
```
## Now do app deployment through helm
```
$ helm install mypython python-flask-rest-api-project
NAME: mypython
LAST DEPLOYED: Sat Jan 13 01:10:09 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services mypython-python-flask-rest-api-project)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
  
$ helm list -a
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                                  APP VERSION
myproject       default         1               2024-01-13 22:39:05.828474587 +0530 IST deployed        python-flask-rest-api-project-0.1.0
```



