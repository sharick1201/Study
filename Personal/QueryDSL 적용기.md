[[6주차) JPA 활용]] 공부 중, 실습 파트는 따라하는 게 다라서 작용 원리를 좀 더 이해하고 싶어 문서 개설

----
QueryDSL을 사용하고자 의 동작 과정을 요약하면 다음과 같다.
1.	Annotation Processor 동작:
	* QueryDSL의 Annotation Processor가 컴파일 시점에 JPA 엔티티(예: `User`)를 스캔하여 타입 안전한 Q 클래스(예: `QUser`)를 생성한다. 이 과정에서 `querydsl-apt`와 Jakarta Persistence API(`jakarta.persistence-api`)가 사용된다.
2.	Q 클래스 저장:
	* 생성된 Q 클래스는 Gradle 설정으로 지정한 경로에 저장된다.
3.	Q 클래스 활용:
	* 생성된 Q 클래스를 사용하여 타입 안전한 쿼리를 작성할 수 있다.

###  1. QueryDSL 기본 설정(플러그인, 종속성 명시)
build.gradle 파일에 아래와 같이 설정하였다.
```
plugins {  
    id 'java'  
    id 'org.springframework.boot' version '3.3.5'  
    id 'io.spring.dependency-management' version '1.1.6'  
  
}  
  
group = 'com.example'  
version = '0.0.1-SNAPSHOT'  
  
java {  
    toolchain {  
       languageVersion = JavaLanguageVersion.of(17)  
    }  
}  
  
configurations {  
    compileOnly {  
       extendsFrom annotationProcessor  
    }  
}  
  
repositories {  
    mavenCentral()  
}  
  
dependencies {  
  
    // JPA  
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'  
  
    // Web  
    implementation 'org.springframework.boot:spring-boot-starter-web'  
  
    // SpringSecurity  
//  implementation 'org.springframework.boot:spring-boot-starter-security'  
  
    // Hibernate (queryDSL을 위해 6.0.2 이상)  
    implementation 'org.hibernate.orm:hibernate-core:6.0.2.Final'  
  
    //mySQL  
    implementation 'mysql:mysql-connector-java:8.0.33'  
  
    // queryDSL  
    implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'  
    annotationProcessor "com.querydsl:querydsl-apt:5.0.0:jakarta"  
    annotationProcessor "jakarta.annotation:jakarta.annotation-api"  
    annotationProcessor "jakarta.persistence:jakarta.persistence-api"  
  
//  implementation 'com.fasterxml.jackson.core:jackson-databind:2.13.3'  
//  implementation 'com.fasterxml.jackson.core:jackson-annotations:2.13.3'  
//  implementation 'com.fasterxml.jackson.core:jackson-core:2.13.3'  
  
    compileOnly 'org.projectlombok:lombok:1.18.22'  
    runtimeOnly 'com.mysql:mysql-connector-j'  
    annotationProcessor 'org.projectlombok:lombok:1.18.22'  
  
    implementation 'org.springdoc:springdoc-openapi-ui:1.6.9'  
    implementation 'org.springdoc:springdoc-openapi-data-rest:1.6.9'  
  
    // 쿼리 파라미터 로그 남기기용  
    implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.9.0'  
  
    implementation 'org.hibernate:hibernate-validator:8.0.0.Final'  
  
  
    testImplementation 'org.springframework.boot:spring-boot-starter-test'  
//  testImplementation 'org.springframework.security:spring-security-test'  
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'  
}  
  
def generated = file("src/main/generated/querydsl")  
  
tasks.named('clean') {  
    doLast {  
       generated.deleteDir() // clean 태스크 시 생성 디렉토리 삭제  
    }  
}  
  
tasks.withType(JavaCompile).configureEach {  
    options.getGeneratedSourceOutputDirectory().set(generated) // Q 클래스 생성 경로 설정  
}  
  
sourceSets {  
    main {  
       java {  
          srcDir generated // 생성된 Q 클래스를 소스셋에 포함  
       }  
    }}
```

* 주요 의존성
	* querydsl-jpa
		* `implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'`
		* QueryDSL의 JPA 통합 모듈
		* QueryDSL이 JPA 엔티티를 기반으로 타입 안전한 Q클래스를 생성하고 JPA 쿼리를 작성할 수 있도록 지원한다.
		* `5.0.0:jakarta`는 Jakarta Persistence API(`jakarta.persistence`)를 기반으로 동작한다.
			* Spring Boot 3.x 이상에서는 Jakarta API를 사용하므로, 반드시 jakarta classifier(애플리케이션의 구성 요소를 정의하고 구분하는 분류기)를 포함시켜야 한다.
		
	* querydsl-apt
		* `annotationProcessor 'com.querydsl:querydsl-apt:5.0.0:jakarta'`
		* QueryDSL의 Annotation Processor(코드의 어노테이션을 처리하는 도구)
		* 컴파일 시점에 JPA 엔티티를 기반으로 Q클래스를 생성한다. 예컨대, `User` 엔티티가 있다면 `QUser`라는 이름의 Q클래스를 생성한다.

* Jakarta API 관련 의존성
	* jakarta.annotation-api
		* `annotationProcessor "jakarta.annotation:jakarta.annotation-api"`
		* Jakarta EE에서 제공하는 어노테이션 API
		* QueryDSL의 Annotation Processor가 Jakarta EE 환경에서 동작하도록 지원한다.
	* jakarta.persistence-api
		* `annotationProcessor "jakarta.persistence:jakarta.persistence-api"`
		* Jakarta Persistence API
		* Hibernate 6.x 이상, Spring Boot 3.x 이상에서는 JPA 표준으로 Jakarta Persistence API를 사용한다. 따라서 QueryDSL의 Annotation Processor가 JPA 엔티티를 처리할 때 필요하다.
	
* lombok 설정
	* `compileOnly 'org.projectlombok:lombok'`
	* `annotationProcessor 'org.projectlombok:lombok'`
	* Lombok과 QueryDSL의 Annotation Processor는 함께 사용이 가능하다. 다만, Gradle 설정에서 각각 명확히 분리해야 한다. <span style="background:rgba(240, 107, 5, 0.2)">왜?</span>
		* 나는 `compileOnly`와 `annotationProcessor`로 분리하여 충돌을 방지했다.

* QueryDSL 관련 gradle 설정
	* Q클래스 생성 경로 설정
		* Q클래스가 생성될 디렉토리를 지정한다.
	* clean 태스크를 통한 Q클래스 삭제
		* clean 태스크를 설정함으로서 이전에 생성된 Q클래스를 삭제한다.
		* 이 작업은 gradle로 빌드할 때마다 항상 최신 상태의 Q클래스가 생성되도록 한다.
	* JavaCompile 태스크에서 Q클래스 경로 설정
		* Gradle 7.x 이상에서는 `options.getGeneratedSourceOutputDirectory()`를 사용하여 Annotation Processor가 생성한 소스 파일의 출력 경로를 지정한다.
		* 여기서는 Q 클래스가 지정된 경로에 생성되도록 설정했다.
	* sourceSets에 Q클래스 경로 추가
		* Gradle의 SourceSets 설정에 Q클래스 경로를 추가함으로써 Q클래스도 프로젝트 소스 코드로 인식하도록 설정했다.

* 추가 확인 사항
	* IntelliJ IDEA 설정
		* IntelliJ IDEA > Preferences > Build, Execution, Deployment > Compiler > Annotation Processors 에서 "Enable annotation processing” 체크박스를 활성화시킨다. 이를 통해 `annotationProcessor`를 사용할 수 있게 된다.

