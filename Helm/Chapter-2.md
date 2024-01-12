# Writing your first Helm Chart for "Hello World"
Now after you have done your Helm Chart installation, we can write our first "Hello World" Helm Chart.

# 2.1: Create your first Helm Chart
We are going to create our first helloworld Helm Chart using the following command
```
$ helm create helloworld
```
It should create a directory helloworld, you can verify it by using the following ls -lart command
```
$ ls -lart | grep helloworld
drwxr-xr-x   4 ansadm ansadm    93 Jan 11 23:09 helloworld
```
To verify the complete directory structure of the HelmChart please do run the command

```
$ tree helloworld
helloworld
├── charts
├── Chart.yaml
├── templates
│  ├── deployment.yaml
│  ├── _helpers.tpl
│  ├── hpa.yaml
│  ├── ingress.yaml
│  ├── NOTES.txt
│  ├── serviceaccount.yaml
│  ├── service.yaml
│  └── tests
│       └── test-connection.yaml
└── values.yaml
```
Great now you created your first Helm Chart - helloworld.

In the next steps we are going to run the helloworld Helm Chart.

Update the service.type from ClusterIP to NodePort inside the values.yml
Before you run your helloworld Helm Chart we need to update the service.type from ClusterIP to NodePort.

The reason for this change is - After installing/running the helloworld Helm Chart we should be able to access the service outside of the kubernetes cluster. And if you do not change the service.type then you will only be able to access the service withing kubernetes cluster.

To update the values.yml, first go inside the directory helloworld
```
$ cd helloworld
2.2.1: Open values.yml in vi
```
After that open the `values.yml` in `vi`
vi values.yaml 

2.2.2: Update service.type from ClusterIP to NodePort
Look for the service.type block and update its value to NodePort

service:
  type: NodePort
  port: 80
YAML

2.3: Install the Helm Chart using command - helm install
Now after updating the values.yml, you can install the Helm Chart.

Note : The helm install command take two arguments -

First argument - Release name that you pick
Second argument - Chart you want to install

It should look like -
```
$ helm install <FIRST_ARGUMENT_RELEASE_NAME> <SECOND_ARGUMENT_CHART_NAME>
```
Your final command would be
```
$ helm install myhelloworld helloworld
NAME: myhelloworld
LAST DEPLOYED: Thu Jan 11 23:23:51 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myhelloworld)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```
# 2.4: Verify the helm install command
Now you need to verify your helm release .i.e. myhelloworld and which can be done by running the helm list command.
```
$ helm list -a
```
It should return you withe release name which you have just installed .i.e. myhelloworld
NAME       	   NAMESPACE	REVISION	UPDATED                                 STATUS  	   CHART          APP VERSION
myhelloworld	default  	1       	2020-11-07 21:48:08.8550677 +0000 UTC	deployed	helloworld-0.1.0	1.16.0

2.5: Get kubernetes Service details and port.
Lets run the kubectl get service command to get the NodePort.

```
$ kubectl get service
NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes               ClusterIP   10.233.0.1      <none>        443/TCP        14d
myhellworld-helloworld   NodePort    10.233.14.134   <none>        80:30738/TCP   7m10s
```
Note: Keep in mind the NodePort number can vary in the range 30000-32767, so you might get different NodePort.
Since my cluster ip is 100.0.0.2 and NodePort is 30738, so I can access my Nginx page of my myhelloworld Helm Chart

```
$ kubectl get svc
NAME           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.96.0.1      <none>        443/TCP        51d
myhelloworld   NodePort    10.106.35.74   <none>        80:31154/TCP   17m
```
```
$ helm uninstall myhelloworld
release "myhelloworld" uninstalled
```