---
title: Azure 스프링 클라우드에서 배포할 응용 프로그램을 준비 하는 방법
description: Azure 스프링 클라우드에 배포할 응용 프로그램을 준비 하는 방법에 대해 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: ff0582e3c4f654ed2a7f5efdc9ce8fd7a226595a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906826"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 배포용 응용 프로그램 준비

::: zone pivot="programming-language-csharp"
Azure 스프링 클라우드는 Steeltoe 앱을 호스트, 모니터링, 크기 조정 및 업데이트 하기 위한 강력한 서비스를 제공 합니다. 이 문서에서는 Azure 스프링 클라우드에 배포 하기 위해 기존 Steeltoe 응용 프로그램을 준비 하는 방법을 보여 줍니다. 

이 문서에서는 Azure 스프링 클라우드에서 .NET Core Steeltoe 앱을 실행 하는 데 필요한 종속성, 구성 및 코드에 대해 설명 합니다. Azure 스프링 클라우드에 응용 프로그램을 배포 하는 방법에 대 한 자세한 내용은 [첫 번째 Azure 스프링 클라우드 응용 프로그램 배포](spring-cloud-quickstart.md)를 참조 하세요.

>[!Note]
> Azure Spring Cloud에 대한 Steeltoe 지원은 현재 공개 미리 보기로 제공됩니다. 퍼블릭 미리 보기 제품을 통해 고객은 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.  퍼블릭 미리 보기 기능 및 서비스는 프로덕션 용도로 사용되지 않습니다.  미리 보기 동안 제공되는 지원에 대한 자세한 내용은 [FAQ](https://azure.microsoft.com/support/faq/)를 참조하거나 [지원 요청](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 제출하세요.

##  <a name="supported-versions"></a>지원되는 버전

Azure 스프링 클라우드는 다음을 지원 합니다.

* .NET Core 3.1
* Steeltoe 2.4

## <a name="dependencies"></a>종속성

[SpringCloud](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 패키지를 설치 합니다.

## <a name="update-programcs"></a>Program.cs 업데이트

`Program.Main`메서드에서 메서드를 호출 합니다 `UseAzureSpringCloudService` .

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .UseAzureSpringCloudService();
```

## <a name="enable-eureka-server-service-discovery"></a>Eureka 서버 서비스 검색 사용

앱이 Azure 스프링 클라우드에서 실행 될 때 사용 되는 구성 원본에서 `spring.application.name` 프로젝트가 배포 될 Azure 스프링 클라우드 앱과 동일한 이름으로 설정 합니다.

예를 들어 이라는 .NET 프로젝트를 `EurekaDataProvider` Azure 스프링 클라우드 앱에 배포 하는 경우 `planet-weather-provider` 파일 * 의appSettings.js* 에 다음 JSON이 포함 되어야 합니다.

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>서비스 검색 사용

Eureka 서버 서비스 검색을 사용 하 여 서비스를 호출 하려면에 대해 HTTP 요청을 수행 합니다 `http://<app_name>` `app_name` . 여기서은 `spring.application.name` 대상 앱의 값입니다. 예를 들어 다음 코드는 서비스를 호출 합니다 `planet-weather-provider` .

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
이 항목에서는 Azure Spring Cloud에 배포하기 위해 기존 Java Spring 애플리케이션을 준비하는 방법을 보여줍니다. 제대로 구성된 경우, Azure Spring Cloud는 Java Spring Cloud 애플리케이션을 모니터링하고, 크기를 조정하고, 업데이트할 수 있는 강력한 서비스를 제공합니다.

이 예제를 실행하기 전에 [기본 빠른 시작](spring-cloud-quickstart.md)을 시도해 볼 수 있습니다.

다른 예제에서는 POM 파일이 구성된 경우 Azure Spring Cloud에 애플리케이션을 배포하는 방법을 설명합니다. 
* [첫 번째 앱 시작](spring-cloud-quickstart.md)
* [마이크로 서비스 빌드 및 실행](spring-cloud-quickstart-sample-app-introduction.md)

이 문서에서는 필요한 종속성과 이것을 POM 파일에 추가하는 방법을 설명합니다.

## <a name="java-runtime-version"></a>Java Runtime 버전

Spring/Java 애플리케이션만 Azure Spring Cloud에서 실행할 수 있습니다.

Azure Spring Cloud는 Java 8 및 Java 11을 모두 지원합니다. 호스팅 환경에는 Azure용 Azul Zulu OpenJDK의 최신 버전이 포함되어 있습니다. Azure용 Azul Zulu OpenJDK에 대한 자세한 내용은 [JDK 설치](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)를 참조하세요.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 및 Spring Cloud 버전

Azure Spring Cloud에 배포할 기존 Spring Boot 애플리케이션을 준비하려면 다음 섹션에 표시된 대로 애플리케이션 POM 파일에 Spring Boot 및 Spring Cloud 종속성을 포함합니다.

Azure Spring Cloud는 Spring Boot 버전 2.1 또는 버전 2.2의 Spring Boot 앱만 지원합니다. 아래 표에는 지원되는 Spring Boot 및 Spring Cloud 조합이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton
2.3 | Hoxton

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot 버전 2.1에 대한 종속성

Spring Boot 버전 2.1의 경우, 애플리케이션 POM 파일에 다음 종속성을 추가합니다.

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
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Spring Boot 버전 2.2에 대한 종속성

Spring Boot 버전 2.2의 경우, 애플리케이션 POM 파일에 다음 종속성을 추가합니다.

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
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>스프링 부팅 버전 2.3에 대 한 종속성

스프링 부팅 버전 2.3의 경우 응용 프로그램 POM 파일에 다음 종속성을 추가 합니다.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 클라이언트 종속성

Azure Spring Cloud는 Spring Cloud 구성 요소를 호스트하고 관리합니다. 구성 요소에는 Spring Cloud 서비스 레지스트리 및 Spring Cloud 구성 서버가 포함됩니다. 스프링 부트 2.2 또는 2.3을 사용 하는 것이 좋습니다. 스프링 부팅 2.1의 경우 Azure 스프링 클라우드 서비스 인스턴스와 통신할 수 있도록 종속성에 Azure 스프링 클라우드 클라이언트 라이브러리를 포함 해야 합니다.

다음 표에는 Spring Boot 및 Spring Cloud를 사용하는 앱의 올바른 Azure Spring Cloud 버전이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전 | Azure 스프링 클라우드 클라이언트 스타터 버전
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2. x | Hoxton | 필요하지 않음
2.3. x | Hoxton | 필요하지 않음

스프링 부트 2.1를 사용 하는 경우 pom.xml 파일에 다음 dependenciy를 포함 합니다.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Azure Spring Cloud 기능을 사용하도록 설정하기 위한 기타 권장 종속성

서비스 레지스트리에서 분산 추적까지 Azure Spring Cloud의 다양한 기본 제공 기능을 사용하도록 설정하려면 애플리케이션에 다음 종속성도 포함해야 합니다. 특정 앱에 해당하는 기능이 필요하지 않은 경우 이러한 종속성 중 일부를 삭제할 수 있습니다.

### <a name="service-registry"></a>서비스 레지스트리

관리형 Azure Service Registry 서비스를 사용하려면 아래와 같이 `spring-cloud-starter-netflix-eureka-client` 종속성을 pom.xml 파일에 포함합니다.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

서비스 레지스트리 서버의 엔드포인트는 앱에 환경 변수로 자동 삽입됩니다. 애플리케이션이 서비스 레지스트리 서버에 자체적으로 등록되고, 기타 종속 마이크로서비스를 검색할 수 있습니다.

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 주석

애플리케이션 소스 코드에 다음 주석을 추가합니다.
```java
@EnableDiscoveryClient
```
예를 들어 이전 예제의 piggymetrics 애플리케이션을 참조하세요.
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>분산 구성

분산 구성을 사용하도록 설정하려면, pom.xml 파일의 종속성 섹션에 `spring-cloud-config-client` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 부트스트랩 구성에 `spring.cloud.config.enabled=false`를 지정하지 마세요. 그렇지 않으면 애플리케이션이 구성 서버 작업을 중지합니다.

### <a name="metrics"></a>메트릭

다음과 같이 pom.xml 파일의 종속성 섹션에 `spring-boot-starter-actuator` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 JMX 엔드포인트에서 메트릭을 정기적으로 끌어옵니다. Azure Portal을 사용하여 메트릭을 시각화할 수 있습니다.

 > [!WARNING]
 > `spring.jmx.enabled=true`구성 속성에서를 지정 하십시오. 그렇지 않으면 Azure Portal에서 메트릭을 시각화할 수 없습니다.

### <a name="distributed-tracing"></a>분산 추적

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

 또한, Azure Application Insights 인스턴스가 Azure Spring Cloud 서비스 인스턴스와 작동하도록 설정해야 합니다. Azure 스프링 클라우드에서 Application Insights를 사용 하는 방법에 대 한 자세한 내용은 [분산 추적에 대 한 설명서](spring-cloud-tutorial-distributed-tracing.md)를 참조 하세요.

## <a name="see-also"></a>참고 항목
* [애플리케이션 로그 및 메트릭 분석](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [구성 서버 설정](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Azure Spring Cloud에서 분산 추적 사용](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring 빠른 시작 가이드](https://spring.io/quickstart)
* [Spring Boot 설명서](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>다음 단계

이 항목에서는 Azure Spring Cloud에 배포하기 위해 Java Spring 애플리케이션을 구성하는 방법을 알아보았습니다. 구성 서버 인스턴스를 설정 하는 방법을 알아보려면 [구성 서버 인스턴스 설정](spring-cloud-tutorial-config-server.md)을 참조 하세요.

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end
