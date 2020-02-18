---
title: 자습서 - Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비
description: 이 자습서에서는 Azure Spring Cloud에 배포할 Java Spring 애플리케이션을 준비합니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: af3611e4c4d1f5d8ca52b3ceb80d79dcfd7d2061
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190749"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비

이 빠른 시작에서는 Azure Spring Cloud에 배포하기 위해 기존 Java Spring 애플리케이션을 준비하는 방법을 보여줍니다. 제대로 구성된 경우, Azure Spring Cloud는 Java Spring Cloud 애플리케이션을 모니터링하고, 크기를 조정하고, 업데이트할 수 있는 강력한 서비스를 제공합니다.

## <a name="java-runtime-version"></a>Java Runtime 버전

Spring/Java 애플리케이션만 Azure Spring Cloud에서 실행할 수 있습니다.

Azure Spring Cloud는 Java 8 및 Java 11을 모두 지원합니다. 호스팅 환경에는 Azure용 Azul Zulu OpenJDK의 최신 버전이 포함되어 있습니다. Azure용 Azul Zulu OpenJDK에 대한 자세한 내용은 [JDK 설치](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)를 참조하세요.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 및 Spring Cloud 버전

Azure Spring Cloud는 Spring Boot 앱만 지원합니다. Spring Boot 버전 2.1과 버전 2.2를 모두 지원합니다. 아래 표에는 지원되는 Spring Boot 및 Spring Cloud 조합이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

pom.xml 파일에 Spring Boot 버전에 따라 올바른 Spring Boot 및 Spring Cloud 종속성이 있는지 확인하세요.

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot 버전 2.1에 대한 종속성

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Spring Boot 버전 2.2에 대한 종속성

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 클라이언트 종속성

Azure Spring Cloud는 Spring Cloud 구성 요소를 호스팅하고 관리해 줍니다. 이러한 구성 요소에는 에는 Spring Cloud 서비스 레지스트리 및 Spring Cloud 구성 서버가 포함됩니다. 종속성에 Azure Spring Cloud 클라이언트 라이브러리를 포함하면, Azure Spring Cloud 서비스 인스턴스와 통신할 수 있습니다.

다음 표에는 Spring Boot 및 Spring Cloud를 사용하는 앱의 올바른 Azure Spring Cloud 버전이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전 | Azure Spring Cloud 버전
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

pom.xml 파일에 다음 종속성 중 하나를 포함하세요. Azure Spring Cloud 버전이 사용하는 버전과 일치하는 종속성을 선택하세요.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Azure Spring Cloud 버전 2.1에 대한 종속성

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Azure Spring Cloud 버전 2.2에 대한 종속성

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>기타 필수 종속성

Azure Spring Cloud의 기본 제공 기능을 사용하려면 애플리케이션에 다음 종속성이 포함되어야 합니다. 이렇게 포함하면 애플리케이션이 각 구성 요소에 맞게 올바르게 구성됩니다.  

### <a name="service-registry-dependency"></a>서비스 레지스트리 종속성

관리형 Azure Service Registry 서비스를 사용하려면 아래와 같이 `spring-cloud-starter-netflix-eureka-client` 종속성을 pom.xml 파일에 포함합니다.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

서비스 레지스트리 서버의 엔드포인트는 앱에 환경 변수로 자동 삽입됩니다. 애플리케이션이 서비스 레지스트리 서버에 자체적으로 등록되고, 기타 종속 마이크로서비스를 검색할 수 있습니다.

### <a name="distributed-configuration-dependency"></a>분산 구성 종속성

분산 구성을 사용하도록 설정하려면, pom.xml 파일의 종속성 섹션에 `spring-cloud-config-client` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 부트스트랩 구성에 `spring.cloud.config.enabled=false`를 지정하지 마세요. 그렇지 않으면 애플리케이션이 구성 서버 작업을 중지합니다.

### <a name="metrics-dependency"></a>메트릭 종속성

다음과 같이 pom.xml 파일의 종속성 섹션에 `spring-boot-starter-actuator` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 JMX 엔드포인트에서 메트릭을 정기적으로 끌어옵니다. Azure Portal을 사용하여 메트릭을 시각화할 수 있습니다.

### <a name="distributed-tracing-dependency"></a>분산 추적 종속성

pom.xml 파일의 종속성 섹션에 다음 `spring-cloud-starter-sleuth` 및 `spring-cloud-starter-zipkin` 종속성을 포함합니다.

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

 또한, Azure Application Insights 인스턴스가 Azure Spring Cloud 서비스 인스턴스와 작동하도록 설정해야 합니다. Azure Spring Cloud에서 Application Insights를 사용하는 방법을 알아보려면 [분산 추적에 대한 자습서](spring-cloud-tutorial-distributed-tracing.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Spring Cloud에 배포하기 위해 Java Spring 애플리케이션을 구성하는 방법을 알아보았습니다. 구성 서버 인스턴스를 설정하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [구성 서버 인스턴스를 설정하는 방법 알아보기](spring-cloud-tutorial-config-server.md)

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
