---
layout: post
title: "캘린더 관련 할 것"
---

# 1. todo CRUD
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
  
그래서 td 안의 <div>를 클릭했을 경우 calendar1.js의 getDateFromId가 제대로 작동하지 않음  
<div>에는 id 값이 들어가지 않아서  
👉 <div>가 클릭될 경우 <div>의 부모인 td의 아이디 값을 찾게 한다  
  
<br>



### 2. 삭제 기능

### 3. 수정 기능

# 2. Spring security
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
