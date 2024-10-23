Maven과 Gradle은 빌드 관리 도구이며 빌드 관리 도구는 프로젝트에서 필요한 파일들을 자동으로 

인식을 해주고 그걸 활용해서 빌드를 해주는 도구를 말하며 빌드 뿐 만아니라 빌드하기 전 프로그램

개발이 제대로 되었는지 컴파일, 테스트, 정적 분석, 프로젝트 정보 관리. 배포, 레포지토리 등 프로젝

트에서 필요한 xml, properties, jar 파일들을 자동으로 인식하여 빌드 해주는 기능을 갖추고 있다.

외부 라이브러리를 참조하여 자동으로 다운로드 및 업데이트를 관리해주는 장점도 있다.

Java에서 대표적인 빌드 도구는 Ant, Maven, Gradle이 있다.

### Ant :

- XML 기반의 빌드 스크리틉
- 자유로운 빌드 단위 지정
- 간단하고 사용하기 쉬움
- 대규모 프로젝트에서 복잡해지는 경향이 있음
- 라이프 사이클이 없음(빌드 순서 정의 어려움)

### Maven :

- XML 기반의 빌드 스크립트
- 라이프 사이클 도입(빌드 순서 정의 가능함)
- pom.xml로 편하게 Dependncy 관리

### Maven을 사용하는 이유

기존에 사용하던  Ant는 빌드의 기능만 가지고 있어 그 외에 것들은 수동으로 개발자가 직접 관리해

주어야 하기 때문에 Ant를 대체하기 위해 Maver이 만들어 졌으며 Maven은 프로젝트의 외부 라이브

러리를 쉽게 참조할 수 있게 pom.xml파일로 명시하여 관리하며 참조한 외부 라이브러리에 연관된 다

른 라이브러리도 컴파일 해서 자동으로 관리되고 만약 다운 받아 사용하고 있는 라이브러리에 변동 

사항이 있으면 자동으로 업데이트를 해주는 장점이 있다.

(개발자가 세세한 부분까지 신경 쓰지 않아도 된다.)

이러한 이유와 같이 자동으로 라이브러리, 업데이트를 관리 해주게 되어 개발자들은 Maven을 많이 

사용하게 되었다. 

### 메이븐(Maven) 대표 태그

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>MySpringProject</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>MySpringProject</name>
    <description>MySpringProject</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

```

modelVersion : maven의 버전을 의미

groupId : 프로젝트 그룹 id를 뜻하며, 일반적으로 대표하는 사이트 도메인을 역순으로 적어 사용

artiactId : groupld외에 다른 프로젝트와는 구분될 수 있는 프로젝트의 Id를 작성

name : 프로젝트의 이름

description : 해당 프로젝트의 간략한 설명을 작성

properties : pim.xml 파일 내에서 빈번하게 사용되는 중복 상수를 정의하는 영역 해당 영역의 상수를  

                   사용하기 위해서는 ${태그명}의 행태로 사용하면 됨

dependendies : 해당 프로젝트에서 의존성을 가지고 사용하는 라이브러리를 정의하는 영역

                         각 라이브러리마다 <dependency> 태그를 사용하여 구분

build : 프로젝트 빌드와 관련된 정보를 설정하는 영역

## 그래들(Gradle)

- **Groovy 스크립트**를 활용한 빌드 관리 도구
- 안드로이드 프로젝트의 표준 빌드 시스템으로 채택
- 멀티 프로젝트(Multi - Project)의 빌드에 최적화 하여 설계됨
- Maven에 비해 더 빠른 처리속도를 가지고 있음 (Maven에 비해서 최대 100배의 처리 속도)
- Maven에 비해 간결한 구성이 가능함

Gradle은 Maven이후에 나온 빌드 툴이다.

Gradle에 비해 Maven이 점유율이 더 높은 상황(점차 Gradle 점유율 오르는중)

Gradle에 비해 Maven의 성능이 떨어짐

Maven에 비해 Gradle이 대규모 프로젝트에서 성능이 좋음 

Maven : pom.xml / Gradle : build.gradle

Gradle은 설치 없이 사용할 수 있다.(Gradle Wrapper)

Gradle이 성능이 더 좋지만 많은 개발자 들은 현재까지 Maven을 사용하였기 때문에 같은 프로젝트를 할 경우 

모든 인원이 Gradle을 사용하여야 Gradle로 전환이 가능하기 때문에 실무단계에서 전환이 쉽지 않고

성능적인 면에서는 Maven의 빌r드 도구인 pom.xml보다 Gradle의 빌드 도구인 GroovyScript가 간략하게 

되어있고 빌드 단계에서 Gradle은 빌드가 변경된 내용이 없으면 패스하는 기능이 있기 때문에 Maven보다 처리

속도가 빠를 수 밖에 없다.

```java
plugins {
    id 'org.springframework.boot' version '3.0.0'
    id 'io.spring.dependency-management' version '1.0.15.RELEASE'
    id 'java'
}

group = 'com.example'
version = '1.0.0-SNAPSHOT'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}

```

repositories : 라이브러리가 저장된 위치 등 설정

라이브러리 참조를 하기 위해서 라이브러리에 어떤게 필요하다고 적어놨을때 그게 어디 있는지 정의하는 영역

mavenCental : 기본 Maven Repository를 사용하겠다는 의미 

기본 Repository외에도 회사에서 자체적인 Repository 솔루션을 사용하여 관리할 수 있고 또한, 여러가지 URL

을 통해서 레포지토리에 추가할 수 있다.

dependencies : 라이브러리 사용을 위한 의존성 설정

Implementation을 사용하여 사용하고자 하는 외부 라이브러리를 설정하면 된다.