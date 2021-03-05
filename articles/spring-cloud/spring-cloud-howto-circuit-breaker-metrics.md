---
title: 스프링 클라우드 Resilience4J 회로 차단기 메트릭 수집
description: 스프링 클라우드 Resilience4J 회로 차단기 메트릭을 수집 하는 방법입니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0227fcebd6d5a136e3306573b7b5aa0d6e39ba1f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122802"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>스프링 클라우드 Resilience4J 회로 차단기 메트릭 (미리 보기) 수집

이 문서에서는 Application Insights java in-process 에이전트로 스프링 클라우드 Resilience4j 회로 차단기 메트릭을 수집 하는 방법을 설명 합니다.  이 기능을 사용 하 여 Application Insights에서 resilience4j 회로 차단기의 메트릭을 모니터링할 수 있습니다.

[스프링-회로 차단기 데모](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 를 사용 하 여 작동 방식을 표시 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Java [In-Process agent for Application Insights 가이드](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights)에서 java In-Process 에이전트를 사용 하도록 설정 합니다. 

* [Application Insights 가이드](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)에서 resilience4j 메트릭에 대 한 차원 컬렉션을 사용 하도록 설정 합니다.

* 개발 컴퓨터에서 아직 사용 하지 않은 경우 git, Maven 및 Java를 설치 합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

다음 절차에서는 앱을 빌드 및 배포 합니다.

1. 데모 리포지토리를 복제 하 고 빌드합니다.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. 끝점을 사용 하 여 응용 프로그램 만들기

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. 응용 프로그램을 배포합니다.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Resilience4j에 필요한 종속성을 포함 합니다.
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * 고객 코드는 `CircuitBreakerFactory` `bean` 스프링 클라우드 회로 차단기 스타터를 포함 하는 경우 자동으로 생성 되는의 API를 사용 해야 합니다. 자세한 내용은 [스프링 클라우드 회로 차단기](https://spring.io/projects/spring-cloud-circuitbreaker#overview)를 참조 하세요.
>
> * 다음 2 개의 종속성은 위의 resilient4j 패키지와 충돌 합니다.  고객에 게는 포함 되지 않아야 합니다.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> 게이트웨이 응용 프로그램에서 제공 하는 URL로 이동 하 여 다음과 같이 [스프링-회로 차단기-데모](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 에서 끝점에 액세스 합니다.
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>포털에서 Resilence4j 메트릭 찾기

1. Azure 스프링 클라우드 포털에서 **Application Insights** 블레이드를 선택 하 고 **Application Insights** 를 클릭 합니다.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. **Application Insights** 페이지에서 **메트릭** 을 선택 합니다.  **메트릭 네임 스페이스** 에서 **azure applicationinsights** 를 선택 합니다.  또한 **평균이** 있는 **resilience4j_circuitbreaker_buffered_calls** 메트릭을 선택 합니다.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. **Resilience4j_circuitbreaker_calls** 메트릭 및 **평균** 을 선택 합니다.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. **Resilience4j_circuitbreaker_calls** 메트릭 및 **평균** 을 선택 합니다.  **필터 추가** 를 클릭 한 다음 이름을 **createNewAccount** 로 선택 합니다.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. **Resilience4j_circuitbreaker_calls** 메트릭 및 **평균** 을 선택 합니다.  그런 다음 **분할 적용** 을 클릭 하 고 **종류** 를 선택 합니다.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. **Resilience4j_circuitbreaker_calls**, '**resilience4j_circuitbreaker_buffered_calls** 및 **평균** 을 사용 하 여 **resilience4j_circuitbreaker_slow_calls** 메트릭을 선택 합니다.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>참고 항목

* [애플리케이션 인사이트](./spring-cloud-howto-application-insights.md)
* [분산된 추적](spring-cloud-tutorial-distributed-tracing.md)
* [회로 차단기 대시보드](spring-cloud-tutorial-circuit-breaker.md)