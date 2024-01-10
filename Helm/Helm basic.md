# Basic Helm CLI Command
# helm create:
helm create helloword

# helm install:
helm install myhelloworldrelease helloword
helm list -a

# Helm Upgrade Command:
$ vim helloword/values.yaml   # make some value change, Here I changed replicaset value from 1 - 2
$ helm upgrade myhellorelease helloword

# helm Rollback || go back to perticular revision version
helm rollback myhelloworldrelease 1 # here 1 is revision version

# helm -debug --dry-run || validate your helm chart before install
helm install myhelloworld --debug --dry-run helloword #note: should not install

# helm template || validate yaml template under your chart
helm template helloword

# helm lint
helm lint

# helm uninstall
helm uninstall 