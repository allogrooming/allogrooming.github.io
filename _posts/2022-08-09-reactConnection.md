---
layout: post
title: "커넥션 테스트"
---

# 깃 브랜치 받아오기(클론)
```
$ git pull https://github.com/aquariumProject/aquarium.git
$ git clone -b test --single-branch https://github.com/aquariumProject/aquarium.git
```

# yarn 설치
새로운 폴더(디렉토리)에 클론하는 것이므로 설치한다

# 실행
![image](https://user-images.githubusercontent.com/86642180/183487233-7a09dfb6-c7d0-4106-b756-1b1587f66756.png)  
디렉토리를 보면  
aquarium/aquarium 에서 `python manage.py runserver`  
aquarium/yourfish 에서 `yarn start`  
![image](https://user-images.githubusercontent.com/86642180/183487686-dc58fda6-2b89-4c93-86b4-56fa1ea7edce.png)  
완료  

# login.js 수정
```
 const signIn = () => {
    const data = {
      email : id,
      password : pw,
    };
    axios.post('http://127.0.0.1:8000/members/login/', data)
    .then(response => {
      const { accessToken } = response.data;
```

# test 브랜치에 푸쉬
```
git init
cd aquarium (가장 상위 말고 그 다음 상위의 aquarium 폴더에서 git add . 해야함!)
git add .
git commit -m "message"
git push -u origin test
```

# 로그인 토큰 (8/9)
![image](https://user-images.githubusercontent.com/86642180/183490375-5722976b-e56b-42ed-8bf2-31bf4adeb970.png)  
email 대신 username일 경우는 성공  
emailField 등 변경을 한다  

<br>

# 진행상황 및 일정
JSON 데이터 키 값 정하기 👉 정기세션 이후에 진행 예정  
리액트 연동 ⭕  
커뮤니티 게시판 👉 혜원  
쇼핑 게시판 👉 민영  
