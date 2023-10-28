## Conditional GET
- 클라이언트는 이전에 한번 요청해서 돌려받은 리소스에 대해 다시 한번 요청을 할 때, 불필요한 트래픽을 줄이기위해 해당 리소스가 변경된 경우에만 다시 보내달라고 요청할 수 있다.
- Conditional Get을 이용해 트래픽을 줄일 수는 있지만, Round Trip은 줄일 수 없다.
- 어떤 리소스에 변경이 있었는지 확인하려면 반드시 Origin 서버에 요청을 보내고 응답이 올때까지 기다려야만 한다.

### Last-Modified
- Last-Modified 생성 권한은 서버가 갖고 있다.
- Last-Modified는 Date보다 이후여서는 안된다. (MUST NOT)
- Last-Modified는 Date를 계산한 시점과 최대한 가까운 시점에서 얻은 것이어야 한다. (SHOULD)
- 서버는 가능한 항상 Last-Modified 헤더를 보내야 한다. (SHOULD)

**동작 과정**
1. 요청을 보낸다.
1. 클라이언트에서는 Last-Modified 응답 헤더를 받는다.
1. 요청에 If-Modified-Since 헤더에 캐시된 마지막 수정일을 보낸다.
1. 서버에서 If-Modified-Since 헤더를 보고, 변경 일시가 클라이언트가 알고 있는 것과 같은 경우 -> 바디가 없다
1. 최근 변경 일시가 클라이언트가 알고 있는 것과 다른 경우 -> 바디를 준다, Last-Modified 헤더에 최근 변경 일시 값을 준다.

**요청 메시지**
```
GET /index.html HTTP/1.1
If-Modified-Since: Sat, 2 Feb 2013, 20:30:00 GMT
```

**응답 메시지**
- 최근 변경 일시가 클라이언트가 알고있던 것과 **같은** 경우의 응답 메시지
```
HTTP/1.1 304 NOT Modified
Date: Wed, 14 Feb 2013 06:25:24 GMT
```

- 최근 변경 일시가 클라이언트가 알고있던 것과 **다른** 경우의 응답 메시지
```
HTTP/1.1 200 OK
Content-Length: 55
Contenty-Type: text/html; charset=UTF-8
Date: Wed, 14 Feb 2013 06:25:24 GMT
Last-Modified: Sun, 3 Feb 2013 16:00:00 GMT <- 새 최근 변경 일시

<body 생략>
```

### ETag
- ETag 생성 권한은 서버가 갖고 있다.
- Strong ETag : 리소스가 조금이라도 바뀌면 바뀌어야 한다.
- Weak ETag : 리소스에 의미있는 수준의 변화가 없다면 바뀌지 않을 수 있다.

**동작 과정**
1. 요청을 보낸다.
1. 응답 헤더에 Etag 값을 보낸다.
1. 요청 메시지에 캐시된 문서의 ETag값을 If-None-Match 헤더에 담아 보낸다.
1. 서버에서 If-None-Match값을 비교하여 변경되지 않았으면, 다른 곳을 봐라, 캐시를 보라는 의미로 300번대, 304 Not Modified를 응답한다.
1. ETag 클라이언트가 알고 있는 것과 다른 경우, 200 OK응답과 새 ETag와 함께 바디를 준다.

**요청 메시지**
```
GET /index.html HTTP/1.1
If-None-Match: "a6f305a"
```

**응답 메시지**
- ETag가 클라이언트가 알고있던 것과 같은 경우의 응답 메시지
```
HTTP/1.1 304 NOT Modified
Date: Wed, 14 Feb 2013 06:25:24 GMT
```

- ETag가 클라이언트가 알고있던 것과 다른 경우의 응답 메시지
```
HTTP/1.1 200 OK
Content-Length: 55
Contenty-Type: text/html; charset=UTF-8
Date: Wed, 14 Feb 2013 06:25:24 GMT
Etag: "abcde74" <- 새 ETag

<body 생략>
```

## Cache
- Conditional get을 해도 일단, 클라이언트가 서버에 요청을 보내서 리소스가 변경되었는지 요청을 보내고, 응답이 올 때 까지 기다려야 한다.
- 한 번 왔다 갔다 하는 작업도 비용이 들기 때문에 클라이언트와 서버 사이의 오가는 HTTP 메시지를 저장한 후 재활용하여, 요청의 반응시간과 네트워크의 트래픽을 모두 줄여주는 기술이다.
- Cache-Control과 Expire 헤더를 사용하여 라운드 트립을 하지 않고, 캐시된 리소스를 응답할 수 있다.

### 재검사(Revalidation)
- 웹 서버 콘텐츠는 변경될 수 있기 떄문에 캐시는 반드시 그들이 갖고 있는 사본이 여전히 최신인지 서버를 통해 때떄로 점검해야 한다.
- 이러한 신선도 검사를 HTTP 재검사라고 부른다.

### 캐시 제어(Cache Control)
- HTTP는 문서가 완료되기 전까지 얼마나 오랫동안 캐시될 수 있게 할 것인지 서버가 설정할 수 있는 여러 가지 방법을 제공한다.
- Cache-Control: no-store
  - no-store가 표시된 응답은 캐시가 그 응답의 사본을 만드는 것을 금지한다.
- Cache-Control: no-cache
  - 진짜 이 캐시 써도 되냐고 서버에 물어봐야 한다. 재검사 필수!
- Cache-Control: must-revalidate
  - 만료 정보를 엄격하게 따르도록 설정
- Cache-Control: max-age
  - max-age같은 문서의 최대 나이를 정의한다.
  - 최대 나이는 문서가 처음 생성된 이후부터(Date), 제공하기엔 더 이상 신선하지 않다고 간주될 때까지 경과한 시간의 합법적인 최댓값이다.
- Expires
  - 절대 유효 기간을 명시한다.

## 참고
- [[HTTP의 이해] 2. Conditional Get / Caching](https://blanco10.tistory.com/3)
- [[HTTP]conditional get과 Cache](https://bohyeon-n.github.io/deploy/http/contditional-get-cache.html)