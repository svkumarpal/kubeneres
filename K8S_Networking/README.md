#go here for detail study
https://www.golinuxcloud.com/kubernetes-networking/
#To login container under pods
Syntax: kubectl exec -it podname -c containername -- shell
Here:
podname = sidecar-pod
containername: container1 / container2
shell= /bin/sh or /bin/bash
-c used for with containername

Example:
kubectl exec -it sidecar-pod -c container1 -- /bin/sh
kubectl exec -it sidecar-pod -c container2 -- /bin/bash

# Connectivity between containers within pods
kubectl exec -it sidecar-pod -c container1 -- /bin/sh
apt-get update
apt-get install -y netcat-openbsd
nc -l -p 8080

kubectl exec -it sidecar-pod -c container2 -- /bin/bash
apt-get update
apt-get install -y netcat-openbsd
root@sidecar-pod:/# nc 127.0.0.1 8080 -vw2
Connection to 127.0.0.1 8080 port [tcp/*] succeeded!

# Connectivity between pods
kubectl apply -f pod1.yaml
kubectl apply -f pod2.yaml

$ kubectl get pods -o wide

Now verify the connectivity from pod1 to pod2 and vice versa:
$ kubectl exec -it pod1 -- curl pod-ipaddress
--------------------
$ kubectl exec -it pod2 -- curl pod-ipaddress

