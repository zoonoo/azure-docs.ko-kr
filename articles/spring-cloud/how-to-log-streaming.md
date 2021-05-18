---
title: 실시간으로 Azure Spring Cloud 앱 로그 스트림
description: 로그 스트리밍을 사용하여 애플리케이션 로그를 즉시 보는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1eeb291c7a058efd8905e95ebf1ea14fed046691
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878287"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>실시간으로 Azure Spring Cloud 앱 로그 스트림

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure 스프링 클라우드를 사용하면 Azure CLI의 로그 스트리밍을 통해 문제 해결을 위한 실시간 애플리케이션 콘솔 로그를 가져올 수 있습니다. 또한 [진단 설정을 사용하여 로그 및 메트릭 분석](./diagnostic-services.md)을 할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 최소 버전 0.2.0의 Spring Cloud [Azure CLI 확장](/cli/azure/install-azure-cli)을 설치합니다.
* 애플리케이션(예: [Spring Cloud 앱](./spring-cloud-quickstart.md))을 실행하는 **Azure Spring Cloud** 의 인스턴스입니다.

> [!NOTE]
>  ASC CLI 확장은 버전 0.2.0에서 0.2.1로 업데이트됩니다. 이 변경 내용은 로그 스트리밍에 대한 명령 구문 `az spring-cloud app log tail`에 영향을 줍니다. 이 구문은 `az spring-cloud app logs`로 대체되었습니다. 이 명령 `az spring-cloud app log tail`은 이후 릴리스에서 더 이상 사용되지 않습니다. 버전 0.2.0을 사용하는 경우 0.2.1로 업그레이드할 수 있습니다. 먼저 `az extension remove -n spring-cloud` 명령을 사용하여 이전 버전을 제거합니다.  그런 다음 `az extension add -n spring-cloud` 명령으로 0.2.1을 설치합니다.

## <a name="use-cli-to-tail-logs"></a>CLI를 사용하여 로그 추적

리소스 그룹 및 서비스 인스턴스 이름을 반복적으로 지정하지 않으려면 기본 리소스 그룹 이름과 클러스터 이름을 설정합니다.
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
다음 예제에서는 리소스 그룹 및 서비스 이름이 명령에서 생략됩니다.

### <a name="tail-log-for-app-with-single-instance"></a>단일 인스턴스를 사용하는 앱에 대한 로그 추적
auth-service라는 이름의 앱에 인스턴스가 하나만 있는 경우 다음 명령을 사용하여 앱 인스턴스의 로그를 볼 수 있습니다.
```azurecli
az spring-cloud app logs -n auth-service
```
이렇게 하면 로그가 반환됩니다.
```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>여러 인스턴스를 사용하는 앱에 대한 로그 추적
`auth-service`이라는 앱에 대한 인스턴스가 여러 개인 경우 `-i/--instance` 옵션을 사용하여 인스턴스 로그를 볼 수 있습니다. 

먼저, 다음 명령을 사용하여 앱 인스턴스 이름을 가져올 수 있습니다.

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
결과는 다음과 같습니다.

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
그런 다음 옵션 `-i/--instance` 옵션을 사용하여 앱 인스턴스의 로그를 스트리밍할 수 있습니다.

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Azure Portal에서 앱 인스턴스의 세부 정보를 가져올 수도 있습니다.  Azure 스프링 클라우드 서비스의 왼쪽 탐색 창에서 **앱** 을 선택한 후 **앱 인스턴스** 를 선택합니다.

### <a name="continuously-stream-new-logs"></a>계속해서 새 로그 스트림
기본적으로 `az spring-cloud ap log tail`은 앱 콘솔로 스트리밍되는 기존 로그만 인쇄한 후 종료됩니다. 새 로그를 스트리밍하려면 -f (--follow)를 추가합니다.  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
지원되는 모든 로깅 옵션을 확인하려면 다음을 실행합니다.
```azurecli
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>다음 단계
* [빠른 시작: 로그, 메트릭 및 추적을 사용하여 Azure Spring Cloud 앱 모니터링](spring-cloud-quickstart-logs-metrics-tracing.md)
* [진단 설정을 사용하여 로그 및 메트릭 분석](./diagnostic-services.md)

