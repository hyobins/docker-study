# docker-study

<img src="./docker.png" alt="Docker" height="300"></img><br/>

> ## 개요 
Docker는 컨테이너 기반 가상화 도구이다.  <br><br>


> ## Docker Architecture
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile24.uf.tistory.com%2Fimage%2F224FC43F58DDFF4C14BDE0"><br>

- Client : 도커 Container를 관리하고 실행하기 위해서 Deamon과 상호작용하는 Binary 파일이다.

- Daemon : Host에 설치되어 도커 Container를 관리하는 daemon 
프로세스이다. Client와 상호작용한다.

- Registry : 도커 Image가 저장되어 있는 장소이다. 대표적으로 Docker Hub가 있다. Registry는 Public Registry와 Private Registry가 있다.

- Image : 도커 Daemon을 통해 Container로 실행 가능하도록 필요한 프로그램, 라이브러리, 소스 등이 설치된 파일이다.

<br><br>

>## Docker Hub, Git 등에서 (base)이미지 받기

1. 도커 레지스트리에서 이미지를 pull 받아서 로컬로 다운로드
2. 이 이미지를 통해 컨테이너 실행

```
//pull받기 
docker pull centos

//컨테이너 실행
docker run -it <이미지이름:버전> <명령어>
docker run -it centos:latest bash
```
<br><br>
>## 직접 이미지 빌드하기

>1. Dockerfile 작성(예시)

```bash
FROM ubuntu:16.04

RUN apt-get update && apt-get install -y vim apache2

COPY index.html /var/www/html/

CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
```

>2. Dockerfile을 이미지로 제작하기위해 Docker Build 진행

```bash
docker build <옵션> <Dockerfile (있는)위치>

//해당 옵션 중 "-t <생성 할 이미지명>:<태그명> <Dockerfile 위치>" 사용
docker build -t golang/docker/dev:latest .
```

<br><br>
>## 기본적인 도커 명령어

```bash
#실행중인 컨테이너 확인 
$ docker ps

#실행중+종료된 컨테이너 확인
$ docker ps -a

#이미지 확인
$ docker images

#모든 docker이미지 삭제
$ docker rmi $(docker images -q) 

#실행된 프로세스와 터미널 상에서 입출력 주고받기위해 
$ docker attach d3fef9c0f9e9
[root@d3fef9c0f9e9 /]#

#종료
[root@d3fef9c0f9e9 /]# exit

#종료된 모든 컨테이너를 삭제
$ docker rm $(docker ps -a -q)
```

<br><br>

>## Dockerfile
이미지를 직접 빌드하는 스크립트가 기재된 파일로서, 다음과 같은 내용을 담고있다.<br>
1. 베이스 이미지의 repository
2. 설치할 패키지
3. 소스코드와 설정파일
4. 컨테이너 기동 시 실행될 명령어

<br>

### Dockerfile 작성
Exmaple

```docker
FROM golang:alpine as builder

WORKDIR /workspace/test
COPY . .

RUN go get -d -v
RUN go build -o test1 test1.go

ENTRYPOINT ["/workspace/test/test1"]
CMD ["/workspace/test/test1"]
```

FROM  : golang 이미지를 가져와서,

WORKDIR : 내 workspace로 이동 후 

COPY : 이 위치에 copy 해주고

RUN : (이 위치에서) test1.go build를 통해 test1 이란 이름의 exe 바이너리 파일 생성

ENTRYPOINT : 컨테이너 실행과 동시에 명령어 실행

<br>

## Dockerfile build 후 실행

```bash
$ docker build -t d3 .
$ docker run -it -p8888:1322 --name test01 d3 /bin/sh
```
<br><br>

위 내용을 shell script 이용하여 편리하게 실행할 수도 있다.<br>
>run_my_docker.sh파일 작성

```bash
docker stop hb-container
docker rm hb-container
docker build -t hb/image .
docker run -it -p:8888:{주소} --name hb-container hb/image /bin/sh
```

>실행

```bash
sh run_my_docker.sh
```