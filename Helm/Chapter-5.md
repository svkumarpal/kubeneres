## Chapter - 5 (Helm File)
1. What is Helmfile and why we need it
2. Installing Helm file
3. Manage your Helmchart using Helmfile
4. Install Helmchart from remote Fit Repo using helmfile
5. Install Multiple HelmChart
---------------------------------------------

Helmfile is an another wrapper working on top of Helm Chart. Just like Helm Chart, Helmfile also uses the YAML for writing the configurations.

But why do you need Helmfile if you are working with Kubernetes and Helm Chart?

Here are some key benfits of using Helmfile -

1. You can bundle several Helm Charts into a Single Helmfile to manage your kubernetes eco system.
2. Helmfile helps you to keep isolation between the different environments(developemnt, staging, production).
3. It can help you to identify the differences between the new changes which you want to apply against the existing running deployment inside kubernetes cluster
4. Helmfile uses the Go Templates which lets you templatify your Helmfile and also you can use Sprig Library with functions - requiredEnv, exec, readFile, toYaml, fromYaml, setValueAtPath, get, tpl, required, fetchSecretValue, expandSecretRefs
5. With the help of HelmFile you can deploy multi-tier applications inside kubernetes cluster.

## 1. How to Install Helmfile and running docker container of helmfile?
(Note :- But there is a prerequisite before installing the Helmfile, you must install the helmchart)

1. Download the Helmfile from https://github.com/roboll/helmfile/releases 
2. Rename the file from - helmfile_linux_amd64 to helmfile
```
mv helmfile_linux_amd64 helmfile
```
3. Change the permission of the file and make it executable
```
chmod 755 helmfile
```
4. Move the file from current location to /usr/local/bin
```
mv helmfile /usr/local/bin
```
5. After moving the file verify the Helmfile installation by running the command - 
```
$ helmfile --version
```
## 2. Create your first Helmfile and install Helmchart
As we know Helmfile is just a wrapper on top of HelmChart, so after installing the Helmfile first we need to create a HelmChart

1. Create a helloworld helmchart.
```
$ helm create hellworld
```

2. Let's create a Helmfile for the helmchart we have just created and we are going to create helmfile.yaml at the same location where we have crated helloworld helmchart.

Here are the contents of helmfile.yaml-  
```
---
releases:

  - name: helloworld
    chart: ./helloworld
    installed: true
```

installed: true - If you want to install the helmchart using helmfile then your must set this flag to true

3. After creating the helloworld helmchart and its respective helmfile let's try to install the helm chart by running the command 
```
$ helmfile sync
```
After running the above command you should see the following output -

```
$ helmfile sync
Building dependency release=helloworld, chart=helloworld
Affected releases are:
  helloworld (helloworld) UPDATED

Upgrading release=helloworld, chart=helloworld
Release "helloworld" does not exist. Installing it now.
NAME: helloworld
LAST DEPLOYED: Sun Oct 17 19:53:41 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=helloworld,app.kubernetes.io/instance=helloworld" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT

Listing releases matching ^helloworld$
helloworld	default  	1       	2021-10-17 19:53:41.44402394 +0000 UTC	deployed	helloworld-0.1.0	1.16.0

UPDATED RELEASES:
NAME         CHART        VERSION
helloworld   helloworld     0.1.0 
```
4. Verify the installation by running the command $ helm list -a
```
helm list -a
NAME      	NAMESPACE	REVISION	UPDATED                               	STATUS  	CHART           	APP VERSION
helloworld	default  	1       	2021-10-17 19:53:41.44402394 +0000 UTC	deployed	helloworld-0.1.0	1.16.0 
```
## 3. Uninstall the helmchart using Helmfile
The uninstallation part is very simple when you are using the Helmfile. You simply need to set the flag installed: false.
```
---
releases:

  - name: helloworld
    chart: ./helloworld
    installed: false
```
After updating the helmfile you need to run the $ helmfile sync command again -
```
$ helmfile sync
```
## 4. Use GitHub repository for installing helm chart using Helmfile
The one more feature of Helmfile which I like the most is - you can even install the helmchart from remote repository such as 
GitHub: https://github.com/
Google Container Repository: https://cloud.google.com/artifact-registry
AWS ECR : https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html

Here is my GitHub repository for my helmchart: 

Add the following configuration attributes for working with remote repository -  

1. Repository name - helloworld
2. Repository URL - git+https://github.com/rahulwagh/helmchart@helloworld?ref=master&sparse=0
```
---
repositories:
  - name: helloworld
    url: git+https://github.com/rahulwagh/helmchart@helloworld?ref=master&sparse=0

releases:

  - name: helloworld
    chart: helloworld/helloworld
    installed: false
```
Now you can run the $ helmfile sync command for installing the helm chart from remote repository

## 5. Deploy multiple Helmcharts using Helmfile.
Now we have seen in the previous steps how to deploy a local Helmchart as well as helmchart deployment from repository such as GitHub. In this section we will deploy multiple helmcharts using a Helmfile.

Use the following steps -
1. Create your first helm chart -
```
helm create helloworld1
```
2. Create your second helm chart
```
helm create helloworld2
```
3. Create a Helmfile for deploying multiple helmchart .i.e. helloworld1, helloworld2
```
---
releases:
  - name: helloworld1
    chart: ./helloworld1
    installed: true

  - name: helloworld2
    chart: ./helloworld2
    installed: true
```
4. Install the helmchart using Helmfile.
```
helmfile sync
```
5. After the successful deployment you should see following message onto your console.
```
$ helmfile sync
Building dependency release=helloworld1, chart=helloworld1
Building dependency release=helloworld2, chart=helloworld2
Affected releases are:
  helloworld1 (helloworld1) UPDATED
  helloworld2 (helloworld2) UPDATED

Upgrading release=helloworld1, chart=helloworld1
Upgrading release=helloworld2, chart=helloworld2
Release "helloworld2" does not exist. Installing it now.
NAME: helloworld2
LAST DEPLOYED: Mon Oct 18 21:05:18 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
```
The same approach can be taken to deploy the multiple helmchart from GitHub repository also. You need to push all of your helmchart under single GitHub repository and add multiple number of releases configurations inside your helmfile. Here is an example of helmfile.yaml -

```
 ---
 repositories:
     - name: helloworld
       url: git+https://github.com/rahulwagh/helmchart@helloworld?ref=master&sparse=0
   
 releases:
   - name: helloworld1
     chart: ./helloworld1
     installed: true

   - name: helloworld2
     chart: ./helloworld2
     installed: true
```
## 6. How to use Template and Environment variables insides Helmfile?
There is a really good support for template expressions as well as templated helm values. Inside the helmfile.yaml you can use values.yaml.gotmpl and it can explained simply by -

1. .gotmpl - Any value files with .gotmpl extension will be rendered with template expressions.
2. .yaml - File ending with .yaml extension will be used as plain value files
Here is example of value file template for helloworld helm chart-
```
releases:
- name: helloworld
  chart: ./helloworld
  values:
  - values.yaml.gotmpl
```
In the above helmfile.yaml the value attribute which has an extension of .gotmpl will be treated as template file. 

Let’s consider the values.yaml.gotmpl has following expression -
```
{{ readFile "values.yaml" | fromYaml | setValueAtPath "foo.bar" "FOO_BAR" | toYaml }}
```
The above expression will be evaluated as -
```
releases:
- name: helloworld
  chart: ./helloworld
  values:
  - foo:
        bar: "FOO_BAR"
```
## 6.1 How to pass Environment values into helmfile.yaml
To easout the deployment process of test, stage and production, helmfile helps you to prepare the helmfile.yaml as well as values.yaml as per the environment.

You can create multiple environments(dev, test, stage, prod) inside the helmfile but keep in mind there exists one default environment which is always present.

Here is an example in which I have defined a test only environment -

```
environments:
  default:
  test:
---

releases:
   - name: helloworld1
     chart: ./helloworld1
     installed: true
```
Let us take one more example with default and production environment -

Here is my helmfile.yaml
```
environments:
  production:
    values:
    - production.yaml
---

releases:
   - name: helloworld1
     chart: ./helloworld1
     installed: true
```
And here is my values.yaml.gotmpl
```
domain: {{ .Values | get "domain" "dev.example.com" }}
```
So if I run the $ helmfile sync command then it will use the value of domain = dev.example.com.

But let us define one more yaml named production.yaml and put following values in it -
```
domain: prod.example.com
releaseName: prod
```
Now re-run the <br>$ helm --environment production sync</br>. It will install the app with value domain = prod.example.com

## 6.2 Loading the environment values from remote.
Along with passing the local environment values from local, helmfile also supports to pass the environment values from the remote github or any other repository such GCR, ECR etc.

Here is example of passing environment values remotely -
```
environments:
  cluster-azure-us-west:
    values:
      - git::https://github.com/rahulwagh/helmchart@helloworld?ref=master&sparse=0

releases:
  - name: helloworld1
    chart: ./helloworld1
    installed: true
```
## 7.Conclusion
The helmfile is a must to have tool if you are managing your kubernetes infrastructure with HelmChart. Undoubtedly HelmChart brings in more advantages and feature when it comes to managing your kubernetes cluster but adding Helmfile will definitely help you with -

1. Managing multiple helm charts with one single helmfile
2. Isolating helmcharts as per the environment
3. Identify the difference between the changes
4. Improving your multi-tier kubernetes setup