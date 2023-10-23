# Docker 개념
+ ### docker hub = appstore
+ ### image = program
+ ### container = process

### pull: docker hub에서 image 다운
### run: image 실행 => container 

## image 설치
```zsh
$ docker pull [imageName]
```
## image 실행
```zsh
$ docker run [OPTIONS] IMAGE [COMMAND] [ARGs...]
$ docker run --name [NAME] IMAGE 
```
## container 목록
```zsh
$ docker ps
$ docker ps -a 
```
## container stop
```zsh
$ docker stop containerName
```
## container start
```zsh
$ docker start containerName
```
## log 확인
```zsh
$ docker logs containerName
$ docker logs -f containerName
```

## container 삭제
```zsh
$ docker rm containerName
```
## image 삭제
```zsh
$ docker rmi imageName
```



## 특정 포트로 container 실행
```zsh
$ docker run --name ContainerName -p 8080:80 httpd
```


## container에서 명령어 실행
```zsh
$ docker exec containerName 명령어
```
## docker container 쉘 연결
```zsh
$ docker exec -it containerName /bin/sh
```


## host / container 파일 바인딩
```zsh
$ docker run -p 8888:80 -v hostDir:containerDir httpd
```