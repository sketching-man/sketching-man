HTTP란?
HTTP는 프로토콜의 일종이며, 웹 서버와 웹 클라이언트(웹 브라우저)가 통신하는 방법을 규정한 규칙
웹 클라이언트가 웹 서버에 접근하고자 할 때 보내는 정보를 Request,
웹 서버가 웹 클라이언트의 요청에 대답하는 형식으로 보내는 정보를 Response라고 함
TCP/IP 통신 위에서 동작하며 기본 포트는 80번 (HTTPS는 443)

HTTPS란?
일반 HTTP는 HTTP - TCP - IP - Net.Interface(HW) 형태
여기서 TCP로 가기 전에 SSL encryption 한게 HTTPS 프로토콜
HTTP - SSL(or TLS) - TCP - IP - Net.Interface(HW)
HTTP 는 원래 TCP 와 직접 통신했지만, HTTPS 에서 HTTP 는 SSL 과 통신하고 SSL 이 TCP 와 통신 하게 된다.

HTTP 프로토콜은 상태가 없는(stateless) 프로토콜?
여기서 상태가 없다라는 말은 데이터를 주고 받기 위한 각각의 데이터 요청이 서로 독립적으로 관리가 된다는 의미
서버는 세션과 같은 별도의 추가 정보를 관리하지 않아도 되고, 다수의 요청 처리 및 서버의 부하를 줄임
아 물론 HTTP 1.1부터는 persistent connections 옵션이 존재함.

HTTP Request & HTTP Response
클라이언트-서버 프로토콜이란 (보통 웹브라우저인) 수신자 측에 의해 요청이 초기화되는 프로토콜을 의미
클라이언트(=브라우저)가 Request를 만들면 서버(웹서버)는 HTTP Response로 응답함

HTTP 흐름
1. TCP 연결을 엽니다:
	TCP 연결은 요청을 보내거나(혹은 여러개의 요청) 응답을 받는데 사용됩니다.
	클라이언트는 새 연결을 열거나, 기존 연결을 재사용하거나, 서버에 대한 여러 TCP 연결을 열 수 있습니다.
2. HTTP 메시지를 전송합니다:
	HTTP 메시지(HTTP/2 이전의)는 인간이 읽을 수 있습니다.
	HTTP/2에서는 이런 간단한 메시지가 프레임 속으로 캡슐화되어, 직접 읽는게 불가능하지만 원칙은 동일합니다.
	GET / HTTP/1.1
	Host: developer.mozilla.org
	Accept-Language: fr
3. 서버에 의해 전송된 응답을 읽어들입니다:
	HTTP/1.1 200 OK
	Date: Sat, 09 Oct 2010 14:28:02 GMT
	Server: Apache
	Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
	ETag: "51142bc1-7449-479b075b2891b"
	Accept-Ranges: bytes
	Content-Length: 29769
	Content-Type: text/html

	<!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
4. 연결을 닫거나 다른 요청들을 위해 재사용합니다.

요청
	메서드		경로	버전
	GET 	/ 	HTTP/1.1
헤더	Host: developer.mozilla.org
헤더	Accept-Language: fr

응답
	버전			상태코드	상태메시지
	HTTP/1.1 	200 	OK
헤더	Date: Sat, 09 Oct 2010 14:28:02 GMT
헤더	Server: Apache
헤더	Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
헤더	ETag: "51142bc1-7449-479b075b2891b"
헤더	Accept-Ranges: bytes
헤더	Content-Length: 29769
헤더	Content-Type: text/html

내용	<!DOCTYPE html... (here comes the 29769 bytes of the requested web page)

메서드 종류
GET / POST / PUT / PATCH / DELETE
POST vs PUT: https://stackoverflow.com/questions/630453/what-is-the-difference-between-post-and-put-in-http
POST는 데이터 추가의 의미가 아니라, client가 보내는 데이터를 server에서 "처리"해달라 하는 의미에 가까움. + 데이터 추가하더라도 cilent가 uri를 언급할 필요 없음
PUT은 데이터 추가의 의미 only. 특히 uri 공간에 데이터를 "추가"하겠다는 의미. 어떤 uri에 데이터 추가해야 할지 client가 언급해야 함.

참고하면 좋은 URI 설계 개념
	- 문서(document)
		단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
		예) /members/100, /files/star.jpg
	- 컬렉션(collection)
		서버가 관리하는 리소스 디렉터리
		서버가 리소스의 URI를 생성하고 관리
		예) /members
	- 스토어(store)
		클라이언트가 관리하는 자원 저장소
		클라이언트가 리소스의 URI를 알고 관리
		예) /files
	- 컨트롤러(controller), 컨트롤 URI
		문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
		동사를 직접 사용
		예) /members/{id}/delete

HTTP 캐싱이란?
캐싱은 주어진 리소스의 복사본을 저장하고 있다가 요청 시에 그것을 제공하는 기술
웹 캐시가 자신의 저장소 내에 요청된 리소스를 가지고 있다면,
요청을 가로채 원래의 서버로부터 리소스를 다시 다운로드하는 대신 리소스의 복사본을 반환
웹 사이트와 애플리케이션의 성능은 이전에 가져온 리소스들을 재사용함으로써 현저하게 향상될 수 있습니다.
웹 캐시는 레이턴시와 네트워크 트래픽을 줄여줌으로써 리소스를 보여주는 데에 필요한 시간을 줄여줍니다.

사설(private) vs 공유(shared? public?) 캐시
공유 캐시는 한 명 이상의 사용자가 재사용할 수 있도록 응답을 저장하는 캐시를 말합니다.
	서버와 클라이언트 사이에 multiple caching proxy node를 두고 여기에서 멀티 유저 프로파일을 저장
사설 캐시는 한 명의 사용자만 사용하는 캐시를 말합니다.
	일반적으로 브라우저에 저장하고 관리

모든 요청/응답이 다 저장되나?
HTTP 캐시들은 일반적으로 GET에 대한 응답만을 캐싱

쿠키란?
HTTP 쿠키(웹 쿠키, 브라우저 쿠키)는 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각
브라우저는 그 데이터 조각들을 저장해 놓았다가, 동일한 서버에 재 요청 시 저장된 데이터를 함께 전송

어디에 쓰나?
잠깐 위에서 나온 얘기를 하자면, HTTP의 characteristics 중 하나로 stateless가 있었음.
모든 요청/응답은 독립적이며, 한 번의 데이터 교환이 있는 뒤로는 연결은 유지되지 않는다.
그런데 사용자가 브라우저를 쓰다보면, 연결을 유지해야 편한 상황이 생김.
	세션 관리(Session management): 서버에 저장해야 할 로그인, 장바구니, 게임 스코어 등의 정보 관리
	개인화(Personalization): 사용자 선호, 테마 등의 세팅
	트래킹(Tracking): 사용자 행동을 기록하고 분석하는 용도
이 상황을 해결하기 위해 쿠키가 등장

쿠키 저장은 어떻게 진행되나?
1. request를 서버가 받고, 쿠키 데이터 확인하고 필요할 경우 fetch & store
2. freshness check
3. create response and send to client
4. Set-Cookie HTTP 응답 헤더가 서버로부터 사용자 에이전트로 전송됩니다.
	이 서버 헤더는 클라이언트에게 쿠키를 저장하라는 뜻이며, 브라우저는 이 정보를 저장합니다.
5. 이제, 서버로 전송되는 모든 요청과 함께,
	브라우저는 Cookie 헤더를 사용하여 서버로 이전에 저장했던 모든 쿠키들을 회신할 것입니다.

########################################################################################

REST란? (Representational State Transfer)
REST 는 Resource Oriented Architecture 이다.
API 설계의 중심에 자원(Resource)이 있고 HTTP Method 를 통해 자원을 처리하도록 설계하는 것이다.
REST는 프로토콜이나 표준이 아닌 아키텍처 원칙 세트.
"자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것"
HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고,
HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미

REST 구성 요소
1. 자원(Resource): URI
	모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.
	자원을 구별하는 ID는 ‘/groups/:group_id’와 같은 HTTP URI 다.
	Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.
2. 행위(Verb): HTTP Method
	HTTP 프로토콜의 Method를 사용한다.
	HTTP 프로토콜은 POST(create), GET(read), PUT(update), DELETE(delete) 와 같은 메서드를 제공한다.
3. 표현(Representation of Resource)
	Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답(Representation)을 보낸다.

REST 원칙
1. Server-Client(서버-클라이언트 구조): 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 된다.
2. Stateless(무상태): HTTP 프로토콜은 Stateless Protocol이므로 REST 역시 무상태성을 갖는다.
	Server는 각각의 요청을 완전히 별개의 것으로 인식하고 처리한다.
3. Cacheable(캐시 처리 가능): 캐시 사용을 통해 응답시간이 빨라지고
	REST Server 트랜잭션이 발생하지 않기 때문에 전체 응답시간, 성능, 서버의 자원 이용률을 향상시킬 수 있다.
4. Layered System(계층화): REST Server는 다중 계층으로 구성될 수 있다.
	REST Server 앞에 로드밸런싱, 공유 캐시, Proxy, Gateway 등을 통해 확장성과 보안성을 향상시킬 수 있다.
5. Code-On-Demand(optional): Server로부터 스크립트를 받아서 Client에서 실행한다.
6. Uniform Interface(인터페이스 일관성): URI로 지정한 Resource에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
	HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하고 특정 언어나 기술에 종속되지 않는다.
	제약 조건: 
		Identification of resources
		Manipulation of resources through representations
		(!) Self-descriptive messages
		(!) Hypermedia as the engine of application state(HATEOAS)
		
왜 REST함을 충족시키기 어려운가?
1) Self-descriptive message라는 것은 메시지를 봤을 때 메시지의 내용으로 온전히 해석이 다 가능해야된다는 것이다.
	GET / HTTp/1.1			<< 요것만 있다면 HTTP 요청 메시지는 뭔가 빠져 있어서 Self-descriptive 하지 못하다.
	Host: www.example.org	<< 요렇게 어디로 가는 메시지라는 정보가 있어야 메시지만 보고 이해할 수 있다.
	
	HTTP/1.1 200 OK
	Content-Type: application/json-patch+json	<< 최고의 명시. 이게 없으면 내용이 뭘 의미하는지 전혀 모르는데,
												json임을 알리고 patch+json라는 미디어타입임을 공지함
	[ { "op": "remove", "path": "/a/b/c" } ]
	
2) HATEOAS: 애플리케이션의 상태는 Hyperlink를 이용해 전이되어야 한다.
	사이트를 넘어갈 때 마다 항상 해당 페이지에 있던 링크를 따라가면서 넘어가는 식으로 구현했을 때 HATEOAS를 만족한다.
	html로 표현하면 보통 a 태그를 사용해서 만족시키기 쉬움
	json으로 표현하면 아래처럼 link라는 헤더를 사용해서 이 리소스와 하이퍼링크로 연결되어 있는 다른 리소스를 가르킬 수 있는 기능을 사용해야 함께
	HTTP/1.1 200 OK
	Content-Type: application/json
	Link: </articles/1>; rel="previous",	// 여기서 어떤 1개의 게시물을 표현 했는데, 이전의 게시물 URI가 /articles/1,
		</articles/3>; rel="next";			// 다음 게시물은 /articles/3에 있다는 정보를 표현해준 것이다.
	{
		"title": "The second article",
		"contents": "blah blah..."
	}

RESTful이란?
RESTful은 일반적으로 REST라는 아키텍처를 구현하는 웹 서비스를 나타내기 위해 사용되는 용어이다.
‘REST API’를 제공하는 웹 서비스를 ‘RESTful’하다고 할 수 있다.
RESTful하지 못한 경우는...
	Ex1) CRUD 기능을 모두 POST로만 처리하는 API
	Ex2) route에 resource, id 외의 정보가 들어가는 경우(/students/updateName)

RESTful API란?
API(Application Programming Interface)는 애플리케이션 소프트웨어를 구축하고 통합하는 정의 및 프로토콜 세트
REST 기반으로 서비스 API를 구현한 것
REST는 HTTP 표준을 기반으로 구현하므로, HTTP를 지원하는 프로그램 언어로 클라이언트, 서버를 구현 가능(델파이, 자바, C#, 웹...).

REST하게 API를 디자인한다는 것은 무엇을 의미하는가?
리소스 와 행위 를 명시적이고 직관적으로 분리한다.
	리소스는 URI로 표현되는데 리소스가 가리키는 것은 명사로 표현되어야 한다.
	행위는 HTTP Method로 표현하고, GET(조회), POST(생성), PUT(기존 entity 전체 수정), PATCH(기존 entity 일부 수정), DELETE(삭제)을 분명한 목적으로 사용한다.
Message 는 Header 와 Body 를 명확하게 분리해서 사용한다.
	Entity 에 대한 내용은 body 에 담는다.
	애플리케이션 서버가 행동할 판단의 근거가 되는 컨트롤 정보인 API 버전 정보, 응답받고자 하는 MIME 타입 등은 header 에 담는다.
	header 와 body 는 http header 와 http body 로 나눌 수도 있고, http body 에 들어가는 json 구조로 분리할 수도 있다.
API 버전을 관리한다.
	환경은 항상 변하기 때문에 API 의 signature 가 변경될 수도 있음에 유의하자.
	특정 API 를 변경할 때는 반드시 하위호환성을 보장해야 한다.
서버와 클라이언트가 같은 방식을 사용해서 요청하도록 한다.
	브라우저는 form-data 형식의 submit 으로 보내고 서버에서는 json 형태로 보내는 식의 분리보다는 json 으로 보내든, 둘 다 form-data 형식으로 보내든 하나로 통일한다.
	다른 말로 표현하자면 URI 가 플랫폼 중립적이어야 한다.
	
장점
Open API 를 제공하기 쉽다.
멀티플랫폼 지원 및 연동이 용이하다.
원하는 타입으로 데이터를 주고 받을 수 있다.
기존 웹 인프라(HTTP)를 그대로 사용할 수 있다.

단점
사용할 수 있는 메소드가 4 가지 밖에 없다.
분산환경에는 부적합하다.
HTTP 통신 모델에 대해서만 지원한다.

########################################################################################

HTTP 에러코드
1xx (정보): 요청을 받았으며 프로세스를 계속한다
2xx (성공): 요청을 성공적으로 받았으며 인식했고 수용하였다
3xx (리다이렉션): 요청 완료를 위해 추가 작업 조치가 필요하다
4xx (클라이언트 오류): 요청의 문법이 잘못되었거나 요청을 처리할 수 없다
5xx (서버 오류): 서버가 명백히 유효한 요청에 대해 충족을 실패했다

 100
 Continue 

 101
 Switching Protocols 

 200
 OK, 에러 없이 전송이 성공함. 

 202
 Accepted, 서버가 클라이언트의 명령의 받음. 

 203 
 Non-authoritative Information, 서버가 클라이언트 요구 중 일부만 전송함. 

 204  
 Non Content, 클라이언트 요구를 처리했으나 전송할 데이터가 없음. 

 205 
 Reset Content 

 206 
 Partial Content 

 300
 Multiple Choices, 최근에 옮겨진 데이터를 요청함. 

 301
 Moved Permanently, 요구한 데이터를 변경된 임시 URL에서 찾음. 

 302 
 Moved Permanently, 요구한 데이터가 변경된 URL에 있음을 명시함. 

 303 
 See Other, 요구한 데이터를 변경하지 않았기 때문에 문제가 있음. 

 304 
 Not modified 

 305 
 Use Proxy 

 400 
 Bad Request, 요청 실패 - 문법상 오류가 있어서 서버가 요청 사항을 이해하지 못함. 

 401.1 
 Unauthorized, 권한 없음 - 접속 실패. 이 에러는 서버에 로그온 하려는 요청사항이 서버에 들어있는 권한과 비교했을 시 맞지 않을 경우 발생. 이 경우, 요청한 자원에 접근할 수 있는 권한을 부여받기 위해서 서버 운영자에게 요청해야 함. 

 401.2
 Unauthorized, 권한 없음 - 서버 설정으로 인한 접속 실패. 이 에러는 서버에 로그온 하려는 요청사항이 서버에 들어있는 권한과 비교했을 때 맞지 않을 경우 발생. 이것은 일반적으로 적절한 www-authenticate head field를 전송하지 않아서 발생함. 

 402.3 
 Unauthorized, 권한 없음 - 자원에 대한 ACL에 기인한 권한 없음. 이 에러는 클라이언트가 특정 자원에 접근할 수 없을 때 발생. 이 자원은 페이지가 될 수 있고, 클라이언트의 주소 입력란에 명기된 파일일 수도 있다. 또한, 클라이언트가 해당 주소로 접속할 때 이용되는 또 다른 파일일 수도 있다. 접근할 전체 주소를 다시 확인해 보고 웹 서버 운영자에게 여러분이 자원에 접근할 권한이 있는지를 확인함. 

 401.4 
 Unauthorized, 권한 없음 - 필터에 의한 권한 부여 실패. 이 에러는 웹 서버가 서버에 접속하는 사용자들을 확인하기 위해 설치한 필터 프로그램이 있음을 의ㅣ함. 서버에 접속하는데 이용되는 인증 과정이 이런 필터 프로그램에 의해 거부된 것임. 

 404.5 
 Unauthorized, 권한 없음 - ISA PI/CGI 어플리케이션에 의한 권한 부여 실패. 이 에러는 이용하려는 웹 서버의 어드레스에 ISA PI나 CGI 프로그램이 설치 되어 있어 사용자의 권한을 검증함. 서버에 접속하는데 이용되는 인증 과정이 이 프로그램에 의해 거부됨. 

 402 
 Payment Required, 예약됨 

 403.1 
 Forbidden, 금지 - 수행 접근 금지. 이 에러는 CGI나 ISA-PI, 혹은 수행시키지 못하도록 되어 있는 디렉터리 내의 실행 파일을 수행시키려고 했을 때 발생함. 

 403.2 
 Forbidden, 금지 - 읽기 접근 금지. 이 에러는 브라우저가 접근한 디렉터리에 가용한 디폴트 페이지가 없을 경우에 발생함. 

 403.4 
 Forbidden, 금지 - SSL 필요. 이 에러는 접근하려는 페이지가 SSL로 보안 유지되고 있는 것일 때 발생함. 

 403.5 
 Forbidden, 금지 - SSL 128이 필요. 이 에러는 접근하려는 페이지가 SSL로 보안 유지되고 있는 것일 때 발생. 브라우저가 128비트의 SSL을 지원하는지를 확인해야 함. 

 403.6
 Forbidden, 금지 - IP 주소 거부됨. 이 에러는 서버가 사이트에 접근이 허용되지 않는 IP주소로 사용자가 접근하려 했을 때 발생함. 

 403.7 
 Forbidden, 금지 - 클라이언트 확인 필요. 이 에러는 접근하려는 자원이 서버가 인식하기 위해서 브라우저에게 클라이언트 SSL을 요청ㅎ하는 경우 발생함. 자원을 이용할 수 있는 사용자임을 입증하는데 사용됨. 

 403.8 
 Forbidden, 금지 - 사이트 접근 거부. 이 에러는 웹 서버가 요청사항을 수행하고 있지 않았거나, 해당 사이트에 접근하는 것을 허락하지 않았을 경우에 발생함. 

 403.9
 Forbidden, 접근 금지 - 연결된 사용자수 과다. 이 에러는 웹 서버가 busy한 상태에 있어서 요청을 수행할 수 없을 경우에 발생함. 

 403.10 
 Forbidden, 접근 금지 - 설정이 확실하지 않음. 이 에러는 웹 서버의 설정 부분에 문제가 있을 경우 발생함.

 403.11 
 Forbidden, 접근 금지 - 패스워드 변경. 이 에러는 사용자 인증 단계에서 잘못된 패스워드를 입력했을 경우 발생함. 

 403.12 
 Forbidden, 접근 금지 - Mapper 접근 금지. 이 에러는 클라이언트 인증용 맵(map)이 해당 웹 사이트에 접근하는 것을 거부할 경우에 발생함. 
 
 404
 Not Found, 문서를 찾을 수 없음 - 이 에러는 클라리언트가 요청한 문서를 찾지 못한 경우에 발생함. URL을 다시 잘 보고 주소가 올바로 입력되었는지를 확인함. 

 405 
 Method not Allowed, 메소드 허용안 됨 - 이 에러는 Request 라인에 명시된 메소드를 수행하기 위한 해당 자원의 이용이 허용되지 않았을 경우에 발생함. 

 406 
 Not Acceptable, 받아들일 수 없음 - 이 에러는 요청 사항에 필요한 자원은 요청 사항으로 전달된 Accept header에 따라 "Not Acceptable" 내용을 가진 사항이 있을 경우에 발생함. 

 407 
 Proxy Authentication Required, Proxy 인증이 필요함 - 이 에러는 해당 요청이 수행되도록 proxy 서버에게 인증을 받아야 할 경우에 발생함. 

 408
 Request timeout, 요청 시간이 지남 

 409
 Confict

 410 
 Gone, 영구적으로 사용할 수 없음. 

 411
 Length Required 

 412 
 Precondition Failed, 선결조건 실패 - 이 에러는 Request-header field에 하나 이상에 선결 조건에 대한 값이 서버에서의 테스트 결과 false로 나왔을 경우에 발생. 

 413
 Percondition Failed, 선결조건 실패 - 이 에러는 Request-header field에 하나 이상에 선결 조건에 대한 값이 서버에서의 테스트 결과 false로 나왔을 경우에 발생. 

 414
 Request-URI too long, 요청한 URI가 너무 김 - 이 에러는 요청한 URI의 길이가 너무 길어서 서버가 요청 사항의 이행을 거부했을 경우 발생 

 415 
 Unsupported media type 

 500
 Internal Server Error, 서버 내부 오류 - 이 에러는 웹 서버가 요청사항을 수행할 수 없을 경우에 발생함. 

 501
 Not Implemented, 적용 안 됨 - 이 에러는 웹 서버가 요청사항을 수행하는데 필요한 기능을 지원하지 않는 경우에 발생. 

 502 
 Bad gateway, 게이트웨이 상태 나쁨 - 이 에러는 게이트웨이 상태가 나쁘거나 서버의 과부하 상태일 때 발생한다. 

 503 
 Service Unavailable, 서비스 불가능 - 이 에러는 서비스가 현재 멈ㅊ춘 상태 또는 현재 일시적인 과부하 또는 관리 상황일 때 발생될 수 있다. 

 504
 Gateway timeout 
 
 505
 HTTP Version Not Supported 