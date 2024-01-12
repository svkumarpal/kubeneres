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

## Note: Before execute 2nd step Please login on docker hub
```commandline
docker login -u <username>
```