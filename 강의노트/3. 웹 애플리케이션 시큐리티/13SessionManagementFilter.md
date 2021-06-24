# 세션 관리 필터: SessionManagementFilter

**기능들**
1. 세션 변조 방지 전략 설정 **sessionFixation**
2. 유효하지 않은 세션 리다이렉트 시킬 URL 설정


## 세션 변조 방지 전략

- **세션 변조**:

![image](https://user-images.githubusercontent.com/83999058/123150168-042dda80-d49d-11eb-8f85-31a6c959c287.png)

1. 공격자가 웹사이트에 로그인해서 쿠키(세션 ID)를 받아온다.
1. 공격자가 타겟에세 쿠키를 보내서 그 사람이 쿠키를 사용해서 서버에 접근한다.
1. 그 세션 ID 를 가지고 로그인 한다.
1. 공격자가 쿠키를 가지고 개인정보를 볼 수 있다.

- **공격 방지**
1. 희생자가 로그인을 하면 세션을 새로 발급해준다.
2. 쿠키가 다르기 떄문에 공격자가 접근할 수가 없다.

- 방지 전략은 Servlet 컨테이너에 따라서 달라진다.
  - 서블릿 `3.1+`: 세션 ID 바꾸는 것 지원 **chagneSessionId**
    - migrate 보다 좀 더 빠를 것이다.
  - 서블릿 `3.0-`: migrate 세션이라는 전략을 사용 **migrateSession**
    - 인증이 됐을 때 새션을 새로 만들고, 기존에 세션에 있던 attribute(key, value) 들을 복사해온다 
  - 톰캣 9.0.x 이상: 서블릿 4.0 스프링부트 2.1
  - 톰캣 8.5.x 이상: 서블릿 3.1
  
```
    http.sessionManagement()
            .sessionFixation() 
            .none() // 방지 하지 않겟다.
            .newSession() // 새로 만들겠다 ..? 쓸 일 없다.
            .migrateSession()
            .changeSessionId() // 굳이 설정할 필요 없다
```

## 유효하지 않은 세션 리다이렉트

- 로그아웃 했을 때 기존의 세션을 invalidate 시킨다.

```
    http.sessionManagement()
            .invalidSessionUrl("/login")
```
- `.invalidSessionUrl("")`: 유효하지 않은 세션을 어디로 보낼 것인가.


## 동시성 제어

1. **새로운 로그인을 막는 방법**
2. **기존 세션을 Invalidate하는 방법**


- 기본으로 한 계정이 동시에 무한대로 로그인할 수 있다. (chrome, firefox...)
  - 이것을 못하게 하고싶으면 `true`. 기본값: `false`

```
    http.sessionManagement()
            .maximumSessions(1)
                .maxSessionsPreventsLogin(false)
                .expiredUrl()
```
- `.maximumSessions(1)`: 한 계정에 대해서 동시에 1개의 세션만 유지하고 싶을 때.
- `.maxSessionsPreventsLogin()`:
  - `false`: 기본값. 다른 브라우저에서 로그인하면 기존 세션이 만료가 된다.
  - `.expiredUrl()`: 만료가 됐을 떄 어디로 보낼 것인가.
  - `true`: 새로운 새션을 로그인 못하게 하고싶다.


## 세션 생성 전략

**SessionCreationPolicy**
1. `IF_REQUIRED`: 기본값. 필요할 때 만듬
2. `NEVER`: 스프링 시큐리티에서는 만들지 않고 기존에 있다면 세션을 사용하겠다.
   - 세션을 안쓰는게 아니라 대부분은 이미 있는 것을 쓴다.
3. `STATELESS`: 정말로 세션을 쓰고싶지 않을 때.
   - form 기반에서 적절하지 않다. 
   - 로그인하고 `/dashboard` 로 가면 또 로그인하게 한다.
   - `/dashboard` 를 통해서 로그인했는데도, `/` 루트로 돌아가게 된다. 세션을 써야한다.
     - `RequestCacheAwareFilter`가 저장한 곳으로 가는데 세션을 참고한다.
4. `ALWAYS``

```
    http.sessionManagement()
            .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
```

## 여러개의 서버에 세션을 공유하고 싶다

**Spring Session** 을 따로 사용한다. .... Session Cluster