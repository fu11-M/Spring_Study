## CSRF Token이란?
CSRF Token은 Cross-Site Request Forgery (CSRF) 공격을 방지하기 위해 사용되는 고유한 식별자 토큰입니다. 
CSRF는 악의적인 웹사이트에서 사용자의 인증된 세션을 이용해 의도하지 않은 요청을 보내는 공격 방식입니다. 
CSRF Token은 이러한 요청이 신뢰할 수 있는 출처에서 왔는지 확인하는 데 사용됩니다.

### CSRF Token의 역할
CSRF Token은 사용자의 요청이 진짜 웹사이트에서 생성된 것인지 검증하는 1회성 고유 토큰입니다. 서버는 CSRF Token을 다음과 같은 방식으로 사용합니다.

### CSRF Token 생성
사용자가 요청을 보낼 때마다 서버는 고유한 CSRF Token을 생성하여 클라이언트에 제공합니다.
이 토큰은 세션과 연결되거나, 요청마다 다르게 생성됩니다.

클라이언트가 CSRF Token 전송:

클라이언트는 서버로 요청을 보낼 때 CSRF Token을 함께 전송합니다. 일반적으로 이는 폼 데이터 또는 HTTP 헤더를 통해 전달됩니다.

서버가 CSRF Token 검증:

서버는 요청에 포함된 CSRF Token이 세션에 저장된 토큰과 일치하는지 확인합니다.
일치하지 않으면 요청을 거부합니다.
CSRF Token의 특징
1회성 및 고유성:

각 요청에 대해 고유한 토큰을 생성하거나, 사용자의 세션과 연결된 고유한 토큰을 생성합니다.
토큰은 다른 사용자에게 복사되거나 재사용될 수 없습니다.
서버에서 생성 및 관리:

서버는 CSRF Token을 세션과 연결하여 관리하며, 만료된 토큰이나 일치하지 않는 토큰은 거부합니다.
사용자에게 숨겨진 전달 방식:

CSRF Token은 일반적으로 사용자에게 보이지 않는 방식으로 전달됩니다. (HTML 폼의 hidden input 필드 또는 HTTP 헤더)
CSRF Token의 사용 방법
1. HTML 폼의 hidden 필드로 CSRF Token 전달
서버가 CSRF Token을 HTML 폼에 추가합니다:

html
코드 복사
<form action="/transfer" method="POST">
  <input type="hidden" name="_csrf" value="generated_csrf_token_here" />
  <input type="text" name="to" placeholder="Recipient">
  <input type="number" name="amount" placeholder="Amount">
  <button type="submit">Transfer</button>
</form>
2. HTTP 헤더로 CSRF Token 전달 (Ajax 요청)
Ajax 요청의 경우, CSRF Token을 HTTP 헤더에 포함시켜 전송합니다:

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
CSRF Token이 필요한 이유
CSRF Token은 CSRF 공격을 막는 데 중요한 역할을 합니다. 브라우저는 쿠키를 자동으로 포함하기 때문에 공격자는 쿠키만으로 사용자의 세션을 이용할 수 있습니다. 하지만 CSRF Token은 다음과 같은 이유로 보안을 강화합니다:

예측 불가능한 값:

공격자가 CSRF Token의 값을 알 수 없습니다. 따라서 유효한 요청을 생성할 수 없습니다.
출처 검증:

요청에 포함된 CSRF Token이 서버에 저장된 토큰과 일치하는지 확인하므로, 요청이 신뢰할 수 있는 출처에서 생성되었음을 보장합니다.
CSRF Token과 SameSite 쿠키 비교
SameSite 쿠키는 CSRF 공격을 막기 위해 브라우저에서 제공하는 또 다른 보안 메커니즘입니다. CSRF Token과 함께 사용하면 더욱 강력한 보호를 제공합니다.

SameSite 쿠키의 작동 방식:
쿠키가 동일한 출처에서만 전송되도록 제한합니다.
예를 들어, 외부 웹사이트에서 보낸 요청에는 SameSite 쿠키가 포함되지 않으므로 CSRF 공격을 방지할 수 있습니다.
하지만 SameSite 쿠키만으로는 모든 CSRF 공격을 막을 수 없으므로 CSRF Token과 함께 사용하는 것이 권장됩니다.

Spring Security에서 CSRF Token 사용
Spring Security는 CSRF Token을 기본적으로 활성화합니다. CSRF Token은 세션 기반으로 생성 및 관리되며, Thymeleaf와 함께 사용할 때 쉽게 구현할 수 있습니다.

Thymeleaf 폼에 CSRF Token 추가:
html
코드 복사
<form th:action="@{/transfer}" method="post">
  <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
  <input type="text" name="to" placeholder="Recipient">
  <input type="number" name="amount" placeholder="Amount">
  <button type="submit">Transfer</button>
</form>
요약
CSRF Token은 서버가 요청의 유효성을 확인하기 위해 사용하는 고유한 토큰입니다.
CSRF 공격을 방지하기 위해 사용되며, 사용자의 요청이 신뢰할 수 있는 출처에서 생성된 것임을 검증합니다.
Spring Security는 CSRF Token을 기본적으로 활성화하고 관리합니다.
HTML 폼, Ajax 요청 등 다양한 방식으로 CSRF Token을 클라이언트에서 서버로 전달할 수 있습니다.


## CSRF 공격 이해
CSRF 공격 시나리오
사용자가 신뢰할 수 있는 웹사이트에 로그인하여 세션이 생성됩니다. (예: 은행, SNS 등)
사용자가 악의적인 웹사이트를 방문합니다.
악의적인 웹사이트는 사용자의 브라우저에 신뢰할 수 있는 웹사이트에 대한 요청을 자동으로 실행합니다.
이때 사용자의 세션 쿠키가 자동으로 포함됩니다.
예: http://example-bank.com/transfer?to=attacker&amount=10000
결과적으로 사용자는 알지 못한 채로 자신의 권한을 이용해 공격자의 의도를 실행하게 됩니다.




