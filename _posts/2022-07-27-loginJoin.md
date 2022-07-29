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
```
function readForm(formId, url){
      $.ajax({
             url : url,
             type : "post",
             contentType: 'application/x-www-form-urlencoded; charset=utf-8',
             dataType : "text",
             data : $(formId).serialize(),
             success : function(result){
                 console.log(result);
                 console.log(this.data);
             },
             error : function(err){
                 console.log(err+"에러발생");
                 console.log(this.data);
             }
      });
}
```
```
<script>
    $(function(){
        $("#form-submit").click(function(){
            var id = $("#id").val();
            var password = $("#password").val();
            var nickname = $("#nickname").val();
            var email = $("#email").val();

            if(id == '' || password== '' || nickname == '' || email == ''){
                alert("Please fill the form completely");
            }else{
                readForm("#joinUs", "/readJoinUs");
            }
        })
    })
</script>
```

<br>

### 3. 컨트롤러
```
    @Transactional
    @RequestMapping(value="/readJoinUs")
    @ResponseBody
    public ModelAndView readJoinUs(@RequestParam Map<String, String> param){

        System.out.println(param);
        joinService.inputJoin(param);

        ModelAndView modelAndView = new ModelAndView("joinSuccess");
        System.out.println("move to joinSuccess");
        return modelAndView;
    }
```

<br>

### 4. Service layer
```
package com.project.smallbeginjava11.service;

import java.text.ParseException;
import java.util.Map;

public interface JoinService {
    void inputJoin(Map<String, String> param) throws ParseException;
}
```
<br>

```
package com.project.smallbeginjava11.serviceImpl;

import com.project.smallbeginjava11.mapper.JoinMapper;
import com.project.smallbeginjava11.service.JoinService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.text.ParseException;
import java.util.Map;

@Service
@RequiredArgsConstructor
public class JoinServiceImpl implements JoinService {

    private final JoinMapper joinMapper;

    @Override
    public void inputJoin(Map<String, String> param) throws ParseException {
        joinMapper.insertMember(param);
    }
}
```

<br>

### 5. Mapper
```
package com.project.smallbeginjava11.mapper;

import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

import java.util.Map;

@Mapper
@Repository
public interface JoinMapper {
    void insertMember(Map<String, String> param);
}
```

<br>

```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.project.smallbeginjava11.mapper.JoinMapper">

    <insert id="insertMember" parameterType="hashMap">
        INSERT INTO member(
            member_code,
            member_id,
            password,
            nickname,
            email,
            sign_up_date,
            active_state
        )
        VALUES(
                      (select IFNULL(MAX(a.member_code), 0) + 1 FROM member a),
                      #{memberId},
                      #{password},
                      #{nickname},
                      #{email},
                      default,
                      1
              )
    </insert>

</mapper>
```

<br>

### 6. DTO
```
package com.project.smallbeginjava11.DTO;

import lombok.*;

import java.util.Date;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class JoinUs {
    private int memberCode;
    private String memberId;
    private String password;
    private String nickname;
    private String email;
    private Date signUpDate;
    private int activeState;
}
```

<br>

### 7. 에러 해결
컨트롤러에서 문제가 두 개나 생겼다ㅎ  
일단 `inputJoin`에서 Unhandled exception: java.text.ParseException  
두 번째는 joinSuccess가 제대로 로드되지 않는것  

<br>

컨트롤러 메소드에도 ParseException 추가하니 문제 해결  

<br>

#### 또 다른 문제가 발생함  
```
2022-07-29 19:23:14.225 ERROR 10028 --- [nio-9090-exec-2] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.springframework.jdbc.BadSqlGrammarException: 
### Error updating database.  Cause: java.sql.SQLSyntaxErrorException: Unknown column 'member_id' in 'field list'
### The error may exist in file [C:\Users\user\IdeaProjects\smallBeginJava11\out\production\resources\mybatis\mapper\JoinMapper.xml]
### The error may involve com.project.smallbeginjava11.mapper.JoinMapper.insertMember-Inline
### The error occurred while setting parameters
### SQL: INSERT INTO member(             member_code,             member_id,             password,             nickname,             email,             sign_up_date,             active_state         )         VALUES(                       (select IFNULL(MAX(a.member_code), 0) + 1 FROM member a),                       ?,                       ?,                       ?,                       ?,                       default,                       1               )
### Cause: java.sql.SQLSyntaxErrorException: Unknown column 'member_id' in 'field list'
```
예전에도 많이 봤던 에러다  
매퍼에 param이 제대로 안넘어가서 생기는 문제  

<br>

저럴 경우 확인할 건 DTO 쪽인데  
이미 lombok을 임포트해서 그건 아닐거다  

<br>

MySQL 워크벤치 다시보니까..  
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
member_id로 해야될거 오타가 나서 저런거였다..  

<br>

Table 칼럼명 수정하니까 나오는 에러  
```
### SQL: INSERT INTO member(             member_code,             member_id,             password,             nickname,             email,             sign_up_date,             active_state         )         VALUES(                       (select IFNULL(MAX(a.member_code), 0) + 1 FROM member a),                       ?,                       ?,                       ?,                       ?,                       default,                       1               )
### Cause: java.sql.SQLIntegrityConstraintViolationException: Column 'member_id' cannot be null
; Column 'member_id' cannot be null; nested exception is java.sql.SQLIntegrityConstraintViolationException: Column 'member_id' cannot be null] with root cause
```

⭐ HTML에서 input의 name과 칼럼명이 맞는지 확인한다  
`<span>ID</span> &nbsp; &nbsp; &nbsp; <input type="text" id="userId" name="userId" class="form" size="25"><br>`  
userId라고 했으니까 안되지... memberId로 변경하니 저장 잘 된다  
![image](https://user-images.githubusercontent.com/86642180/181751968-02d92483-78f9-40dd-82ef-5442130941f1.png)
