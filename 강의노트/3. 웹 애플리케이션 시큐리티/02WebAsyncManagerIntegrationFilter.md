# Async 웹 MVC를 지원하는 필터 WebAsyncManagerIntegrationFilter

- 필터 중 가장 위에있는 필터
- 원래는 ThreadLocal 이라는 컨텍스트를 사용하여 동일한 쓰레드에서만 데이터를 공유한다.
- 다른 쓰레드에서도 동일한 Security Context를 사용할 수 있게 해주는 필터.

## 사용 방법

- Request 메소드에서 리턴 값을 `Callable<String>`
- `Callable<String>() {}` 함수 안에 있는 로직을 처리하기 전에 리퀘스트를 처리하는 스레드를 반환(release)한다.
- 안에서 하는 일이 완료될 때 응답을 내보낸다. 

```
    @GetMapping("/async-handler")
    @ResponseBody
    public Callable<String> asyncHandler() {
        
        System.out.println("MVC");
        Thread thread = Thread.currentThread();
        System.out.println("Thread: " + thread.getName());
        Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
        System.out.println("Principal: " + principal);

        return new Callable<String>() -> {
            System.out.println("Callable");
            Thread thread = Thread.currentThread();
            System.out.println("Thread: " + thread.getName());
            Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            System.out.println("Principal: " + principal);

            return "Async Handler";
        };
    }

```
- 컨텍스트, 스레드, 현재 위치 `MVC`
- `"MVC"` 관련 까지 처리해주는 스레드는 톰캣이 할당해주는 nio 스레드일 것.
- `Callable` 함수 안에는 별도의 스레드일 것.
- 스레드는 다르지만 컨텍스트 정보는 동일한 것을 확인하는 것.