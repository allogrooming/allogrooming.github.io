---
layout: post
title : "캘린더 전체프로세스"
---

# 흐름
1. 사용자가 캘린더 페이지 접속  
2. JS에서 자동으로 오늘 날짜 보여주고 ajax로 오늘 날짜 컨트롤러에 전달  
3. readCalendar 읽는 컨트롤러가 선택된 날짜를 파라미터로 /calendar에 전달  
4. DB에서 to_do, ini에서 오늘 날짜 조회 뒤 맞는 데이터 select
5. 브라우저에 데이터가 보일 수 있게 thymeleaf 출력   

<br>

# 1. 캘린더 페이지
지금까지는 큰 문제 없음

<br>

# 2. ajax 전달
![image](https://user-images.githubusercontent.com/86642180/158941780-805d7dcc-7473-4ce3-989b-24d382151228.png)
계속 undefined가 나와서 ajax 전달이 안되는 중이다  
이유를 찾아보니ㅎ..  
`var selectedDate = document.getElementById("selected-date").innerText;`  
"selected-date"라는 p태그에서 값을 가져오는건데  
처음에 `.value`를 작성해서 값을 가져오지 못한 것이었다.  

<br>

innerText로 고치니  
![image](https://user-images.githubusercontent.com/86642180/158942018-4ee25774-ef28-406a-a0cb-82aefa7177f7.png)
값을 가져오는 것은 잘 되나 ajax 통신이 여전히 안된다  

<br>

이것도 어노테이션 문제였던 것으로 보인다  
```
 @Transactional
 @RequestMapping(value="/readCalendar", produces="text/html;charset=UTF-8")
 @ResponseBody
 @PostMapping
```
기존에 컨트롤러 안에 있던 메소드에 어노테이션을 이렇게 작성했던 것을  
밑처럼 수정하니 js에서 컨트롤러까지 통신 O

<br>

```
@Transactional
@RequestMapping(value="/readCalendar", produces="text/html;charset=UTF-8", method = RequestMethod.POST)
@ResponseBody
```

`@PostMapping`이나 `@ReqeustMapping(method = RequestMethod.POST`는 같은 것인데  
왜 위처럼 작성한 것은 안되는지 정확하게 모르겠다  
Mapping 관련 어노테이션이 두 개여서 그런가..?  

<br>

```
@Transactional
    @RequestMapping(value="/readCalendar", produces="text/html;charset=UTF-8", method = RequestMethod.POST)
    @ResponseBody
    public ModelAndView readCalendar(@RequestParam Map<String, String> param) throws ParseException {

        String selectedDate = param.get("selectedDate");
        System.out.println(selectedDate);

        ModelAndView modelAndView = new ModelAndView("calendar");
        return modelAndView;
    }
```
<br>

# 3 DB에서 데이터 select하기
어쩐지 또 잘되나 했으나 값이 null로만 나온다  
```
@Transactional
    @RequestMapping(value="/readCalendar", produces="text/html;charset=UTF-8", method = RequestMethod.POST)
    @ResponseBody
    public ModelAndView readCalendar(@RequestParam Map<String, String> param) throws ParseException {

        String selectedDate = param.get("selectedDate");
        System.out.println(selectedDate);
        List<Calendar> result = calendarService.getTodoList(selectedDate);

        System.out.println(result);

        ModelAndView modelAndView = new ModelAndView("calendar");
        return modelAndView;
    }
```
Map으로 리턴 타입 바꾸니까 안되서 다시 List<CalendarDTO>로 리턴으로 되돌리니까 잘 된다  
이런 경우는 처음본다  
  
![image](https://user-images.githubusercontent.com/86642180/158947763-05ceb15b-f343-43bc-9d71-54f7cd187b81.png)
  
<br>
  
# 추가 - 스프링부트 리다이렉트 방법
  `return new RedirectView("url");`
  
  <br>
  
# 3/30
  리다이렉트 안하고 그냥 modelAndView로 다시 보내줌  
  이렇게 해도 객체 전달은 되는데 문제가  
  자꾸 null이 찍힌다..  
  해결!  
  snake case로 DTO의 자바빈 작성해서  
  
  <br>

# 4. 브라우저에 thymeleaf로 출력
  다시보니 모든 프로세스가 엉망이긴했다ㅋㅋㅋㅋ  
  결국 출력은 됐으나 휴...  
  처음에 ajax에서 받아오는 컨트롤러가 직접적으로 modelAndVIew 리턴을 하게 해서  
  프로세스가 너어어무 꼬였다ㅋㅋ  
  
# 5. 타임리프 문제
  `if(param == null)` 컨트롤러에서 파라미터인 param을 map으로 받는데 저렇게 하니까 로직이 꼬이지ㅋㅋ  
  `param.isEmpty()`로 바꿔줌  
  
  <br>
  
  그러고 그전에도 잘되던 category 부분도 select가 안되는 상황ㅎㅎ  
  일단 브랜치 새로 만들고(thymeleafHotIssue)  
  기존 브랜치 롤백해서 작업 중 병합할 때 뭔가 문제가 있었나보다  
  
  <br>
  
  내 생각에 지금 문제가 생긴 이유는  
  리다이렉트 뒤 동일한 페이지를 보여주는데 modelAndView 타입 메소드로 진행해서 그런것 같다  
  막상 타임리프 출력문에서 카멜케이스를 스네이크로 바꾸면 exception 문제가 생겼다.  
  컨트롤러에서 오브젝트 만들고 추가하고 보내는 것은 문제가 없는데  
  렌더링의 문제로 추정중  
  
  <br>
  
  그래도 배포는 해야되니까 다른 방법으로 진행한다  
  생각해보니 컨트롤러가 리턴해주는 값은 ajax의 data로 들어가니  
  ajax에서 success 했을 때 요소에 data 값을 추가해도 된다..  
  https://velog.io/@leyuri/SpringBoot-ajax-%EC%82%AC%EC%9A%A9%EB%B2%95-%EB%B0%8F-%EC%98%88%EC%A0%9C-thymeleaf
