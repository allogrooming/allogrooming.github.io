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
