---
title: 자습서 - Azure Spring Cloud에서 배포용 Spring 애플리케이션 준비
description: 이 자습서에서는 배포용 Java Spring 애플리케이션을 준비합니다.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890317"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>자습서: Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비

이 빠른 시작에서는 Azure Spring Cloud에 배포하기 위해 기존 Java Spring Cloud 애플리케이션을 준비하는 방법을 보여줍니다.  올바르게 구성된 Azure Spring Cloud는 Spring Cloud 애플리케이션을 모니터링하고, 크기를 조정하고, 업데이트할 수 있는 강력한 서비스를 제공합니다. 

## <a name="java-runtime-version"></a>Java Runtime 버전

Spring/Java 애플리케이션만 Azure Spring Cloud에서 실행할 수 있습니다.

Java 8 및 Java 11이 둘 다 지원됩니다. 호스팅 환경에는 Azure용 최신 Azul Zulu OpenJDK가 포함되어 있습니다. Azure용 Azul Zulu OpenJDK에 대한 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)를 참조하세요. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 및 Spring Cloud 버전

Azure Spring Cloud에서는 Spring Boot 앱만 지원됩니다. Spring Boot 2.0 및 2.1이 모두 지원됩니다. 지원되는 Spring Boot 및 Spring Cloud 조합은 아래 표에 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

`pom.xml` 파일에 버전에 따라 Spring Boot 및 Spring Cloud 종속성이 있는지 확인합니다.

### <a name="version-20"></a>버전 2.0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>버전 2.1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 클라이언트 종속성

Azure Spring Cloud에서는 Spring Cloud 서비스 레지스트리 및 Spring Cloud Config 서버와 같은 Spring Cloud 구성 요소가 호스팅되고 관리됩니다. Azure Spring Cloud 서비스 인스턴스와 통신할 수 있도록 종속성에 Azure Spring Cloud의 클라이언트 라이브러리를 포함합니다.

아래 표에는 Spring Boot/Spring Cloud 앱의 올바른 버전이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전 | Azure Spring Cloud 버전
---|---|---
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

`pom.xml` 아래 코드 조각 중 하나를 포함합니다.  사용자 소유의 버전과 일치하는 코드 조각을 선택합니다.

### <a name="version-20x"></a>버전 2.0.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>버전 2.1.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>기타 필수 종속성

Azure Spring Cloud의 기본 제공 기능을 사용하려면 애플리케이션에 다음 종속성이 포함되어야 합니다. 이렇게 하면 애플리케이션이 각 구성 요소에 맞게 올바르게 구성됩니다.  

### <a name="service-registry"></a>서비스 레지스트리

관리형 Azure Service Registry 서비스를 사용하려면 아래와 같이 `POM.xml`에 `spring-cloud-starter-netflix-eureka-client`를 포함합니다.

서비스 레지스트리 서버의 엔드포인트는 앱에 환경 변수로 자동 삽입됩니다. 애플리케이션이 서비스 레지스트리 서버에 자체 등록하고 다른 종속 마이크로서비스를 검색할 수 있습니다.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>분산 구성

분산 구성을 사용하도록 설정하려면, `pom.xml`의 종속성 섹션에 `spring-cloud-config-client`를 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 부트스트랩 구성에 `spring.cloud.config.enabled=false`를 지정하지 마십시오. 애플리케이션이 Config 서버와 작동하지 못하게 됩니다.

### <a name="metrics"></a>메트릭

pom.xml의 종속성 섹션에 `spring-boot-starter-actuator`를 포함합니다. 메트릭을 JMX 엔드포인트에서 주기적으로 가져오고 Azure Portal을 사용하여 시각화할 수 있습니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>분산 추적

아래 pom.xml의 종속성 섹션에 `spring-cloud-starter-sleuth` 및 `spring-cloud-starter-zipkin`을 포함합니다. 또한 Azure App Insights 인스턴스가 Azure Spring Cloud 서비스 인스턴스와 작동하도록 설정해야 합니다. Azure Spring Cloud로 App Insights를 사용하도록 설정하는 방법에 대한 자세한 내용은 [여기](spring-cloud-tutorial-distributed-tracing.md)를 참조하세요.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Spring Cloud에 배포하기 위해 Java Spring 애플리케이션을 구성하는 방법을 알아보았습니다.  Config 서버를 사용하도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [Config 서버를 설정하는 방법을 알아봅니다](spring-cloud-tutorial-config-server.md).

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
