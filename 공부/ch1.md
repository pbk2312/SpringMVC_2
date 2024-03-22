# 타임리프 기본 기능
-------

###  타임리프 특징

* 서버 사이드 HTML 렌더링(SSR)
* 네츄럴 템플릿
* 스프링 통합 지원

### 타임리프 기본 기능

타임리프 사용 선언
```ruby
<html xmlns:th="http://www.thymeleaf.org">
```

## 텍스트 - text,utext

타임리프는 기본적으로 HTML 태그의 속성에 기능을 정의해서 동작한다.HTML의 콘텐트(content)에 데이터를 출력할 때는 다음과 같이 `th:text`를 사용하면 된다.


`<span th:text=${data}`


HTML 태그의 속성이 아니라 HTML 콘텐츠 영역안에서 직접 출력하고 싶으면 다음과 같이 `[[....]]`를 사용하면 된다.

`컨텐츠 안에서 직접 출력하기 = [[${data}]]`

## HTML 엔티티

* `<` -> `&lt`
* `>` -> `&gt`


## 변수 - SpringEL

변수 표현식:${...}



BasicController
```ruby
@GetMapping("/variable")
 public String variable(Model model) {
     User userA = new User("userA", 10);
     User userB = new User("userB", 20);
     List<User> list = new ArrayList<>();
     list.add(userA);
     list.add(userB);
     Map<String, User> map = new HashMap<>();
     map.put("userA", userA);
     map.put("userB", userB);
     model.addAttribute("user", userA);
     model.addAttribute("users", list);
     model.addAttribute("userMap", map);
     return "basic/variable";
 }
 @Data
 static class User {
   private String username;
    private int age;
    public User(String username, int age) {
        this.username = username;
        this.age = age;
}
}

```

/resources/templates/basic/variable.html
```ruby
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>SpringEL 표현식</h1> <ul>Object
    <li>${user.username} =
    <li>${user['username']} = <span th:text="${user['username']}"></span></li>
    <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></
li>
</ul>
<ul>List
    <li>${users[0].username}    = <span th:text="${users[0].username}"></span></
li>
    <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></
span></li>
    <li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></
span></li>
</ul>
<ul>Map
    <li>${userMap['userA'].username} =  <span th:text="$
{userMap['userA'].username}"></span></li>
    <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']
['username']}"></span></li>
    <li>${userMap['userA'].getUsername()} = <span th:text="$
{userMap['userA'].getUsername()}"></span></li>
 </ul>
 </body>
</html>

```

**SpringEL 다양한 표현식 사용**

**Object**


* `user.username` : user의 username을 프로퍼티 접근 `user.getUsername()`
* `user['username']` : 위와 같음 `user.getUsername()`
* `user.getUsername()` : user의 `getUsername()` 을 직접 호출

**List**

* `users[0].username` : List에서 첫 번째 회원을 찾고 username 프로퍼티 접근 -> `list.get(0).getUsername()`
* `users[0]['username']` : 위와 같음
* `users[0].getUsername()` : List에서 첫 번째 회원을 찾고 메서드 직접 호출


**Map**

* `userMap['userA'].username` : Map에서 userA를 찾고, username 프로퍼티 접근 -> `map.get("userA").getUsername()`
* `userMap['userA']['username']` : 위와 같음
* `userMap['userA'].getUsername()` : Map에서 userA를 찾고 메서드 직접 호출


## 유틸리티 객체와 날짜

**타임리프 유틸리티 객체들**

* `#message` : 메시지, 국제화 처리
* `#uris` : URI 이스케이프 지원
* `#dates` : `java.util.Date` 서식 지원
* `#calendars` : `java.util.Calendar` 서식 지원
* `#temporals` : 자바8 날짜 서식 지원
* `#numbers` : 숫자 서식 지원
* `#strings` : 문자 관련 편의 기능
* `#objects` : 객체 관련 기능 제공
* `#bools` : boolean 관련 기능 제공
* `#arrays` : 배열 관련 기능 제공
* `#lists` , `#sets` , `#maps` : 컬렉션 관련 기능 제공
* `#ids` : 아이디 처리 관련 기능 제공, 뒤에서 설명


**자바8 날짜**
타임리프에서 자바8 날짜인 `LocalDate` , `LocalDateTime` , `Instant` 를 사용하려면 추가 라이브러리가 필요하
다. 스프링 부트 타임리프를 사용하면 해당 라이브러리가 자동으로 추가되고 통합된다.

**타임리프 자바8 날짜 지원 라이브러리**
`thymeleaf-extras-java8time`

**자바8 날짜용 유틸리티 객체**
`#temporals`

**사용 예시**
```ruby
<span th:text="${#temporals.format(localDateTime, 'yyyy-MM-dd HH:mm:ss')}"></span>
```


**URL 링크**
타임리프에서 URL을 생성할 때는 `@{...}` 문법을 사용하면 된다.

**BasicController 추가**
```ruby
 @GetMapping("/link")
 public String link(Model model) {
     model.addAttribute("param1", "data1");
     model.addAttribute("param2", "data2");
     return "basic/link";
}
```

**/resources/templates/basic/link.html**

```ruby
<!DOCTYPE html>
 <html xmlns:th="http://www.thymeleaf.org">
 <head>
     <meta charset="UTF-8">
     <title>Title</title>
 </head>
<body>
<h1>URL 링크</h1> <ul>
     <li><a th:href="@{/hello}">basic url</a></li>
     <li><a th:href="@{/hello(param1=${param1}, param2=${param2})}">hello query
 param</a></li>
     <li><a th:href="@{/hello/{param1}/{param2}(param1=${param1}, param2=$
 {param2})}">path variable</a></li>
     <li><a th:href="@{/hello/{param1}(param1=${param1}, param2=${param2})}">path
 variable + query parameter</a></li>
 </ul>
 </body>
</html>
```
**단순한 URL**
`@{/hello}` -> `/hello`

**쿼리 파라미터**
`@{/hello(param1=${param1}, param2=${param2})}` -> `/hello?param1=data1&param2=data2`


`()` 에 있는 부분은 쿼리 파라미터로 처리된다.


**경로 변수**

`@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}` -> `/hello/data1/data2`

URL 경로상에 변수가 있으면 `()` 부분은 경로 변수로 처리된다.

**경로 변수 + 쿼리 파라미터**

`@{/hello/{param1}(param1=${param1}, param2=${param2})}` -> `/hello/data1?param2=data2`

경로 변수와 쿼리 파라미터를 함께 사용할 수 있다.


## 반복

타임리프에서 반복은 `th:each` 를 사용한다. 추가로 반복에서 사용할 수 있는 여러 상태 값을 지원한다.

**BasicController 추가**
```ruby
 @GetMapping("/each")
 public String each(Model model) {
     addUsers(model);
     return "basic/each";
 }
 private void addUsers(Model model) {
     List<User> list = new ArrayList<>();
     list.add(new User("userA", 10));
     list.add(new User("userB", 20));
     list.add(new User("userC", 30));
     model.addAttribute("users", list);
 }
```


**/resources/templates/basic/each.html**

```ruby
 <!DOCTYPE html>
 <html xmlns:th="http://www.thymeleaf.org">
 <head>
     <meta charset="UTF-8">
     <title>Title</title>
 </head>
<body>
<h1>기본 테이블</h1> <table border="1">
     <tr>
         <th>username</th>
         <th>age</th>
     </tr>
     <tr th:each="user : ${users}">
         <td th:text="${user.username}">username</td>
         <td th:text="${user.age}">0</td>
  </tr>
 </table>
<h1>반복 상태 유지</h1>
 <table border="1">
     <tr>
         <th>count</th>
         <th>username</th>
         <th>age</th>
         <th>etc</th>
     </tr>
     <tr th:each="user, userStat : ${users}">
         <td th:text="${userStat.count}">username</td>
         <td th:text="${user.username}">username</td>
         <td th:text="${user.age}">0</td>
         <td>
             index = <span th:text="${userStat.index}"></span>
             count = <span th:text="${userStat.count}"></span>
             size = <span th:text="${userStat.size}"></span>
             even? = <span th:text="${userStat.even}"></span>
             odd? = <span th:text="${userStat.odd}"></span>
             first? = <span th:text="${userStat.first}"></span>
             last? = <span th:text="${userStat.last}"></span>
             current = <span th:text="${userStat.current}"></span>
</td> </tr>
 </table>
 </body>
</html>
```

**반복 기능**
`<tr th:each="user : ${users}">`
* 반복시 오른쪽 컬렉션( `${users}` )의 값을 하나씩 꺼내서 왼쪽 변수( `user` )에 담아서 태그를 반복 실행합니다.
* `th:each` 는 `List` 뿐만 아니라 배열, `java.util.Iterable` , `java.util.Enumeration` 을 구현한 모든 객체를 반복에 사용할 수 있습니다. `Map` 도 사용할 수 있는데 이 경우 변수에 담기는 값은 `Map.Entry` 입니다.


**반복 상태 유지**
`<tr th:each="user, userStat : ${users}">`
반복의 두번째 파라미터를 설정해서 반복의 상태를 확인 할 수 있습니다.
두번째 파라미터는 생략 가능한데, 생략하면 지정한 변수명( `user` ) + `Stat` 가 됩니다. 여기서는 `user` + `Stat` = `userStat` 이므로 생략 가능합니다.


**반복 상태 유지 기능**

* `index` : 0부터 시작하는 값
* `count` : 1부터 시작하는 값
* `size` : 전체 사이즈
* `even` , `odd` : 홀수, 짝수 여부( `boolean` )
* `first` , `last` :처음, 마지막 여부( `boolean` )
* `current` : 현재 객체



## 조건부 평가

타임리프의 조건식
`if` , `unless` ( `if` 의 반대)


**BasicController 추가**

```ruby
 @GetMapping("/condition")
 public String condition(Model model) {
     addUsers(model);
     return "basic/condition";
 }
```


**/resources/templates/basic/condition.html**
```ruby
 <!DOCTYPE html>
 <html xmlns:th="http://www.thymeleaf.org">
 <head>
     <meta charset="UTF-8">
     <title>Title</title>
 </head>
 <body>
 <h1>if, unless</h1>
 <table border="1">
<tr>
 <th>count</th>
         <th>username</th>
         <th>age</th>
     </tr>
     <tr th:each="user, userStat : ${users}">
         <td th:text="${userStat.count}">1</td>
         <td th:text="${user.username}">username</td>
         <td>
<span th:text="${user.age}">0</span>
<span th:text="'미성년자'" th:if="${user.age lt 20}"></span> <span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>
</td> </tr>
</table>
 <h1>switch</h1>
 <table border="1">
     <tr>
         <th>count</th>
         <th>username</th>
         <th>age</th>
     </tr>
     <tr th:each="user, userStat : ${users}">
         <td th:text="${userStat.count}">1</td>
         <td th:text="${user.username}">username</td>
         <td th:switch="${user.age}">
<span th:case="10">10살</span> <span th:case="20">20살</span> <span th:case="*">기타</span>
</td> </tr>
 </table>
 </body>
</html>
```

**if, unless**
타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않는다.
만약 다음 조건이 `false` 인 경우 `<span>...<span>` 부분 자체가 렌더링 되지 않고 사라진다.
`<span th:text="'미성년자'" th:if="${user.age lt 20}"></span>`

**switch**
`*` 은 만족하는 조건이 없을 때 사용하는 디폴트이다.

## 자바스크립트 인라인

타임리프는 자바스크립트에서 타임리프를 편리하게 사용할 수 있는 자바스크립트 인라인 기능을 제공한다. 자바스크립트 인라인 기능은 다음과 같이 적용하면 된다.


`<script th:inline="javascript">`


**BasicController 추가**
```ruby
 @GetMapping("/javascript")
 public String javascript(Model model) {
     model.addAttribute("user", new User("userA", 10));
     addUsers(model);
     return "basic/javascript";
 }
```

**/resources/templates/basic/javascript.html**

```ruby
 <!DOCTYPE html>
 <html xmlns:th="http://www.thymeleaf.org">
 <head>
     <meta charset="UTF-8">
     <title>Title</title>
 </head>
<body>
<!-- 자바스크립트 인라인 사용 전 --> <script>
     var username = [[${user.username}]];
     var age = [[${user.age}]];
//자바스크립트 내추럴 템플릿
var username2 = /*[[${user.username}]]*/ "test username";
//객체
     var user = [[${user}]];
 </script>
<!-- 자바스크립트 인라인 사용 후 --> <script th:inline="javascript">
     var username = [[${user.username}]];
     var age = [[${user.age}]];
//자바스크립트 내추럴 템플릿
var username2 = /*[[${user.username}]]*/ "test username";
//객체
     var user = [[${user}]];
 </script>
 </body>
 </html>

```



**자바스크립트 인라인 사용 전 - 결과**
```ruby
 <script>
 var username = userA;
 var age = 10;
//자바스크립트 내추럴 템플릿
var username2 = /*userA*/ "test username";
//객체
var user = BasicController.User(username=userA, age=10);
</script> 


```
**자바스크립트 인라인 사용 후 - 결과**

```ruby
 <script>
 var username = "userA";
 var age = 10;
//자바스크립트 내추럴 템플릿 var username2 = "userA";
//객체
var user = {"username":"userA","age":10};
</script>
```

**텍스트 렌더링**

* `var username = [[${user.username}]];`
  * 인라인 사용 전 -> `var username = userA;`
  * 인라인 사용 후 `var username = "userA";`
* 인라인 사용 전 렌더링 결과를 보면 `userA` 라는 변수 이름이 그대로 남아있다. 타임리프 입장에서는 정확하게 렌 더링 한 것이지만 아마 개발자가 기대한 것은 다음과 같은 "userA"라는 문자일 것이다. 결과적으로 userA가 변수 명으로 사용되어서 자바스크립트 오류가 발생한다. 다음으로 나오는 숫자 age의 경우에는 `"` 가 필요 없기 때문에 정상 렌더링 된다.
* 인라인사용후렌더링결과를보면문자타입인경우 `"` 를포함해준다.추가로자바스크립트에서문제가될수있 는 문자가 포함되어 있으면 이스케이프 처리도 해준다. 예) `"` -> `\"`


**자바스크립트 내추럴 템플릿**


타임리프는 HTML 파일을 직접 열어도 동작하는 내추럴 템플릿 기능을 제공한다. 자바스크립트 인라인 기능을 사용하 면 주석을 활용해서 이 기능을 사용할 수 있다.

* `var username2 = /*[[${user.username}]]*/ "test username";`
  *   인라인 사용 전 -> `var username2 = /*userA*/ "test username";`
  *   인라인 사용 후 `var username2 = "userA";`
* 인라인 사용 전 결과를 보면 정말 순수하게 그대로 해석을 해버렸다. 따라서 내추럴 템플릿 기능이 동작하지 않고, 심지어 렌더링 내용이 주석처리 되어 버린다
* 인라인 사용 후 결과를 보면 주석 부분이 제거되고, 기대한 "userA"가 정확하게 적용된다.


**객체**


타임리프의 자바스크립트 인라인 기능을 사용하면 객체를 JSON으로 자동으로 변환해준다.

* `var user = [[${user}]];`
  * 인라인 사용 전 -> `var user = BasicController.User(username=userA, age=10);`
  * 인라인 사용 후 -> `var user = {"username":"userA","age":10};`
* 인라인 사용 전은 객체의 toString()이 호출된 값이다.
* 인라인 사용 후는 객체를 JSON으로 변환해준다.
  

