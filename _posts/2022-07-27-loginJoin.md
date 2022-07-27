---
layout: post
title: "로그인/회원가입 스프링시큐리티"
---

# 순서도 및 구상
![image](https://user-images.githubusercontent.com/86642180/181168936-a33b75d0-36ca-48df-b567-bcf1a26e7dac.png)  

# 회원가입
### 0. DB 확인
member table 생성  
```
create table member(
	member_code int not null auto_increment,
    memer_id varchar(15) not null,
    password varchar(20) not null,
    nickname varchar(15) not null,
    email varchar(50) not null,
    sign_up_date timestamp default now(),
    active_state int not null,
    primary key (member_code));
```
어차피 인서트 할 때 member_code의 경우는 IFNULL + MAX 써서 자동으로 들어가게 할 것  
그리고 sign_up_date는 default로 넣어주면 됨  
active_state도 가입한 사람이니까 일단 활동 중인 1로 값이 입력됨  

<br>

### 1. form 생성
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Small Begin</title>

    <script src="/webjars/jquery/3.6.0/jquery.min.js"></script>
    <script type="text/javascript" src="/js/common.js"></script>

    <!-- navbar 및 전체 css 관련 !-->
    <link th:href="@{/css/common.css}" rel="stylesheet" />
    <link th:href="@{/css/navbar.css}" rel="stylesheet" />
    <link th:href="@{/css/footer.css}" rel="stylesheet" />
    <script th:src="@{/js/navbar.js}"></script>

</head>
<body>
<div id="container">

    <div th:replace="/fragment/navbar.html :: fragment-nav"></div>

    <div id="formCss">
        <form method="post" id="joinUs">
            <span>ID</span> &nbsp; &nbsp; &nbsp; <input type="text" id="userId" name="userId" class="form" size="25"><br>
            <span>Password</span> &nbsp; &nbsp; &nbsp; <input type="text" id="password" name="password" class="form" size="25"><br>
            <span>Nickname</span> &nbsp; &nbsp; &nbsp; <input type="text" id="nickname" name="nickname" class="form" size="25"><br>
            <span>Email</span> &nbsp; &nbsp; &nbsp; <input type="text" id="email" name="email" class="form" size="25"><br>
            <input type="button" id="click" value="sumbit" class="buttons">
        </form>
    </div>

</div>

<footer th:replace="/fragment/footer.html :: fragment-footer"></footer>

</body>
</html>
```

<br>

### 2. ajax를 통해 json 타입으로 컨트롤러에 정보 전달


<br>

### 3. 
