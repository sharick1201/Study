[[6주차) JPA 활용]] 공부 중, 실습 파트는 따라하는 게 다라서 작용 원리를 좀 더 이해하고 싶어 문서 개설

----

1. QueryDSL 기본 설정
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
