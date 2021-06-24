# @AuthenticationPrincipal

- `ArgumentResolver`: 스프링 시큐리티가 제공하는 기능. 
- 그 중에 `AuthenticationPrincipal`: 로그인한 정보를 파라미터롤 받고싶을 때 Java 스탠다드 에디션 (`java.security.`)에 있는 `Principal`를 쓸 수 있다.
- 또는 `SecurityContextHolder.getContext().getAuthentication().getPrincipal()`을 쓸 수 있는데, **둘은 다르다.**

**컨트롤러 핸들러**
```
    public String index(MOdel model, Principal principal) {}
```
- 여기서의 `Principal`은 `.getName()` 같은 제한적인 것 밖에 제공되지 않는다.
  - 스프링이 아닌 java 에서 제공하는 것이다.
- 요청이 들어왔을 때 로그인한 사용자에 대한 정보를 `Principal`가 아닌 우리 도메인에서 제공하는 `Account`로 나타내고싶다.

## 커스텀한 유저를 만든다

**UserAccount**
```
public class UserAccount extends User {

    private Account account;

    public UserAccount(Account account) {
        super(account.getUsername(), account.getPassword(), List.of(new SimpleGrantedAuthority("ROLE_" + account.getRole())));
        this.account = account;
    }

    public Account getAccount() {
        return account;
    }
}

```
- Spring Security 가 제공하는 `User`를 상속받는다.
- 이 클래스를 `Account`만 가지고 만든다.
- `List.of()`: Java language 버젼을 11 이상으로 해야한다.
- `Account`에 접근할 수 있게 `getter`를 만들었다.

- `public class Account extends User {}` 로 클래스를 만들수도 있지만, 도메인에 기술적인 디펜던시를 제거하기 위해 분리해서 `UserAccount`를 만들었다.
  - 또는 `public class Account implements UserDetails`

**UserDetailsService**
```
        return new UserAccount(account);
```

## 컨트롤러 세팅

- 컨트롤러에서 `@AuthenticationPrincipal` 어노테이션으로 파라미터를 `Principal`에 해당하는 객체를 받을 수 있다.

```
    @GetMapping("/")
    public String index(Model model, @AuthenticationPrincipal UserAccount userAccount) {
        if (userAccount == null) {
            model.addAttribute("message", "Hello Spring Security");
        } else {
            model.addAttribute("message", "Hello, " + userAccount.getUsername());
        }

        return "index";
    }
```

- `Account`로 바로 받는 방법도 있다.

```
    @GetMapping("/")
    public String index(Model model, @AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account") Account account) {
        if (account == null) {
            model.addAttribute("message", "Hello Spring Security");
        } else {
            model.addAttribute("message", "Hello, " + account.getUsername());
        }

        return "index";
    }
```
- `Principal`안에있는 객체를 특정 객체로 받아오거나 다른 객체로 읽어오도록 할 수 있다.
- `#this`: 현재 객체가 익명이라면 null 아니면 `Principal` 안에 들어있는 account를 꺼내준다.
- 문제는 이게 너무 길다 코드가.

## @CurrentUser 어노테이션 생성

```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PARAMETER)
@AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account")
public @interface CurrentUser {
}
```
- 인터페이스를 만들고 긴 어노테이션을 넣어준다.

```
public String index(Model model, @CurrentUser Account account) {
```