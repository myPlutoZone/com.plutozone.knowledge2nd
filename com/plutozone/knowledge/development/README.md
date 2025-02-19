# com.plutozone.knowledge.development


## Standard Guide for Development since 2005-04-11
- Common
	- Workspace vs. Project vs. Package vs. Class
  	- 저작권(Copyright)과 주석 at File, Class, Method, Attribute
   	- TODO Comment for 필수, 개선 그리고 향후
   	- Code Rule for Insert, Update, Delete
	- Tab vs. Space for File Size and Usage
	- 로컬 개발 환경 설정(Static vs. Dynamic Resouce)
	- 효율적 기준 정보(예: 코드와 값)와 입력값(예: 로그인 아이디와 암호, 회원 가입 정보) 관리
- Framework for MVC
  	- Dependency for Spring Web(Spring Version + Java Version) vs. Spring Boot(Spring Boot Version + Spring Version + Java Version)
  	- MVC = M(Service + Dao) + V(View) + C(Controller)
  	- [Controller] CRUD Page(list, writeForm, writeProc, view, modifyForm, modifyProc, remove, ...)
  	- [Controller] Frist Error for View and try/catch/finally
  	- [Controller] Service Message for Write, Update, ... and Redirect or Forward
  	- [Service] System Transation vs. User Transaction
  	- Componet Name(ex: @Controller("com.plutozone.common.controller.SystemExceptionCtrl) at Spring Object
  	- Session or Token vs. Filter vs. AOP vs. Interceptor 그리고 방어 코드
  	- Default Error Page(ex: Tomcat) vs. User Defined Error Page for 404, 500, ...
  	- Static and Dynamic Properties
  	- Logging(DEBUG, INFO, ...)
  	- Support Multi Database and Language
- Java
	- Variable Initialization(Primitive vs. Object) at DTO/VO
	- RESTful(POST, GET, PUT, DELETE, HEAD, ...) API vs. HTTP only POST API
- JSP
- JavaScript
	- 난독화	
- HTML
	- POST vs. GET
- CSS
  	- 난독화	
	- 기본적으로 속성들은 스페이스(Space)로 분리하지만 include일 경우 스페이스(Space)를 생략(예: border:1px solid red;text-aling:center;)한다. 특히 min 파일일 경우


## Co-Work at Web Development
- Web Developer vs. 사업
- Web Developer vs. 기획
- Web Developer vs. 디자인
- Web Developer vs. Publisher or App Developer or DBA or SE
