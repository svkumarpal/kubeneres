<b> Vinod Kumar K8S Repo </b><br>
This is my first Kubernetes pod creation yaml
<br>
Author : Vinod Kumar Pal (Kubernetes)
<br>
Contact: svkumarpal@gmail.com
Written Kubernetes Networking pod creation
```
kubectl --as=system:serviceaccount:default:read-only-user describe pod -n kube-system calico-node-tzkss
kubectl auth can-i get pods --all-namespaces / -A
kubectl --as=system:serviceaccount:default:read-only-user auth can-i get pods -A / --all-namespaces
kubectl api-resources
kubectl api-versions
kubectl get --raw /
kubectl get --raw /api/v1/namespaces
kubectl get --raw /apis/batch/v1
kubectl get --raw /api/v1/namespaces/default
kubectl get -v=9 --raw /api/v1/namespaces/default | python3 -m json.tool
kubectl proxy --port=8080 &
curl http://127.0.0.1:8080/apis | less
curl http://127.0.0.1:8080/apis/apps/v1/namespaces/default/deployments
```
```
kubectl create deployment label-nginx-example --image=nginx --dry-run=client -o yaml > label-nginx-example.yml
```


## Method1 - Assign labels while creating a new object. 
```
kubectl create deployment label-nginx-example --image=nginx --dry-run=client -o yaml > label-nginx-example.yml
kubectl create -f label-nginx-example.yml
kubectl get deployments --show-labels
kubectl get pods --show-labels
```
## Method2 - Assign a new label to existing pod runtime as a pod (Patch the label on running pods)
```
$cat update-label.yml
spec:
  template:
    metadata:
      labels:
        tier: frontend
```
```
kubectl patch deployment label-nginx-example --patch "$(cat update-label.yml)"
kubectl describe deployment label-nginx-example
kubectl get pods --show-labels
```

## Method3: Assign a new label to existing deployment using runtime kubectl command
```
kubectl label deployment nginx-deployment tier=backend
kubectl get deployments --show-labels
```
## Using labels to list resource objects
```
# kubectl get pods --show-labels
# kubectl get deployments --show-labels
# kubectl get all --show-labels
# kubectl get deployments -l type=dev
# kubectl get pods -l app=prod
```
Using selector to list respource objects
```
[root@k8sm ~]$ cat lab-nginx.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dev
    tier: backend
  name: lab-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dev
  template:
    metadata:
      labels:
        app: dev
    spec:
      containers:
      - image: nginx
        name: nginx
```
```
# kubectl create -f lab-nginx.yml
# kubectl get pods --show-labels
# kubectl get deployments --show-labels
# kubectl get pods --selector "app=dev"
# kubectl get pods --selector "tier=backend"
# kubectl get deployments --selector "app=prod"
# kubectl get deployments --selector "app in (prod, dev)"
```

## Removing Labels:
```
# kubectl label <resource-type> <resource> <label-key>-		(Syntex)
# kubectl label pod lab-nginx-78bfcbc5d8-6sq97 app-
# kubectl get pods --show-labels
# kubectl get deployments --show-labels
# kubectl label deployment lab-nginx app-
# kubectl get deployments --show-labels
```

## How to add or remove label from node in kubernetes
1- Add label to a node:
Syntex: kubectl label nodes <your-node-name> <label>
```
Eg: $ kubectl label nodes k8sw2.svkp.lab color=blue
$ kubectl get nodes --show-labels|grep color
```
## Deploy pod on specific node:
```
[root@k8sm ~]$ vim nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    ## This label is applied to the Deployment
    type: dev
  name: nginx-deploy
spec:
  replicas: 1
  selector:
     matchLabels:
        ## This label is used to match the Pod to create replicas
        type: dev
  template:
    metadata:
      labels:
        ## This label is applied to the Pod
        type: dev
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
      nodeSelector:
        ## This label is used to deploy the pod on matching nodes
        color: blue
```
```
$ kubectl create -f nginx-deploy.yaml
$ kubectl get pods -o wide
```
## Remove label from node:
```
# kubectl label node <nodename> <labelname>-
# kubectl label node k8sw2.svkp.lab color-
# kubectl get nodes --show-labels | grep color
# kubectl delete po nginx-deploy-55f6784944-9jwr
# kubectl label node k8sw4.svkp.lab color=blue
```
## How to add label to running pod in kubernetes:
Method-1 Using kubectl label Command.  
Syntax to apply label to running pod
```
# kubectl label pods <your-pod-name> <label-name>
```
Example-1: Apply Single label to running pod
```
# kubectl label pods test-pod-rsyslog app=prod
```
Example-2: Apply multiple labels to running pods
```
kubectl label pod <pod-name> {key1=value1,key2=value2,key3=value3}
kubectl label pods test-pod-rsyslog -n vinod {color=blue,env=prod1}
kubectl get pod test-pod-rsyslog -n vinod --show-labels
```
Example-3: Overwrite any existing labels
```
kubectl label pods test-pod-rsyslog -n vinod app=dev (It will through error)
kubectl label pods test-pod-rsyslog -n Vinod app=dev --overwrite (this command with --overwrite switch will overwrite)
```
Method-2, Using kubectl edit command
```
$ kubectl edit <resource-name> <obj-name>
```
Example: Add or remove label from running command.
create a simple deployment
```
$ cat nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    ## This label is applied to the deployment
    type: dev
  name: nginx-deploy-new
spec:
  replicas: 2
  selector:
     matchLabels:
        ## Search for this label in the pod to map it to the deployment
        app: webserver
  template:
    metadata:
      labels:
        ## Apply this label to the pod
        app: webserver
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```
```
kubectl edit pod nginx-deploy-5cc69bf9c4-6dsjx
kubectl edit pod nginx-deploy-5cc69bf9c4-6dsjx
```
How to delete label for kuberneted pods
```
kubectl label pods <your-pod-name> <label-name>-
```
Create a daemonset:
```
kubectl api-resources | grep -iE 'KIND|daemonse'
NAME                              SHORTNAMES   APIGROUP                       NAMESPACED   KIND
daemonsets                        ds           apps                           true         DaemonSet
```
```
$ kubectl explain DaemonSet | head -n 3
KIND:     DaemonSet
VERSION:  apps/v1
```
```
$ cat fluentd-daemonset.yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  labels:
    app: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v0.14.10
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```
Create DaemonSet using node selector
```
#vim nginx-daemonset.yml
apiVersion: apps/v1
kind: "DaemonSet"
metadata:
  labels:
    app: nginx
    ssd: "true"
  name: nginx-fast-storage
spec:
  selector:
    matchLabels:
      ssd: "true"
  template:
    metadata:
      labels:
        app: nginx
        ssd: "true"
    spec:
      nodeSelector:
        ssd: "true"
      containers:
        - name: nginx
          image: nginx:1.10.0
```
```
kubectl create -f nginx-daemonset.yml
kubectl get ds		  
kubectl get nodes --show-labels
kubectl label nodes k8sw3.svkp.lab ssd=true
kubectl get ds
kubectl get pods -o wide
```
Removing the required label from the node
```
kubectl label node <nodename> <labelname>-
kubectl label nodes k8sw3.svkp.lab ssd-
kubectl get ds
kubectl get pods -o wide
```
Deleteting Daemoinset
```
kubectl get ds
kubectl delete ds fluentd nginx-fast-storage
```
Get Details of CronJob in kubernetes
```
kubectl api-resources | grep -iE 'KIND|cron'
kubectl explain cronjob.spec
kubectl explain cronjob.spec.schedule
```
Create CronJob in kubernetes
```
$ cat pod-cronjob.yml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: pod-cronjob
spec:
  schedule: "*/2 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: pod-cronjob
            image: busybox
            args:
            - /bin/sh
            - -c
            -  date; echo hello from k8s cluster
          restartPolicy: OnFailure
```
```
kubectl create -f pod-cronjob.yml
kubectl get cronjobs.batch
kubectl get jobs --watch
kubectl get pods -o wide
kubectl get jobs --watch
kubectl get pods
```

## Delete Kubernetes Cron Job
```
$ kubectl delete cronjobs.batch <cron_job_name>
$ kubectl delete cronjobs.batch pod-cronjob
cronjob.batch "pod-cronjob" deleted
```
## NoddeAffinity Pre-requisites
Add label on nodes
```
kubectl label nodes k8sw3.svkp.lab tier=gold
kubectl label nodes k8sw4.svkp.lab tier=silver
```
## Create a deployment with NodeAffinity
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: tier
                operator: In
                values:
                - gold
      containers:
      - image: nginx
        name: nginx
```
```
kubectl create -f nodeaffinity.yaml
kubectl get pod -o wide
kubectl get node k8sw3.svkp.lab -L tier
kubectl scale --current-replicas=1 --replicas=4 -f nodeaffinity.yaml
kubectl get pod
kubectl scale --current-replicas=4 --replicas=1 -f nodeaffinity.yaml
```

```
Node Antiaffinity action:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: tier
                operator: NotIn
                values:
                - gold
```

## Kubernetes Node affinity weight in Action
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: tier
                operator: In
                values:
                - gold
                - silver
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 10
            preference:
              matchExpressions:
              - key: tier
                operator: In
                values:
                - gold
          - weight: 50
            preference:
              matchExpressions:
              - key: tier
                operator: In
                values:
                - silver
      containers:
      - image: nginx
        name: nginx
```

## Misslenious Commands
```
kubectl get deployments.v1.apps -o yaml
```
Kubemnetes fort forwardings
Syntax:
```
kubectl port-forward <resource-type/resource-name> [local_port]:<pod_port>
```
examples:
```
kubectl port-forward pods/my-pod 8080:80
kubectl port-forward deployment/my-deployment 8080:80
kubectl port-forward replicaset/my-replicaset 8080:80
kubectl port-forward service/my-service 8080:80

Kubemnetes fort forwardings in backend
kubectl port-forward pods/my-pod 8080:80 &
[root@k8sm ~]$ kubectl port-forward --address 0.0.0.0 pod/nginx 8080:80
```
