## CSRF
CSRF는 악의적인 웹사이트에서 사용자의 인증된 세션을 이용해 의도하지 않은 요청을 보내는 공격 방식으로 
CSRF 공격을 방지하기 위해 사용자의 요청이 진짜 웹사이트에서 생성된 것인지 검증하는 고유한 식별자 토큰인 CSRF Token을 사용한다. 
CSRF Token은 클라이언트의 요청이 신뢰할 수 있는 출처에서 왔는지 확인하는데 사용된다.

### CSRF Token
사용자가 특정 요청을 보낼 때마다 서버는 고유한 CSRF Token을 생성하여 클라이언트에 제공하며 CSRF Token은 세션과 연결되거나, 요청마다 다르게 생성된다.

### CSRF 전송 과정

1. 서버가 CSRF Token을 생성하여 클라이언트에 제공 (HTML 폼의 hidden 필드에 포함해서 보냄):
  CSRF Token을 서버에서 생성한 후 클라이언트에게 보냄으로써 클리이언트는 특정 요청을 보낼때 데이터와 함께 CSRF Token을 서버에 보낼 수 있다.
  클라이언트가 요청한 데이터를(from), hidden 필드 및 HTTP 헤더를 통해서 CSRF Token을 생성하면 악용 하는 사용자는 CSRF Token을 알 수 있는 방법이 없고 

2. 클라이언트가 CSRF Token을 서버로 전송 : 
  클라이언트는 서버로 요청을 보낼 때 발급 받은 CSRF Token을 요청 데이터와 함께 서버로 전송하여 서버에서 해당 요청이 신뢰할 수 있는 클라이언트인지 유효성을 검증하고
  요청을 수행하고 만약 CSRF Token이 유효하지 않으면 요청을 거부한다.


### CSRF ToKen 특징

1회성 및 고유성 :
  생성된 CSRF Token 토큰은 다른 사용자에게 복사되거나 재사용될 수 없다.

서버에서 생성 및 관리 :
  서버는 CSRF Token을 세션과 연결하여 관리하며, 만료된 토큰이나 일치하지 않는 토큰은 거부한다.

사용자에게 숨겨진 전달 방식:
  CSRF Token은 일반적으로 사용자에게 보이지 않는 방식으로 전달된다. (HTML 폼의 hidden input 필드 또는 HTTP 헤더)


### CSRF Token의 사용 방법

1. HTML 폼의 hidden 필드로 CSRF Token 전달 서버가 CSRF Token을 HTML 폼에 추가 하는 방법

```html
<form action="/transfer" method="POST">
  <input type="hidden" name="_csrf" value="generated_csrf_token_here" />
  <input type="text" name="to" placeholder="Recipient">
  <input type="number" name="amount" placeholder="Amount">
  <button type="submit">Transfer</button>
</form>
```
___

2. Ajax 요청을 통해 HTTP 헤더로 CSRF Token 전달 하는 방법 (Ajax 요청의 경우, CSRF Token을 HTTP 헤더에 포함시켜 전송한다.)

```javascript
javascript
코드 복사
fetch('/transfer', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-TOKEN': 'generated_csrf_token_here'
  },
  body: JSON.stringify({ to: 'recipient', amount: 100 })
});
```


## CSRF 공격 이해를 위한 시나리오

1. 신뢰할 수 있는 웹사이트에 로그인
  사용자는 은행, 소셜 미디어 등 신뢰할 수 있는 웹사이트(example-bank.com)에 로그인
  사용자가 example-bank.com에 로그인하면, 서버는 사용자를 인증하고 세션을 생성
  세션은 브라우저의 쿠키에 저장. (쿠키는 브라우저가 자동으로 서버에 전송하는 작은 데이터)

예시 : 사용자가 자신의 계좌에서 돈을 이체하기 위해 example-bank.com/transfer 페이지에 접근
      이때 서버는 사용자가 인증된 상태임을 확인하기 위해 세션 쿠키를 사용

2. 악의적인 웹사이트 방문
사용자는 공격자가 만든 악의적인 웹사이트(malicious-site.com)를 방문.(이 사이트는 공격자가 조작한 HTML 폼, 스크립트, 또는 이미지 태그 등을 포함하고 있다.)

예시 : 악의적으로 만든 웹사이트의 HTML 코드에서 from으로 탈취할 페이지의 주소를 작성하여 
      탈취할 페이지에서 사용자가 이미 인증한 데이터를 바탕으로 악용한다.

<form action="http://example-bank.com/transfer" method="POST">
    <input type="hidden" name="to" value="attacker_account" />
    <input type="hidden" name="amount" value="10000" />
    <button type="submit" style="display:none;">Submit</button>
</form>
<script>
    // 폼 자동 제출
    document.forms[0].submit();
</script>

3. 악의적인 요청 자동 실행
malicious-site.com은 사용자 브라우저를 이용해, 신뢰할 수 있는 웹사이트(example-bank.com)에 요청을 보냄
이 요청은 보통 사용자의 브라우저에서 자동으로 실행되며, HTTP GET 요청이나 POST 요청의 형태를 취한다.

예시 : 공격자는 사용자 몰래 이미지 태그로 아래와 같은 요청을 실행하도록 웹사이트를 조작 
(HTML의 img 태그는 페이지가 로드될 때, 브라우저가 자동으로 이미지를 불러오기 위한 GET 요청을 실행.)

```html
<img src="http://example-bank.com/transfer?to=attacker&amount=10000">
```

브라우저는 HTTP GET 요청을 실행하면 자동으로 세션 쿠키를 포함하기 때문에 공격자는 GET 요청으로 받은 사용자의 권한으로 서버에서 실행할 수 있게 된다.

4. 세션 쿠키의 악용
브라우저는 신뢰할 수 있는 웹사이트(example-bank.com)에 대한 요청 시 세션 쿠키를 자동으로 포함하며 사용자 인증에 사용되므로, 
서버는 요청이 사용자로부터 온 것으로 간주한다. 하여 서버는 요청을 허용하고, 사용자의 계좌에서 공격자가 지정한 계좌로 돈을 이체할 수 있게된다.
즉, 서버는 요청이 브라우저로부터 왔기 때문에 신뢰하지만, 요청이 사용자의 의도인지 확인할 방법이 없다.
따라서 공격자는 사용자가 모르는 사이에 사용자의 권한을 이용해 악의적인 작업을 실행할 수 있다.

5. 결과
공격자는 사용자의 인증 세션을 이용해 이익을 취하고 사용자는 악의적인 요청이 실행되었음을 알지 못한 채, 자신의 자산(예: 돈)을 잃게 된다.

