---
title: 실시간으로 Azure 스프링 클라우드 앱 로그 스트림
description: 로그 스트리밍을 사용 하 여 응용 프로그램 로그를 즉시 보는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264002"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>실시간으로 Azure 스프링 클라우드 앱 로그 스트림
Azure 스프링 클라우드를 사용 하면 Azure CLI의 로그 스트리밍을 통해 문제 해결을 위한 실시간 응용 프로그램 콘솔 로그를 가져올 수 있습니다. [진단 설정을 사용 하 여 로그 및 메트릭을 분석할](./diagnostic-services.md)수도 있습니다.

## <a name="prerequisites"></a>필수 조건

* 스프링 클라우드, 최소 버전 0.2.0에 대 한 [Azure CLI 확장](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) 을 설치 합니다.
* 응용 프로그램을 실행 하는 **Azure 스프링 클라우드의** 인스턴스 (예: [스프링 클라우드 앱](./spring-cloud-quickstart-launch-app-cli.md)).

## <a name="use-cli-to-tail-logs"></a>CLI를 사용 하 여 비상 로그

리소스 그룹 및 서비스 인스턴스 이름을 반복적으로 지정 하지 않으려면 기본 리소스 그룹 이름과 클러스터 이름을 설정 합니다.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
다음 예제에서는 리소스 그룹 및 서비스 이름이 명령에서 생략 됩니다.

### <a name="tail-log-for-app-with-single-instance"></a>단일 인스턴스를 사용 하는 앱에 대 한 비상 로그
Auth service 라는 앱에 인스턴스가 하나만 있는 경우 다음 명령을 사용 하 여 앱 인스턴스의 로그를 볼 수 있습니다.
```
az spring-cloud app log tail -n auth-service
```
로그를 반환 합니다.
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>여러 인스턴스를 사용 하는 앱에 대 한 비상 로그
`auth-service`라는 응용 프로그램에 대해 여러 인스턴스가 있는 경우 `-i/--instance` 옵션을 사용 하 여 인스턴스 로그를 볼 수 있습니다. 

먼저, 다음 명령을 사용 하 여 앱 인스턴스 이름을 가져올 수 있습니다.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
결과 포함:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
그런 다음 옵션 `-i/--instance` 옵션을 사용 하 여 앱 인스턴스의 로그를 스트리밍할 수 있습니다.

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Azure Portal에서 앱 인스턴스의 세부 정보를 가져올 수도 있습니다.  Azure 스프링 클라우드 서비스의 왼쪽 탐색 창에서 **앱** 을 선택한 후 **앱 인스턴스**를 선택 합니다.

### <a name="continuously-stream-new-logs"></a>계속 해 서 새 로그 스트림
기본적으로 `az spring-cloud ap log tail`는 응용 프로그램 콘솔로 스트리밍되는 기존 로그만 인쇄 한 후 종료 됩니다. 새 로그를 스트리밍하려면-f (--follow)를 추가 합니다.  

```
az spring-cloud app log tail -n auth-service -f
``` 
지원 되는 모든 로깅 옵션을 확인 하려면:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>다음 단계

* [진단 설정을 사용 하 여 로그 및 메트릭 분석](./diagnostic-services.md)

 





