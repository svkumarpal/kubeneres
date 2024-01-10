# Installing the Kubernetes Dashboard
# Deploying the Kubernetes Dashboard is not a tough job, you’d have to follow the step-by-step procedure given below. We need to apply the recommended deployment descriptor file:

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
If everything was right, all the required resources are created and we should be able to see the below output:

Next step is to check all the resources by the below command
$ kubectl get all -n kubernetes-dashboard

Kubernetes Dashboard Overview
The web-based Kubernetes console, which is a general-purpose UI made to look after the Cluster in Kubernetes. Surprisingly, even the dashboard is nothing but a container that access the cluster inform from within the cluster. So, it’s just another resource for Kubernetes to manage.

It is used for deploying containerized applications as well as for general cluster resource management. As mentioned before it is very useful to have a visual representation of our cluster in a user-friendly interface.

Installing the Kubernetes Dashboard
Deploying the Kubernetes Dashboard is not a tough job, you’d have to follow the step-by-step procedure given below. We need to apply the recommended deployment descriptor file:

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml 
If everything was right, all the required resources are created and we should be able to see the below output:

# Next step is to check all the resources by the below command
$ kubectl get all -n kubernetes-dashboard

# Accessing the Kubernetes Dashboard
We can access the Kubernetes dashboard in the following ways:

kubectl port-forward (only from kubectl machine)
kubectl proxy (only from kubectl machine)
Kubernetes Service (NodePort/ClusterIp/LoadBalancer)
Ingress Controller (Layer 7)

Once the Kubernetes dashboard deployment is completed you can access the Kubernetes dashboard by following the next set of instructions.
This will be a proxy server between your machine and K8s API server. Since the service type is ClusterIp by default, we can’t access the dashboard outside the cluster as it is not exposed. But if we want to access the dashboard from outside the cluster we have to change the type of the service to either NodePort or Loadbalancer.

We will have to change the type of service from ClusterIp to NodePort. So, give the following command to edit the service and make the following changes:

$ kubectl edit service/kubernetes-dashboard -n kubernetes-dashboard

selector:
    k8s-app: kubernetes-dashboard
  sessionAffinity: None
  type: NodePort # (changed from ClusterIP to NodeIP) 
status:
  loadBalancer: {}
# save and exit

$ vim dashboard-adminuser.yaml 

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

$ kubectl apply -f dashboard-adminuser.yaml

After successful creation of the service account, perform the below set of commands:

$ kubectl get sa -n kubernetes-dashboard

$ kubectl describe sa kubernetes-dashboard -n kubernetes-dashboard

$ kubectl get svc -n kubernetes-dashboard

# Get token for user and copy it.
kubectl -n kubernetes-dashboard create token admin-user

# create secret
$vim admin-user-secret.yaml
<pr>apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"   
type: kubernetes.io/service-account-token</pr>
$ kubectl create -f admin-user-secret.yaml

After Secret is created, we can execute the following command to get the token which saved in the Secret:

$ kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d.

# Accessing Dashboard
Now, to view the dashboard in the browser, navigate to the following address in the browser:
<pr>https://<NodeIp>:<dashboard port> #port will get through kubect get svc -n kubernetes-dashboard
</pr>
<pr>https://k8sm.svkp.lab:<NodePort Port></pr>
https://k8sm.svkp.lab:32504