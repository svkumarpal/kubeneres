# Helm CLI Command
1- helm create
2- helm install
3- helm upgrade
4- helm rollback
5- helm --debug --dry-run
6- helm template
7- helm lint
8- helm uninstall
===============================
1- Helm create and install
$ helm create helloworld
$ helm install  myhelloworldrelease helloworld
[ansadm@k8sm ~]$ helm list -a
NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
myhelloworldrelease     default         1               2024-01-11 23:54:57.118424937 +0530 IST deployed        helloworld-0.1.0        1.16.0

2- upgrade 
vim helloworld/values.yaml
find replica and change it from 1 - 2.

$ helm upgrade myhelloworldrelease helloworld
$ helm list -a
NAME                    NAMESPACE REVISION   UPDATED                                STATUS    CHART             APP VERSION
myhelloworldrelease     default   2          2024-01-12 00:00:00.676666262 +0530 IST deployed helloworld-0.1.0  1.16.0

3- rollback
$ helm rollback myhelloworldrelease <REVISION#>
$ helm rollback myhelloworldrelease 1
Rollback was a success! Happy Helming!

$ helm list -a
NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
myhelloworldrelease     default         3               2024-01-12 00:03:41.122797746 +0530 IST deployed        helloworld-0.1.0        1.16.0


