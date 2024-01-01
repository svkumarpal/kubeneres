# TO generate yaml file.
$ kubectl create deployment nginx-lab-1 --image=nginx --replicas=3 --dry-run=client -o yaml > nginx-lab-1.yml

# Modify Yaml file according to requirement.
$ vim nginx-lab-1.yml

# create deployment through yaml file.
$ kubectl create -f nginx-lab-1.yml

# list pods.
$ kubectl get pods

# Describe pods status.
$ kubectl describe pod nginx-lab-1-78bfcbc5d8-zb5q6

# Create service --type=NodePort and --port=80.
$ kubectl expose deployment nginx-lab-1 --type=NodePort --port=80

# List Service details.
$ kubectl get service

# Get Service details.
$ kubectl describe svc nginx-lab-1