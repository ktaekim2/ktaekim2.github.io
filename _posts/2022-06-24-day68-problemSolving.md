---
layout: single
title: "68일차-스프링부트(5)-Interceptor, BoardProject-문제해결"
---

# Interceptor-로그아웃 기능 에러
## 문제
인터셉터 기능 적용 후에 로그아웃을 하면 인덱스로 가도록 `return "redirect:/"`를 하였는데, 로그아웃 링크를 누르면 계속 로그인 페이지로 이동하는 문제가 발생.

## 원인

```java
@GetMapping("/logout")
public String logout(HttpSession session) {
    session.invalidate();
    session.removeAttribute("loginEmail");
    return "redirect:/";
}
```
>`session.invalidate()`로 이미 모든 세션을 무효화 했는데, `session.removeAttribute("loginEmail")`로 다시 한 번 세션 삭제를 시도해서, error가 뜸 -> 그 에러 페이지를 WebConfig.java에서 `.excludePathPatterns`에 주소값 예외처리가 되어있지 않아서 로그인 페이지로 이동하게 된것임.

## 해결

```java
@GetMapping("/logout")
public String logout(HttpSession session) {
    session.invalidate();
    return "redirect:/";
}
```
> `session.removeAttribute("loginEmail")` 코드 삭제

### WebConfig.java

```java
.excludePathPatterns("/", "/member/save-form", "/member/login-form", "/member/login",
                        "/member/save", "/member/logout", "/member/duplicate-check", "/js/**",
                        "/css/**", "/*.ico", "/error", "/images/**", "/favicon/**")
```
> 발생할 수 있는 다양한 경로에 예외처리를 추가

# BoardProject-404에러
## 문제
save 기능 완성 후 글 작성 페이지로 이동하니 404에러가 뜸.

## 원인
처음 프로젝트를 만들 때 패키지 이름을 "com.its.board"로 해야 하는데 실수로 "com.its.board.Board_20220624"로 만들어서 "Board_20220624"라는 하위 디렉토리가 생겼고 그 안에 "Board20220624Application"파일이 들어가게 되면서 생긴 문제였다.

## 해결

"Board20220624Application" 파일을 "board" 디렉토리로 옮기고 잘못 만들어진 "Board_20220624" 디렉토리는 삭제하였다.