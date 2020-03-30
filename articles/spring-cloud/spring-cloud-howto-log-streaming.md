---
title: 실시간으로 Azure Spring Cloud 앱 로그 스트림
description: 로그 스트리밍을 사용하여 응용 프로그램 로그를 즉시 보는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192203"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>실시간으로 Azure Spring Cloud 앱 로그 스트림
Azure Spring Cloud를 사용하면 Azure CLI에서 로그 스트리밍을 통해 문제 해결을 위한 실시간 응용 프로그램 콘솔 로그를 얻을 수 있습니다. [진단 설정으로 로그 및 메트릭을 분석할](./diagnostic-services.md)수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 스프링 클라우드, 최소 버전 0.2.0에 대한 [Azure CLI 확장을](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) 설치합니다.
* 실행 중인 응용 프로그램이 있는 **Azure 스프링 클라우드의** 인스턴스(예: [스프링 클라우드 앱)](./spring-cloud-quickstart-launch-app-cli.md)

> [!NOTE]
>  ASC CLI 확장은 버전 0.2.0에서 0.2.1로 업데이트됩니다. 이 변경 사항은 로그 스트리밍에 대한 `az spring-cloud app log tail`명령구문에 영향을 `az spring-cloud app logs`줍니다. 명령: `az spring-cloud app log tail` 이후 릴리스에서 더 이상 사용되지 않습니다. 버전 0.2.0을 사용한 경우 0.2.1로 업그레이드할 수 있습니다. 먼저 명령으로 이전 버전을 제거합니다. `az extension remove -n spring-cloud`  그런 다음 명령에 의해 0.2.1을 설치합니다. `az extension add -n spring-cloud`

## <a name="use-cli-to-tail-logs"></a>꼬리 로그에 CLI 사용

리소스 그룹 및 서비스 인스턴스 이름을 반복적으로 지정하지 않으려면 기본 리소스 그룹 이름 및 클러스터 이름을 설정합니다.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
다음 예제에서는 리소스 그룹 및 서비스 이름이 명령에서 생략됩니다.

### <a name="tail-log-for-app-with-single-instance"></a>단일 인스턴스가 있는 앱용 테일 로그
auth-service라는 앱에 인스턴스가 하나만 있는 경우 다음 명령을 통해 앱 인스턴스의 로그를 볼 수 있습니다.
```
az spring-cloud app logs -n auth-service
```
이렇게 하면 로그가 반환됩니다.
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>여러 인스턴스가 있는 앱의 테일 로그
명명된 `auth-service`앱에 대해 여러 인스턴스가 있는 경우 `-i/--instance` 옵션을 사용하여 인스턴스 로그를 볼 수 있습니다. 

먼저 다음 명령을 통해 앱 인스턴스 이름을 얻을 수 있습니다.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
결과:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
그런 다음 옵션 `-i/--instance` 옵션을 통해 앱 인스턴스의 로그를 스트리밍할 수 있습니다.

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Azure 포털에서 앱 인스턴스에 대한 세부 정보를 얻을 수도 있습니다.  Azure Spring 클라우드 서비스의 왼쪽 탐색 창에서 **앱을** 선택한 후 **앱 인스턴스를 선택합니다.**

### <a name="continuously-stream-new-logs"></a>새 로그를 지속적으로 스트리밍
기본적으로 앱 `az spring-cloud ap log tail` 콘솔로 스트리밍된 기존 로그만 인쇄한 다음 종료합니다. 새 로그를 스트리밍하려면 -f(--follow)를 추가합니다.  

```
az spring-cloud app logs -n auth-service -f
``` 
지원되는 모든 로깅 옵션을 확인하려면 다음을 수행하십시오.
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>다음 단계

* [진단 설정으로 로그 및 메트릭 분석](./diagnostic-services.md)

 





