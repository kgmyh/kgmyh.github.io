---
layout: post
comments: true
title: "Linux에 nginx + gunicon 설치 및 django 프로젝트 연동"
author: "김성환"
categories: [django]
tags: [django, gunicon, wsgi, nginx, 설치]
description: 리눅스에 nginx, gunicorn 설치 및 장고프로젝트 연동하기
---

장고 프로젝트 root 경로에 의존성 문서를 만든다.
```
pip freeze > requirements.txt   
```

# 필요 모듈 설치
```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essential
sudo apt-get install python3    
sudo apt-get install python3-pip
```

# project directory 만들기
```bash
mkdir project 
cd project 
```

# git hub 장고 프로젝트 clone 받기
```
git clone url
```

# 가상환경 생성 및 라이브러리 설치 
```bash
sudo apt-get install virtualenv
virtualenv -p python3 venv
source venv/bin/activate  (활성화)

pip install -r django-board/requirements.txt
```

# Django 개발서버로 실행
- 여기 까지 하면 개발 서버로 실행 가능

```
python manage.py runser 0.0.0.0:8000
```

# nginx + gunicon(구니콘-wsgi) 설치

## gunicon 설치 

```bash
pip install gunicorn
```

- gunicon 테스트 
	- project root 경로에서(django-board/) 아래 명령어 실행

```bash
gunicorn --bind 0:8000 config.wsgi:application
```
> - --bind: 포트
> - config.wsgi:application : wsgi와 연결된 wsgi application은 config/wsgi.py 파일에 설정된 application이란 의미

- web browser에서 http://ip:8000 으로 접속 테스트 
	- wsgi는 장고 동적 부분만 실행하고 정적파일은 처리 안하기 때문에 이미지 등이 **static 요소들은 안나온다.**
## Gunicon 을 port 대신 unix socket을 이용
- Gunicorn은 nginx 와 연결할 것이므로 속도가 더빠른 소켓연동을 한다.
- 다음 명령어로 실행 

```bash
gunicorn --bind unix:/tmp/gunicorn.sock config.wsgi:application
	- bind 옵션부분이 바뀜
	- unix 소켓방식으로 실행하면 gunicon 서버를 단독서버로 실행할 수 없다. nginx와 연결해서 외부에서 호출하게 한다.
	- 여기선 되는지만 확인하자. 
```

- Gunicon을 리눅스 service로 등록 
	- 서버 시작할 때 gunicon을 자동으로 실행되도록 service로 등록한다.
	- 서비스로 등록하려면 환경변수파일과 서비스 파일을 작성해야한다.
	- 환경변수 파일 등록
		- 환경변수에 DJANGO_SETTINGS_MODULE 환경변수 등록해야함.
		- 특정 경로에 환경설정파일을 작성할 파일을 생성하고 다음을 작성한다.(ex:django-board.env)
			- ex) /home/ubuntu/project/django-board.env
		```bash
			DJANGO_SETTINGS_MODULE=config.settings
		```
	- 서비스 파일 생성 및 작성 
		- /etc/systemd/system 디렉토리에 서비스 설정파일 생성
		```bash
		cd /etc/systemd/system
		sudo vi django-board.service
		```
	- 다음 내용을 넣는다.
	
	```
	[Unit]
	Description=gunicorn daemon
	After=network.target

	[Service]
	User=ubuntu
	Group=www-data
	WorkingDirectory=[프로젝트 경로]
	ExecStart=[gunicorn 설치 경로] --workers 3 --bind 0.0.0.0:8000 [장고앱설정패키지명].wsgi:application

	[Install]
	WantedBy=multi-user.target
	```
	- 예)
	
	```
	[Unit]
	Description=gunicorn daemon
	After=network.target

	[Service]
	User=ubuntu
	Group=ubuntu
	WorkingDirectory=/home/ubuntu/project/django-board
	EnvironmentFile=/home/ubuntu/project/django-board.env
	ExecStart=/home/ubuntu/project/venv/bin/gunicorn --workers 2 --bind unix:/tmp/gunicorn.sock config.wsgi:application

	[Install]
	WantedBy=multi-user.target
	```	
	- WorkingDirectory: 장고 프로젝트 경로
	- EnvironmentFile: 환경변수 파일 경로
	- ExecStart: gunicon 실행 프로그램(윈도우라면 exe) - 가상환경/bin 아래 있다.
		- --workers : 프로세스 몇개 사용하는지.
		- ExecStart에는 gunicorn 모듈이 설치된 경로를 지정한다.
	- 서비스 등록
	```bash
sudo systemctl start django-board.service    #서비스 시작
sudo systemctl enable django-board.service   #등록 
sudo systemctl status django-board   	     #상태확인
```

# Nginx 설치 및 설정

## 설치 
```
sudo apt-get install nginx
```

## 설정 
- /etc/nginx/sites-available (Nginx 설정파일들 모아논 dir) 디렉토리로 이동
```
cd /etc/nginx/sites-available
```

- 설정파일을 생성한 뒤 아래 내용을 넣는다.
	- sudo vi 파일명 (sudo vi django-board)

```
server {
        listen 80;
        server_name {접속할 IP주소-ec2 public ip주소};

        # location = /favicon.ico { access_log off; log_not_found off; }

        location /static {
                alias {장고프로젝트 static 경로};
        }
		
		location /media {
                alias {장고프로젝트 media 경로};
        }
		
        location / {
                include proxy_params;
                proxy_pass http://unix:/tmp/gunicorn.sock;
        }
}
```
> - location /static, location /media
> 	- url/static 또는 url/media url경로로 들어온 요청은 Nginx가 지정한 경로의 파일을 읽어서 처리한다. 장고프로젝트 static 파일 디렉토리를 지정한다.
> - location / 
> 	- /static 이외의 모든 요청은 gunicorn으로 처리하도록 설정한다.
> 	- prox_pass는 동적 요청이 발생하면 그 요청을 gunicon의 유닉스 소켓으로 보내란 설정.

- 예
```
server {
        listen 80;
        server_name 3.36.62.94;

        location /static {
			alias /home/ubuntu/project/django-board/static;
        }

        location /media {
			alias /home/ubuntu/project/django-board/media;
        }
		location / {
			include proxy_params;
			proxy_pass http://unix:/tmp/gunicorn.sock;
        }
}
```
- **주의**
	- static, media 경로 에 /로 끝나면 안된다. (ex:location /media/)
	- 끝에 ";" 를 반드시 넣는다.

- /etc/nginx/site-enabled 에 symbolic 링크를 추가한다. (default는 제거)
```bash
cd /etc/nginx/sites-enabled
sudo rm default
sudo ln -s /etc/nginx/sites-available/파일명 
sudo ln -s /etc/nginx/sites-available/django-board
```

## Nginx 서비스 리스타트 
- nginx는 설치시 서비스로 등록되 자동으로 실행 된다.
```
sudo systemctl restart nginx	
```

- 웹브라우저에서 http://ip 로 요청한다.

# 참고
- 실행중인 서비스 확인 
service --status-all