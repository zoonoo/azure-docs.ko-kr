---
title: Azure 스프링 클라우드의 구조적 응용 프로그램 로그 | Microsoft Docs
description: 이 문서에서는 Azure 스프링 클라우드에서 구조화 된 응용 프로그램 로그 데이터를 생성 하 고 수집 하는 방법을 설명 합니다.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878357"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 구조적 응용 프로그램 로그

이 문서에서는 Azure 스프링 클라우드에서 구조화 된 응용 프로그램 로그 데이터를 생성 하 고 수집 하는 방법을 설명 합니다. 적절 한 구성을 통해 Azure 스프링 클라우드는 Log Analytics를 통해 유용한 응용 프로그램 로그 쿼리 및 분석 기능을 제공 합니다.

## <a name="log-schema-requirements"></a>로그 스키마 요구 사항
로그 쿼리 환경을 개선 하려면 응용 프로그램 로그가 JSON 형식이 고 스키마를 준수 해야 합니다. Azure 스프링 클라우드는이 스키마를 사용 하 여 응용 프로그램 및 스트림을 Log Analytics로 구문 분석 합니다. 

**JSON 스키마 요구 사항:**

| Json 키      | Json 값 형식|  필수 | Log Analytics의 열| Description |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     예   | AppTimestamp    | 타임 스탬프 (UTC 형식)  |
| logger        | 문자열      |     No    | 로거          | logger                   |
| 수준         | 문자열      |     No    | CustomLevel     | 로그 수준                |
| 스레드        | 문자열      |     No    | 스레드          | 스레드                   |
| message       | 문자열      |     No    | 메시지         | 로그 메시지              |
| stackTrace    | 문자열      |     No    | StackTrace      | 예외 스택 추적    |
| exceptionClass| 문자열      |     No    | ExceptionClass  | 예외 클래스 이름     |
| mdc           | 중첩 된 JSON |     아니요    |                 | 매핑된 진단 컨텍스트|
| mdc. traceId   | 문자열      |     No    | TraceId         |분산 추적의 추적 Id|
| spanId    | 문자열      |     No    | SpanId          |분산 추적의 범위 Id |
|               |             |           |                 |                          |

* "Timestamp" 필드는 필수 이며 UTC 형식 이어야 하며 다른 모든 필드는 선택 사항입니다.
* "mdc" 필드의 "traceId" 및 "spanId"는 추적 용도로 사용 됩니다.
* 각 JSON 레코드를 한 줄에 기록 합니다. 

**로그 레코드 샘플** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>스키마 규격 JSON 로그 생성  
스프링 응용 프로그램의 경우 [logback](http://logback.qos.ch/) 및 [log4j2.xml](https://logging.apache.org/log4j/2.x/)와 같은 일반적인 [로깅 프레임 워크](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)를 사용 하 여 예상 되는 JSON 로그 형식을 생성할 수 있습니다. 

### <a name="log-with-logback"></a>Logback을 사용 하 여 로그 
스프링 부팅 초보자를 사용 하는 경우 기본적으로 logback이 사용 됩니다. Logback 앱의 경우 [logback 태 시-인코더](https://github.com/logstash/logstash-logback-encoder) 를 사용 하 여 JSON 형식의 로그를 생성 합니다. 이 메서드는 스프링 부팅 버전 2.1 이상에서 지원 됩니다. 

절차는 다음과 같습니다.

1. pom.xml 파일에 logstash 종속성을 추가 합니다. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. logback.xml 구성 파일을 업데이트 하 여 JSON 형식을 설정 합니다.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Log4j2.xml로 로그 

Log4j2.xml apps의 경우 json [템플릿-레이아웃](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) 을 사용 하 여 json 형식의 로그를 생성 합니다. 이 메서드는 스프링 부팅 버전 2.1 이상에서 지원 됩니다.

절차는 다음과 같습니다.

1. `spring-boot-starter-logging` `spring-boot-starter` pom.xml 파일에서 종속성을 제외 하 고 종속성 `spring-boot-starter-log4j2` 을 추가 `log4j-layout-template-json` 합니다.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. JSON 레이아웃 템플릿 파일을 클래스 경로에 jsonTemplate.js준비 합니다.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. log4j2.xml 구성 파일에서이 JSON 레이아웃 템플릿을 사용 합니다. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Log Analytics에서 로그 분석

응용 프로그램을 올바르게 설정한 후에는 응용 프로그램 콘솔 로그가 Log Analytics로 스트리밍됩니다. 구조는 Log Analytics에서 효율적인 쿼리를 가능 하 게 합니다.

### <a name="check-log-structure-in-log-analytics"></a>Log Analytics에서 로그 구조 확인
이렇게 하려면 다음 절차를 수행합니다.
1. 서비스 인스턴스의 서비스 개요 페이지로 이동 합니다.
2. `Logs`섹션에서 항목 `Monitoring` 을 클릭 합니다.
3. 이 쿼리를 실행 합니다.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. 응용 프로그램 로그는 다음 이미지와 같이 반환 됩니다.

![Json 로그 표시](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>오류를 포함 하는 로그 항목 표시

오류가 있는 로그 항목을 검토 하려면 다음 쿼리를 실행 합니다.

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

이 쿼리를 사용 하 여 오류를 찾거나 쿼리 용어를 수정 하 여 특정 예외 클래스 또는 오류 코드를 찾습니다. 

### <a name="show-log-entries-for-a-specific-traceid"></a>특정 traceId에 대 한 로그 항목 표시
특정 추적 ID "trace_id"에 대 한 로그 항목을 검토 하려면 다음 쿼리를 실행 합니다.

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>다음 단계
* 로그 쿼리에 대 한 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md) 을 참조 하세요.