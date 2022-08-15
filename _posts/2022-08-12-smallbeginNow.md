---
layout: post
title: "캘린더 관련 할 것"
---

# ☝ todo CRUD
### 0. DB 수정
`Alter table to_do modify plan_date timestamp default now();`  

<br>

### 1. td 아이디 값으로 그 날짜에 맞는 todo 값 가져오기
td 안의 <div>를 클릭했을 경우(숫자만 클릭)  
![image](https://user-images.githubusercontent.com/86642180/184384236-fe0d17a1-1a19-41ca-bb57-f1c0df877be8.png)  

<br>

td를 클릭했을 경우(숫자 외의 공간)  
![image](https://user-images.githubusercontent.com/86642180/184388352-8644b0f7-c384-4b02-b96f-fd38d62f304b.png)  

<br>
  
그래서 td 안의 div를 클릭했을 경우 calendar1.js의 getDateFromId가 제대로 작동하지 않음  
div에는 id 값이 들어가지 않아서  
👉 div가 클릭될 경우 div의 부모인 td의 아이디 값을 찾게 한다  
  
<br>
  
```
    function changeClickedDate(e){
        if (clickedDateElement != e.target){
            clickedDateElement.classList.remove('active');
            clickedDateElement = e.target;
            clickedDate = e.target.id;
            e.target.classList.add('active');
            showCurrentDateOnLeft(getDateFromId(clickedDate));
        }
    }
```
이랬던 코드에서 <div>의 부모인 td를 가져오게 하고  
그 id 값을 getDateFromId에 사용했다

```
    function changeClickedDate(e){
        if (clickedDateElement != e.target){
            clickedDateElement.classList.remove('active');
            clickedDateElement = e.target;
            clickedDate = e.target.id;
            if(clickedDate == ""){
                var tempClickedDate = clickedDateElement.parentNode.id;
                clickedDateElement.parentNode.classList.add('active');
                showCurrentDateOnLeft(getDateFromId(tempClickedDate));
            }else{
                e.target.classList.add('active');
                showCurrentDateOnLeft(getDateFromId(clickedDate));
            }
        }
    }
```
이거 찾는다고 하루가 다 갔다.. 허탈하다ㅎ..  
결과  
![image](https://user-images.githubusercontent.com/86642180/184420889-8e519505-2336-4ec2-9e45-949cfa40f6f3.png)  

### 2. 추가 기능
```
    @Transactional
    @RequestMapping(value="/readCalendar")
    @ResponseBody
    public List<Todo> readCalendar(@RequestParam Map<String, String> param) {

        String selectedDate = param.get("selectedDate");
        List<Todo> todo = calendarService.getTodoList(selectedDate);
        return todo;
    }
```
아주 예전에 작성했던 컨트롤러로 진행을 다시 할 것이다  
먼저 JS에서 선택된 날짜를 /readCalendar로 보내주는 것이 필요하다  
JS 함수는  
(1) 클래스 값이 "active"인 요소를 찾는다  
(2) 그 아이디 값을 readForm 함수를 통해 /readCalendar로 보내준다  
처음 로드되었을 때도 적용이 필요함!!  

<br>  
  
```
function getDate4Ajax(idStr){
    if(idStr != ""){
        dateStr = "";
        dateStr += idStr.slice(0, 4);
        dateStr += "-";
        dateStr += idStr.slice(4, 6);
        dateStr += "-";
        dateStr += idStr.slice(-2);
        console.log(dateStr);
        return dateStr;
    }
}

function readToDo(clickedDate){
    console.log(clickedDate);
     $.ajax({
             url : "/readCalendar",
             type : "post",
             contentType: 'application/x-www-form-urlencoded; charset=utf-8',
             dataType : "text",
             data : {"clickedDate" : clickedDate},
             success : function(result){
                 console.log(result);
             },
             error : function(err){
                 console.log(err+"에러발생");
             }
      });
}
  
function clickDate(pointDate){
    // 캘린더 화면에 접속하면 자동으로 해당일의 날짜가 클릭되어 활성화 상태가 된다.
    if (!pointDate) pointDate = new Date();
    var clickedDate = setDateId(pointDate, pointDate.getDate());
    var clickedDateElement = document.getElementById(clickedDate);
    console.log('clickedDate: ', clickedDate);
    clickedDateElement.classList.add('active');

    // 여기에 첫화면에 대한 to_do 서치하는 함수 넣기 parameter = getDateFromId(clickedDate)
    var param4readToDo = getDate4Ajax(clickedDate);
    readToDo(param4readToDo);
```
함수 2개 만들고 clickedDate 안에 넣어준다 이러면 첫화면일 경우에 데이터를 잘 불러옴  
![image](https://user-images.githubusercontent.com/86642180/184425481-412b31c9-067b-4442-ab9d-12b1ed867837.png)  
  
<br>
  
#### 다른 날짜 클릭 시에도 적용시키기
```
    $('#calendar td').click(function(){
        var tdID = $(this).attr('id');
        console.log("click!!");
        var param4readToDo = getDate4Ajax(tdID);
        readToDo(param4readToDo);
    })
```  
![image](https://user-images.githubusercontent.com/86642180/184426128-e5c4c6f7-8b27-40dd-94e1-6489bfd88957.png)  

#### 가져온 데이터 html에 붙이기


### 3. 삭제 기능

### 4. 수정 기능

# ✌ Spring security
```
package com.project.smallbeginjava11.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter{

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .defaultSuccessUrl("/home", true)
                .permitAll()
                .and()
                .logout();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```
