# 스프링 시큐리티와 @Async

- async 한 핸들러(컨트롤러)가 아닌 서비스를 호출하는 경우.

**핸들러 코드**
```
        sampleService.asyncService();
        SecurityLogger.log("MVC, after async service");
```
- 이 두 코드는 순서가 보장되지 않는다.
- Asynce 메소드의 결과를 기다리지 않고 실행된다.

```
    @Async
    public void asyncService() {
        SecurityLogger.log("Async Service");
        System.out.println("Async service is called.");
    }
```
- `@Async` 어노테이션을 주면 별도의 스레드를 만들어서 비동기적으로 호출한다.
- 어노테이션만 준다고 해서 쓰레드가 생성되지 않는다. 쓰레드가 같다.
- **`@EnableAsync` 어노테이션을 스프링부트 어플리케이션에 부여해야 한다.
  - 쓰레드 풀에 대한 설정도 해야한다.

**결과**
- 핸들러에서 호출한