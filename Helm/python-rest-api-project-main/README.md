## 1. Build Docker image 
```commandline
docker build -t python-rest-api .
```

## 2. Run Docker image
```commandline
docker run -p 9001:9001 python-rest-api
```
## 3. Docker push
```Commandline
docker push svkumarpal/python-rest-api-project-main
```
## 4. Docker pull
```Commandline
docker pull svkumarpal/python-rest-api-project-main
```