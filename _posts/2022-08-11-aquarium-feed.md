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
📌 feed 글 목록 보기(feed/feed)  
![image](https://user-images.githubusercontent.com/86642180/184082517-b3ba7d6a-3f87-4884-b04b-c8090b20954a.png)  

<br>

📌 feed 글 내용 보기, 수정, 삭제(feed/feed/글번호)
![image](https://user-images.githubusercontent.com/86642180/184082709-c7ee1d6f-7f78-4004-9c3f-d1a4f4ae3dc5.png)  
저기서 delete 버튼 누르면 삭제 O  
스크롤 내려서 내용 바꾸고 put 누르면 수정 O  

<br>

# 3. 페이지별로 permission 추가
feed/feed 제외하고는 로그인한 사용자(admin, staff, 일반유저)만 접근 가능하게 바꿔준다!  
```
from rest_framework import permissions


SAFE_METHODS = ('GET', 'HEAD', 'OPTIONS')

class AllowAny(permissions.BasePermission):
    def has_permission(self, request, view):
        return True

class IsAuthenticated(permissions.BasePermission):
    def has_permission(self, request, view):
        return bool(request.user and request.user.is_authenticated)

class IsAdminUser(permissions.BasePermission):
    def has_permission(self, request, view):
        return bool(request.user and request.user.is_staff)

class IsAuthenticatedOrReadOnly(permissions.BasePermission):
    def has_permission(self, request, view):
        return bool(
            request.method in SAFE_METHODS or
            request.user and
            request.user.is_authenticated
        )

# feed 글 관련 
# def has_object_permission(self, request, view, obj):

#     # 수정, 삭제
#     return obj.author == request.user

class IsAuthorOrReadonly(permissions.BasePermission):
    # 인증된 유저에 대해 목록 조회 / 포스팅 등록 허용
    def has_permission(self, request, view):
        return request.user.is_authenticated

    # 작성자에 한해 Record에 대한 수정 / 삭제 허용
    def has_object_permission(self, request, views, obj):
        # 조회 요청은 항상 True
        if request.method in permissions.SAFE_METHODS:
            return True
        # PUT, DELETE 요청에 한해, 작성자에게만 허용
        return obj.author == request.user
```

최상단 aquarium 폴더에 permission.py 설정  
그 뒤 임포트해서  
```
@csrf_exempt
class FeedCreate(generics.CreateAPIView):
    queryset = Feed.objects.all()
    serializer_class = FeedSerializer
   
    permission_classes = [
        IsAuthenticated,
    ]
```

<br>

# 4. 로그인 유지
어차피 잘 되는거라  
postman으로 로그인한 유저만 글 쓸 수 있는지 테스트함  
https://velog.io/@cptkuk91/Postman%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-Token-Test  
참고☝  
![image](https://user-images.githubusercontent.com/86642180/184546109-db81a010-0c2d-4762-8740-05ae09e1df71.png)  
