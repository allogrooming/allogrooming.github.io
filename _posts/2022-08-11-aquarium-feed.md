---
layout: post
title: "게시글 crud, like, 이미지 업로드, 조회수, 스크랩, 댓글, 대댓글, 로그인 permission"
---

# 일단 지금까지 진행된 것
like, 스크랩, user 외래키 사용해서 끌어오기 없이는  
게시글 작성 가능!  

# 1. Login permission
urls.py에서 `path('api_auth/', include('rest_framework.urls', namespace='rest_framework')),`를 추가했으므로  
![image](https://user-images.githubusercontent.com/86642180/184071149-5be305f2-5b17-4813-9b28-df6cd804f60c.png)  
어느 페이지에서든 로그인 자체는 가능함  
지금은 로그인을 클릭시 `http://127.0.0.1:8000/api_auth/login/?next=/feed/add/`  
처럼 로그인을 완료하면 이전 페이지로 돌아가게 설정됨  

<br>

https://ssungkang.tistory.com/entry/Django-Authentication-%EA%B3%BC-Permissions  
참고!  
각각 앱별로 permission.py 파일을 설정한 뒤  
generic으로 만들어서 보내지는 view 클래스 안에서 어떤 것을 쓸지 정한다  
<br>

# 2. CRUD - like, 이미지, 조회스, 스크랩 안되는 feed 글
