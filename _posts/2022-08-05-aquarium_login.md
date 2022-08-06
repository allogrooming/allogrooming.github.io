---
layout: post
title: "로그인 추가"
---

# 1. Authentication 종류
<b>(1) Basic Authentication</b>  
Username, password 일치를 확인해서 판별  
<b>📌(2) Token Authentication</b>  
token으로 인증, 이거 쓸 예정  
<b>(3) Session Authentication</b>  
ajax 사용할 때 적합함  
<b>(4) Remote User Authentication</b>  
remote_user 객체를 활용  

<br>

# 2. User 확장
<b>(1) 프록시 모델 사용</b>  

<br>

<b>(2) User 모델과 일대일관계의 프로필 테이블 추가</b>  

<br>

<b> 📌 (3) AbstractUser 모델 상속한 사용자 정의 User 모델 사용</b>  

<br>

<b>(4) AbstractBaseUser 모델 상속한 사용자 정의 User 모델 사용</b>  

<br>

# 3. 회원가입 완료된 브랜치 받아오기
user확장이 끝났고 admin으로 계정이 생성된것이 확인된 브랜치를 가져온다  
1. 폴더 생성
2. 우클릭 - git bash 실행 - code .
3. `$ git clone -b 클론할 브랜치명 --single-branch url`  

<br>

# 4. 에러 해결 django.core.exceptions.ImproperlyConfigured: The SECRET_KEY setting must not be empty.
받아온 브랜치 서버 실행하려니까 안된다  
![image](https://user-images.githubusercontent.com/86642180/183142941-2d1a9305-c897-4676-a29d-b5e99a5d9ae1.png)  

<br>

당장 받은 브랜치는 장고 내부의 sqlite를 쓴거라  
rds 세팅을 넣어주니 일단 서버는 켜졌다  

<br>
회원가입을 진행했고 여기에 이어서 로그인 기능 실현, serialize까지 하면된다  

<br>

# 5. 참고 : 리액트와 장고의 작동 방식
1. 백 : 장고와 DB 연결 ⭕  
2. 백 : 장고 ORM - DB의 데이터를 object 형태로 받아옴 ⭕  
3. 백 : 받아온 데이터를 Django REST Framework를 활용해 json객체로 변형 ⭕  
4. 백 : json객체를 특정 url에 전달  
👉 프론트에서 리액트 앱을 만들면 백에서는 views.py와 urls.py를 수정해 리액트 모듈이 얹힌 템플릿에서 해당 url의 json view를 받도록 수정  
5. 프 : React App에서 fetch를 통해 특정 url로부터 json객체를 받음  
6. 프 : 받아온 데이터를 리액트식 문법으로 가공/작업합니다  

<br>

# 6. 로그인 구현 with JWT
### JWT
Json Web Token  
로그인이 성공적으로 되면 사용자에게 주는 토큰도 json형태로 프론트(리액트)에 전달할 예정  

<br>

### (1) 패키지 설치 및 
📌 command line
```
pip install djangoframework-jwt
pip install django-rest-authtoken
```
![image](https://user-images.githubusercontent.com/86642180/183255557-2b13b9a9-ccb2-4bd6-b043-6f9116a8b6b6.png)  

<br>

📌 settings.py
```
import datetime

INSTALLED_APPS = [
	'rest_framework.authtoken',
]

AUTH_USER_MODEL = 'members.User' //members 앱 안의 models.py에 User 모델 사용!

REST_FRAMEWORK={
    'DEFAULT_AUTHENTICATION_CLASSES':[
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        # 'rest_framework.authentication.TokenAuthentication',
        # 'rest_framework.authentication.SessionAuthentication',
        # 'rest_framework.authentication.BasicAuthentication',
    ],

    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated', # 인증된 사용자만 접근 가능
        'rest_framework.permissions.IsAdminUser', # 관리자만 접근 가능
        'rest_framework.permissions.AllowAny', # 누구나 접근 가능
    ],

    'DEFAULT_RENDERER_CLASSES': [ # 자동으로 json으로 바꿔줌
        'rest_framework.renderers.JSONRenderer',
        'rest_framework.renderers.BrowsableAPIRenderer',
    ],
}

# JWT_AUTH 설정 (JWT 추가 설정)
JWT_AUTH = {
    'JWT_SECRET_KEY': SECRET_KEY,
    'JWT_ALGORITHM': 'HS256', # 암호화 알고리즘
    'JWT_ALLOW_REFRESH': True, # refresh 사용 여부
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=7), # 유효기간 설정
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=28), # JWT 토큰 갱신 유효기간
}
```

### (2) User 모델 생성 + 회원가입 창 수정
figma에서 작업한 것을 보면 닉네임도 회원가입 시 받는데 닉네임 필드를 추가한다  

