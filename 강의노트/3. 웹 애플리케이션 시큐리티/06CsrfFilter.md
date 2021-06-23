# CsrfFilter

- **Cross Site Request Forgery** 어택:  내가 원치 않는 요청을 임의로 만들어서 보냄
  - Ex) 은행 사이트와 공격 사이트를 동시에 켜놓고, 공격 사이트에서 어떤 정보를 클릭하면 계좌 전송 요청!
- Thymeleaf 2.1 버젼 이상일 때 `<form>` 태그를 쓰면 csrf 자동 생성.


## CORS

- Cross-origin Resource Sharing

- 방법 1. (안전함) 인증서로 양방향 인증을 한 다음에 통신
- 방법 2. (간단) 특정 타 도메인에게 리소스를 제공하도록 허용.
  
## CsrfFilter

- 리소스를 변경하는 요청은 서버에서 발급한 토큰을 체크.
- CsrfFilter 에서 토큰을 발급해준다.
- 공격자가 만들어낸 Form 에는 Csrf 토큰이 있을 수 없다.

**CsrfFilter.java**
```
    request.setAttribute(CsrfToken.class.getName(), csrfToken);
    request.setAttribute(CsrfToken.getParameterName(), csrfToken);
```
- 요청을 보낼 때 토큰 부여
  
```
    String actualToken = request.getHeader(csrfToken.getHeaderName());
```
- Csrf 토큰 일치하는지 확인

```
    if(!this.requireCsrfProtectionMatcher.matches(request))
```
- Post 요청일 경우에만 처리한다.
- 인증에 실패하면 `accessDeniedHandler`에세 유효한 요청이 아님을 응답으로 보냄.
- 포스트맨으로 Post 요청을 보내면 `402 Unauthorized`

## Form csrf 확인 절차

1. 로그인 페이지 접근
2. 서버에서 CsrfToken 세팅을 함
3. 페이지에서 `<input name="_csrf" type="hidden" value="토큰값">` 으로 페이지에서 확인할 수 잇다. 폼에 대한 input 인것을 확인.
4. 폼 요청이 오면 서버에서 실제 발급한 토큰 값과 일치하는지 확인.

## 설정 Off

```
    http.csrf().disable();
```

## Test 작성

- Form 을 요청하는 테스트와 보내는 테스트가 나뉘어 있을 때 csrf 테스트는 어떻게 하나?
  - csrf 없으면 `404 Forbidden`
  - `.with(csrf())` 적으면 된다.