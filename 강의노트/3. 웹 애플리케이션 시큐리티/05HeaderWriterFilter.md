# HeaderWriterFilter

- 직접 설정할 일이 없는 필터
- 응답 헤더에 Security 관련된 정보를 추가해주는 필터

응답 헤더에 시큐리티 관련 헤더를 추가해주는 필터
   * XContentTypeOptionsHeaderWriter: 마임 타입 스니핑 방어.
   * XXssProtectionHeaderWriter: 브라우저에 내장된 XSS 필터 적용.
   * CacheControlHeadersWriter: 캐시 히스토리 취약점 방어.
   * HstsHeaderWriter: HTTPS로만 소통하도록 강제.
   * XFrameOptionsHeaderWriter: clickjacking 방어.

```
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Content-Language: en-US
Content-Type: text/html;charset=UTF-8
Date: Sun, 04 Aug 2019 16:25:10 GMT
Expires: 0
Pragma: no-cache
Transfer-Encoding: chunked
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
```
- 첫 페이지 접근했을 때 보이는 헤더 정보들
- `X-Content-Type-Options: nosniff`: 으로 주면 반드시 지정된 MIME 타입으로만 `Content-Type: text/html;charset=UTF-8` 렌더링하게 된다.
  - 그러면 브라우저에서 어떤 실행이 일어나지 않는다. 예를들면 다운로드 받는다던지.
- `X-XSS-Protection: 1; mode=block`: 브라우저에서 제공하는 XSS 방어 on (`1`).
  - LUCYXSS: 네이버에서 공개한 것도 추가 적용하는 걸 고민해볼 수 있다.
- `Cache-Control: no-cache, no-store, max-age=0, must-revalidate`: `CacheControlHeadersWriter`으로 설정됨.
  - 캐시를 쓰지 않도록 설정. 
  - 캐시를 사용하는 것은 정적인 리소스를 사용할 때 성능이 상향된다.
  - 서버사이드에서 실행하고 만들어주는 페이지는 동적인데, 이런 페이지에는 민감한 정보가 노출되어 캐시에 남아있을 수 있음.
    - 캐시를 지우면 다른 사람이 브라우저에 있는 정보로 해킹을 방지
- `X-Frame-Options: DENY`: iframe이나 object를 넣으면 보이지 않는 영역에 정보가 들어올 수 있다. 그것을 방지
  - 클릭 재킹을 방어.