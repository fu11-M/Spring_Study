배포할 때 로컬 실행 프로그램은 JAR로 패키징하고 웹은 WAR로 패키징 한다. WAR는 압축 파일에 

자바 관련 규약이 포함된 것이 바로 WEB - INF 폴더다.

웹 애플리케이션 컨테이너는 WAR파일의 WEB - INF 폴더를 기준으로 클래스 파일들을 로드한다.

WAR는 Web Application Archive의 약자이며 webapp또는 web과 같은 이름으로 프로젝트 설정에 

따라서 조금씩 다르지만, HTML, JavaScript, CSS, 또는 HTML 태그를 포함하고 있는 JSP 파일처럼 

브라우저에서 보여 줘야 하는 정적 자원을 관리하기 위한 폴더이다. 

이 폴더는 브라우저상에서 직접 접근할 수 있어서 최근에는  content directory를 WAR 파일의

상위에 두지 않고 WEB - INF 하위에 설정하는 추세이다.

WAR로 패키징하면 클래스 파일들은 WEB - INF 하위 class 폴더에 저장된다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea204791-94b0-4594-95e9-37705edf8245/65535d4f-4f25-4605-a108-7afa09777f42/Untitled.png)

libs폴더에는 JAR형식의 외부 라이브러리들이 있으며 이 폴더에 있는 JAR 라이브러리들은 사용자 

정의 클래스 로더 웹 애플리케이션 컨테이너의 로더를 통해 클래스 패스에 추가된다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea204791-94b0-4594-95e9-37705edf8245/ed691b04-8319-4ab5-b891-9deb3474551f/Untitled.png)

웹 애플리케이션 클래스 로더는 클래스 로더의 유형 중에서 시스템 클래스 로더 하위에 사용자가 

정의한 클래스 로더에 해당한다. 웹 애플리케이션 컨테이너는 웹 애플리케이션 자체를 제공하기 

위해서 컨터이너를 로드하는 클래스 로더와 사용자가 추가한 JSP나 WAR파일들을 다루기 위한

ServletContext Loader를 사용한다. 컨테이너가 시작되고 콘텍스트가 초기화 되면 서블릿 스펙의 

권장 사항에 따라 WEB - INF/classes 파일을 먼저 검색해서 로딩하고, 그 후에 WEB - INF/libs에 있는 

JAR파일들을 로드 한다.