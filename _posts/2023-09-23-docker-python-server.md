---
layout: post
title: 도커 파이썬 서버 이미지 생성 및 컨테이너 실행 - Dokcer Python server image and container generating
tags: [docker, python]
categories: docker
---

# 도커를 활용하여 파이썬 서버를 구축해보기

### 1. Dokcer 설치 및 이미지 준비 (windows)
---
* 도커 설치  [Download](https://www.docker.com/products/docker-desktop/)
* 도커에서 공식적으로 알려주는 가이드를 참고하여 수행
[Guide](https://docs.docker.com/language/python/build-images/)  

* docker에서 제공해주는 기본 python-docker 프로젝트를 clone

```
git clone https://github.com/docker/python-docker
```

* clone한 프로젝트에는 flask를 이용한 서버가 실행되도록 되어있음

```python
# app.py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Docker!'
```

* requirements.txt에 필요한 패키지들이 나열되어 있음  

```python
# requirements.txt
blinker==1.6.2
click==8.1.6
colorama==0.4.6
Flask==2.3.2
itsdangerous==2.1.2
Jinja2==3.1.2
MarkupSafe==2.1.3
Werkzeug==2.3.6
```
<br>
### 2. Python 가상환경 실행(windows) 및 Python 동작 확인
---
* powershell 등을 통해 위에서 clone 한 프로젝트 디렉토리로 이동

```python 
python -m venv venv 
#'venv'이름의 파이썬 가상환경 생성, 명령 실행 시 .venv내에 파일들이 자동으로 생성됨

# .venv\Script 디렉토리로 이동

./activate 실행
```

![python venv activate](/assets/img/posting-img/image.png)

위와 같이 (.venv) 가 좌측에 생성되면 가상환경으로 실행됨을 의미  
activate가 정상적으로 안되는 경우(windows) powershell 의 설정 변경이 필요  
관련 내용은 아래 링크 참고  

[윈도우 powershell에서 가상환경 실행](https://dreamlog.tistory.com/603)

* 파이썬 가상 환경에 미리 정의된 패키지 설치

```
python -m pip install -r requirements.txt
```
![패키지 설치 과정](/assets/img/posting-img/image-1.png)

* 패키지 정상적으로 설치되었는지 확인
![정상 설치 확인](/assets/img/posting-img/image-2.png)

* 서버 정상 동작 확인  
브라우저에서 127.0.0.1:5000으로 접속하여 Hello, Docker!가 출력되는지 확인

<br><br>  

### 3. 도커 이미지 생성 및 컨테이너 실행
---
* docker init 수행
    * docker init 수행 시 .dockerignore, Dockerfile, compose.yaml 생성됨
    * Dockfile 기본 설정 관련 질의가 실행됨 (아래와 같이 설정, 파이썬 버전은 다를 수 있음)
    ![docker init question](/assets/img/posting-img/image-3.png)
<br>
<br>
* docker build --tag python-docker . 수행
    * 도커 이미지를 생성하는 것임 --tag로 이름을 지정
    * Dockfile에 정의되어 있는 순서로 명령어가 실행되는 것임
    * 파이썬 패키지도 자동으로 설치됨
<br>
<br>
* 위 과정을 수행하면 docker desktop 프로그램에서 image 확인 가능  
또는  docker images 명령어로 확인 가능
![docker image list](/assets/img/posting-img/image-4.png)
<br><br>
* docker run -p 8000:5000 python-docker 으로 컨테이너 실행
    * -p 또는 --publish는 컨테이너의 port를 publish한다는 의미임
    * 8000:5000의 의미는 host os 8000 port를 docker 컨테이너 5000 port에 binding한다는 개념
    * 컨테이너 실행후 host os 브라우저에서 http://127.0.0.1:8000 접속하면 "Hello, Docker!" 메시지 확인 가능
    * docker desktop에서 Container 탭에서 현재 실행중인 컨테이너 확인 가능
    * 또는 docker ps로 현재 실행중인 컨테이너 확인 가능
    * 이후 docker stop, kill, rm 등의 명령어로 Container를 제어할 수 있음
<br><br>