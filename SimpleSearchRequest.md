## SimpleSearchRequest
SimpleSearchRequest 클래스는 검색 요청 데이터를 캡슐화 하는 DTO로, 클라이언트에서 보낸 검색 조건을
서버에서 간편하게 처리하고, 검색 조건을 기반으로 데이터 조회나 URL 생성에 활용하기 위해 사용된다.
즉, 검색 조건을 컨트롤러 메서드에 전달하여 검색 기능을 실행할 때 사용된다.

```java
import lombok.Data;

@Data
public class SimpleSearchRequest {
    protected String category = "";
    protected String value = "";
    protected int showNum = 10;
    protected int page = 0;

    public String toPathParam() {
        return "?category=" + category +
                "&value=" + value +
                "&showNum=" + showNum;
    }
}
```

### 검색 조건을 저장
검색할 카테고리 : (category)
검색할 값 : (value)
한 페이지에 표시할 데이터 개수 : (showNum)
현재 페이지 번호 : (page)

### 데이터 전달
이 클래스는 검색 조건을 컨트롤러 메서드에 전달하여 검색 기능을 실행할 때 사용된다.

### URL 파라미터 생성
toPathParam() 메서드를 통해 검색 조건을 URL 파라미터 문자열로 변환하여, 페이징이나 링크 생성 시 활용할 수 있다.(Query)


### toPathParam() 메서드의 역할
toPathParam() 메서드는 객체에 저장된 검색 조건 데이터를 URL 쿼리 스트링(Query String) 형식으로 변환한다.

### 변환 과정

```java : 
SimpleSearchRequest searchReq = new SimpleSearchRequest();
searchReq.setCategory("from");
searchReq.setValue("01012345678");
searchReq.setShowNum(30);
searchReq.setPage(2);
```

```java
category = "from"
value = "01012345678"
showNum = 30
page = 2
```

```java
String pathParam = searchReq.toPathParam();
```

호출 결과 :

```java
"?category=from&value=01012345678&showNum=30"
```

### SimpleSearchReques 사용 예시

예를 들어 
```/list?page=1&category=from&value=01012345678&showNum=10``` 와 같이 Get요청이 들어왔을 떄 

컨트롤러에서 아래와 같이 데이터를 받는다.

```java
@RequestMapping("/list/{page}")
public String list(Model model, @ModelAttribute SimpleSearchRequest searchReq) {
    model.addAttribute("pathParam", searchReq.toPathParam());
    return "admin/sendLog/list";
}
```

searchReq의 category에는 from, vlaue에는 01012345678, showNum에는 10이라고 저장되고
이 데이터들을 Parameter로 변환 후 pathParam이란 이름으로 설정하여 admin/sendLog/list 페이지로 전달한다.

admin/sendLog/list 페이지에서 pathParam 데이터를 받아 다음 페이지로 넘어가는 기능에 추가한다. 

```html
    <a href="list?page=${pagingInfo.curPage + 1}${pathParam}">다음 페이지</a>
```

다음 페이지에로 넘거나는 기능에 추가하게 되면 첨음 Get 요청 했던 데이터들이 다음 페이지로 넘어가더라도 데이터가 사라지지 않고
요청했던 정보 그대로 조건에 맞게 검색하게 된다.

즉, 특정 페이지에서 category가 from이며 01012345678 번호를 10개 보여달라는 조건을 검색 했을때 
현재 페이지에서 불러오고 다음 페이지에서도 동일한 조건으로 검색해서 불러와야 하기 때문에 SimpleSearchReques 클래스를 사용하는 것이다.


왜 반환타입이 Page<MsgSendLog>인가?
페이징 처리 결과를 한꺼번에 반환하려면 데이터와 메타정보가 모두 필요합니다. 따라서:

데이터(List 형태): 현재 페이지에 해당하는 데이터(예: 10개 또는 20개)를 담음.
메타정보: 페이징 처리를 위해 필요한 추가 정보(총 페이지 수, 현재 페이지 등).


1. Page<MsgSendLog>가 반환해야 할 정보
페이징 처리의 결과는 단순히 데이터만 반환하는 것이 아니라, 데이터를 포함한 여러 페이징 관련 메타정보를 반환해야 합니다. Page 인터페이스는 이 모든 정보를 효율적으로 담을 수 있는 구조를 제공합니다.

(1) 데이터 (List 형태)
사용자가 요청한 페이지에 해당하는 데이터가 필요합니다.
예를 들어, 10개의 데이터씩 나누어진 2번째 페이지를 요청했다면, 해당 페이지에 포함된 10개의 데이터를 반환해야 합니다.
이 데이터는 Page 객체의 getContent() 메서드를 통해 접근할 수 있습니다.
java
코드 복사
List<MsgSendLog> data = page.getContent(); // 현재 페이지 데이터
(2) 메타정보 (페이징 처리에 필요한 추가 정보)
클라이언트가 페이지 네비게이션을 구현하거나, 페이징 처리를 제어하려면 다음과 같은 메타정보가 필요합니다:
총 데이터 개수 (getTotalElements())
데이터베이스에 저장된 전체 데이터의 개수입니다.
예: 250개의 데이터가 있는 경우, 클라이언트는 총 페이지 수를 계산할 수 있음.
총 페이지 수 (getTotalPages())
페이지 크기(예: 한 페이지에 10개 데이터)로 나누었을 때 생성되는 전체 페이지 수.
예: 데이터가 250개이고, 페이지 크기가 10이라면, 총 25페이지가 필요함.
현재 페이지 번호 (getNumber())
현재 클라이언트가 요청한 페이지 번호입니다.
예: 클라이언트가 2번째 페이지를 요청한 경우, getNumber()는 2를 반환.
다음 페이지 존재 여부 (hasNext())
다음 페이지가 존재하는지 여부를 반환합니다.
예: 마지막 페이지라면 false, 그렇지 않으면 true.
이전 페이지 존재 여부 (hasPrevious())
이전 페이지가 존재하는지 여부를 반환합니다.
예: 첫 페이지라면 false.
2. 왜 데이터와 메타정보를 함께 반환해야 하는가?
페이징 처리는 단순히 데이터 일부만 가져오는 것이 아니라, 클라이언트가 다음과 같은 동작을 할 수 있도록 해야 합니다:

페이지 네비게이션: 클라이언트가 "다음 페이지"나 "이전 페이지"로 이동할 수 있어야 함.
전체 데이터의 범위를 파악: 클라이언트가 전체 데이터의 개수와 페이지 수를 알아야 적절히 UI를 구성할 수 있음.
유연한 데이터 표시: 클라이언트가 현재 페이지의 데이터와 상태를 표시할 수 있어야 함.
Page 인터페이스는 데이터와 메타정보를 함께 제공하여, 이 모든 요구사항을 충족할 수 있습니다.

List를 사용하여 페이징 데이터를 반환할 수도 있지만, 이는 몇 가지 한계와 추가 구현 작업을 요구합니다. List는 단순히 데이터를 담는 용도로만 사용되기 때문에, 페이징 처리에 필요한 메타정보(총 데이터 개수, 현재 페이지 번호, 총 페이지 수 등)를 별도로 관리해야 합니다.

1. List를 사용하는 경우
만약 페이징 처리 결과를 Page 대신 List로 반환한다면, 페이징 메타정보를 추가적으로 관리해야 합니다. 예를 들어:

데이터 반환 코드:
java
코드 복사
public List<MsgSendLog> getPageData(int page, int pageSize) {
    int offset = page * pageSize; // 시작 위치 계산
    return msgSendLogRepository.findAll(Sort.by(Sort.Direction.DESC, "id"))
                               .stream()
                               .skip(offset)
                               .limit(pageSize)
                               .collect(Collectors.toList());
}
메타정보 반환 코드:
메타정보는 별도로 계산해서 관리해야 합니다:

java
코드 복사
public Map<String, Object> getPageMeta(int page, int pageSize) {
    long totalElements = msgSendLogRepository.count(); // 전체 데이터 개수
    int totalPages = (int) Math.ceil((double) totalElements / pageSize);

    Map<String, Object> metaInfo = new HashMap<>();
    metaInfo.put("currentPage", page);
    metaInfo.put("totalPages", totalPages);
    metaInfo.put("totalElements", totalElements);
    metaInfo.put("hasNext", page < totalPages - 1);
    metaInfo.put("hasPrevious", page > 0);

    return metaInfo;
}
2. 단점: List를 사용하면 생기는 문제
(1) 데이터와 메타정보를 분리해야 함
List는 단순히 데이터만 담기 때문에, 페이징 메타정보(총 페이지 수, 현재 페이지 번호 등)를 별도로 관리해야 합니다.
이는 코드가 분산되고 복잡해지며, 데이터와 메타정보 간의 일관성을 유지하기 어려워집니다.
(2) 별도의 로직 추가 필요
총 데이터 개수(count()), 총 페이지 수 계산, 현재 페이지 정보 등은 List로 처리할 수 없으므로, 이를 계산하는 별도의 로직이 필요합니다.
위와 같이 별도의 메타정보 관리 메서드(getPageMeta)를 작성해야 하므로 유지보수가 어려워집니다.
(3) 클라이언트에서 데이터를 활용하기 어렵다
클라이언트가 네비게이션을 구현하거나, 전체 데이터를 파악하려면 추가적인 메타정보가 필요합니다.
이 경우, 메타정보를 List와 별도로 제공해야 하므로 클라이언트에서의 사용이 복잡해질 수 있습니다.
3. 결론: List와 Page의 비교
특징	List	Page
데이터 포함 여부	데이터만 포함	데이터와 메타정보 모두 포함
메타정보 관리	별도의 로직이 필요	자동으로 제공
코드 복잡성	복잡해짐	단순함
페이징 네비게이션	클라이언트가 별도로 메타정보를 처리해야 함	제공된 메타정보를 바로 활용 가능
유지보수 용이성	어렵다	용이하다
4. List를 사용해야 하는 경우
단순히 데이터를 반환하고, 클라이언트가 메타정보를 따로 계산하는 경우.
페이징 메타정보가 필요 없고, 단순 데이터만 필요한 경우.
예:

java
코드 복사
public List<MsgSendLog> getLogs() {
    return msgSendLogRepository.findAll(Sort.by(Sort.Direction.DESC, "id"));
}
5. 권장사항
대부분의 페이징 처리 요구사항에서는 Page를 사용하는 것이 적합합니다. 왜냐하면:

데이터와 메타정보를 한 번에 반환할 수 있음.
Spring Data JPA가 제공하는 페이징 기능을 쉽게 활용할 수 있음.
유지보수성과 코드의 간결성을 보장할 수 있음.
List는 단순한 데이터 반환 용도에 적합하지만, 페이징 처리가 필요한 경우에는 추가적인 작업이 많이 필요하므로 권장되지 않습니다.

PagingInfo란?
PagingInfo는 페이징에 필요한 메타 정보를 캡슐화한 클래스입니다. 보통 이런 클래스는 페이징 관련 데이터를 뷰(HTML, JSP 등)로 전달하거나, 클라이언트가 페이징 UI(페이지 이동 버튼, 총 페이지 수 등)를 생성하는 데 활용됩니다.


6. PagingInfo를 사용하는 이유
데이터 가공:

Page 객체는 Spring Data JPA에서 제공하는 표준 형식이지만, 클라이언트나 뷰에서 사용할 때는 불필요한 정보가 많을 수 있습니다. 필요한 정보만 골라서 제공하기 위해 PagingInfo와 같은 별도 클래스를 사용합니다.
유지보수 용이성:

만약 페이징 정보를 변경하거나 확장해야 할 경우, PagingInfo 클래스만 수정하면 되기 때문에 코드가 간결하고 유지보수하기 쉽습니다.
뷰에 전달하기 쉽게:

PagingInfo는 JSP나 프론트엔드로 전달하기에 적합한 형태로 데이터를 가공해줍니다.


검색데이터를 파라미터로 바꾸는건 이해가 가는데 왜 view에 추가하기 위해서 model에 추가하는지 모르겠어

model.addAttribute("pathParam","?"+searchReq.toPathParam());

model.addAttribute("pathParam", "?" + searchReq.toPathParam()); 코드에서 검색 데이터를 뷰(View)에 전달하기 위해 Model에 추가하는 이유는 다음과 같습니다.


1. 검색 데이터를 View에서 재사용하기 위해
검색 조건 데이터를 URL 쿼리 스트링 형식으로 변환한 pathParam 값은 **뷰(View)**에서 다음과 같은 경우에 필요할 수 있습니다:

페이징 링크 생성:
사용자가 다음 페이지나 이전 페이지로 이동할 때, 현재 검색 조건을 유지한 상태로 링크를 생성해야 합니다.
예: list?page=2&category=from&value=01012345678&showNum=10
검색 조건 유지:
검색 후에 페이지를 새로고침하거나 다른 페이지로 이동했을 때, 검색 조건이 유지되어야 사용자 경험이 좋아집니다.
예제:
뷰(JSP)에서 pathParam을 활용하여 페이징 버튼을 만들 수 있습니다:

html
코드 복사
<a href="list?page=1${pathParam}">1</a>
<a href="list?page=2${pathParam}">2</a>
이 코드에서 pathParam이 포함되지 않으면 검색 조건이 유지되지 않아 링크가 잘못 동작할 수 있습니다.

2. Model은 Controller에서 View로 데이터를 전달하는 주요 수단
Spring MVC에서 Model 객체는 컨트롤러에서 생성된 데이터를 뷰로 전달하기 위한 도구입니다.

컨트롤러에서 생성한 데이터(pathParam)를 뷰(JSP 등)로 전달하려면 Model 객체를 사용해야 합니다.
JSP에서 ${pathParam}과 같이 사용하여 해당 데이터를 참조할 수 있습니다.
Spring MVC의 기본 동작:

컨트롤러에서 model.addAttribute()를 호출하여 데이터를 추가.
뷰(JSP)에서 해당 데이터를 EL(Expression Language)로 접근.
3. pathParam 없이도 동작할 수는 있지만…
pathParam을 Model에 추가하지 않고 JSP에서 검색 조건을 직접 조합할 수도 있습니다. 예를 들어:

html
코드 복사
<a href="list?page=1&category=${searchReq.category}&value=${searchReq.value}&showNum=${searchReq.showNum}">1</a>
하지만 이 방식은:


검색 조건을 뷰(View) 레이어에서 다시 조립해야 하므로 뷰가 지나치게 복잡해질 수 있습니다.
검색 조건 조합 로직이 컨트롤러와 뷰에 중복될 가능성이 높아져 유지보수 어려움이 생깁니다.
4. 컨트롤러에서 처리하면 코드 재사용성과 유지보수성 증가
toPathParam() 메서드로 검색 조건을 쿼리 스트링으로 변환하는 로직은 컨트롤러에서 처리하므로:

뷰는 데이터 표현만 담당하고, 로직을 포함하지 않게 됩니다(관심사 분리).
향후 검색 조건이 변경되거나 추가될 경우, 컨트롤러의 로직만 수정하면 뷰 코드는 수정할 필요가 없습니다.
요약
model.addAttribute("pathParam", "?" + searchReq.toPathParam());는 뷰에서 검색 조건을 재사용하거나 페이징 링크를 생성하기 위해 사용됩니다.
Model을 통해 데이터를 뷰로 전달하면 뷰의 복잡도를 줄이고, 로직을 컨트롤러에 집중시킬 수 있습니다.
이는 MVC 패턴에서 컨트롤러는 로직을 처리하고, 뷰는 데이터를 표현하는 역할에 충실하게 만드는 좋은 설계입니다.



Spring MVC에서 JSP와 Thymeleaf가 뷰를 처리하는 방식에는 약간의 차이가 있기 때문에 발생하는 현상입니다. 그 차이를 이해하면 왜 admin/sendSms/smsList와 같은 경로를 명시적으로 제공해야 하는지 알 수 있습니다.

1. Thymeleaf는 기본적으로 파일 시스템의 위치를 몰라도 동작
Thymeleaf는 뷰 템플릿 엔진으로 동작하며, 기본적으로 컨텍스트 경로만 알면 되도록 설계되었습니다.
Thymeleaf 템플릿은 주로 resources/templates 디렉토리에 저장되며, Spring Boot는 기본적으로 이 위치를 템플릿 루트로 가정합니다. 따라서 뷰 이름만 전달해도 (예: "smsList") 템플릿이 자동으로 찾아집니다.
예제 (Thymeleaf):
java
코드 복사
@Controller
@RequestMapping("/cms")
public class SmsSendController {
    @GetMapping("/smsList")
    public String smsList() {
        // Spring Boot가 기본 템플릿 경로를 자동으로 연결
        return "smsList"; // resources/templates/smsList.html
    }
}
2. JSP는 웹 애플리케이션 구조에 의존
JSP는 Thymeleaf와 다르게 WEB-INF 디렉토리 아래에 물리적으로 존재하며, Spring의 뷰 리졸버를 통해 해당 경로를 정확히 알려주어야만 파일을 찾을 수 있습니다.
즉, JSP는 물리적인 파일 경로에 의존하며, 이를 Spring 뷰 리졸버의 prefix/suffix로 지정합니다.


@Controller
@RequestMapping("/cms")
public class SmsSendController {
    @RequestMapping(value = "/smsList", method = RequestMethod.GET)
    public String smsList() {
        // JSP 파일은 명시적인 경로를 제공해야 찾을 수 있음
        return "admin/sendSms/smsList"; // /WEB-INF/jsp/admin/sendSms/smsList.jsp
    }
}
