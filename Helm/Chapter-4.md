# Chapter - 4 (Steps to create our own custum chart)
```
1- Python Application with REST API
2- Create Docker Container
3- Tag, Push, pull & run Docker image to Docker repository
4- Create HelmChart for python REST API app
5- Install the Helm chart
6- Verify the Helm Chart after installation
```
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

## Note: Before execute 2nd steps Plese login on docker hub
```commandline
docker login -u <username>
```

