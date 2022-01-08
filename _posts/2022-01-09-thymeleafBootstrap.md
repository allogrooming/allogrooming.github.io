---
layout: post
title: "thymeleaf와 Bootstrap"
---

# 0. jar war ear
![image](https://user-images.githubusercontent.com/86642180/148649640-cb55159b-e9df-4c45-baeb-5843f4b90112.png)  
`jar` = Java Archive  
Java application이 동작할 수 있도록 자바 프로젝트를 압축한 파일 형식  

<br>

`war` = Web Application Archive  
웹을 구동시키기 위해 jsp, servelt, Js, HTML, jar 등 servlet context 접근을 위한 파일들로 패키징 된 형식  
실행 시키려면 웹서버(WAS) 혹은 웹 컨테이너가 필요하다.  

<br>

`ear` = Enterprise Archive  
Java EE(Enterprise Edition)에서 쓰이는 형식  
한 개 이상의 모듈을 단일 아카이브로 패키징 뒤 애플리케이션 서버에  
한번에 일관적으로 올리기 위하여 사용되는 포맷  

<br><br>

# 1. Thymeleaf
스프링부트에서 사용을 권장하는 템플릿 엔진  
WAS를 통하지 않고 브라우저에서 파일을 열 수 있다.  
즉 HTML이 읽을 수 있는 마크업으로만 이루어졌기 때문이다.
https://www.thymeleaf.org/  
JSP보다 가볍다고 하는데 본격적으로 사용하지 않아서 잘 모르겠다.  

<br><br>

# 2. Bootstrap
CSS와 JS 기반인 반응형 오픈소스 프레임워크다.  

<br><br>

# 3. Thymeleaf에서 bootstrap 적용하기
(1) 마음에 드는 부트스트랩 지정 뒤 다운로드 및 압축 풀기  
https://themefisher.com/products/airspace-free-bootstrap-website-template/  
내가 사용할 부트스트랩 템플릿  

<br>

(2) 프로젝트에 파일 복사  
