# 1. Install Helm Chart
Installing the Helm Chart pretty easy but there is a pre-requisite of setting up Kubernetes Cluster.

Install Helm Chart Using Script
If you like doing everything from scratch then I would suggest you to install the Helm Chart Using script.

Run the following scripts -

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```
# Install Helm Chart Using Binary
The other option would be to download the complete binary and do the installation be yourself
```
Step 1 : Download the Binary https://github.com/helm/helm/releases
```

Step 2 : Extract the binary using the command
```
$ tar -zxvf helm-vxxx-xxxx-xxxx.tar.gz
```
Step 3 : Move it to
```
$ mv linux-amd64/helm /usr/local/bin/helm or /usr/bin
```
Verify the Installation - You can verify the installation by running the following command
```
$ helm version
```
