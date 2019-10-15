---
title: Azure Spring Cloud 문제 해결 가이드 | Microsoft Docs
description: Azure Spring Cloud 문제 해결 가이드
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ebb960085691206b096090813636ef56366e6536
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038362"
---
# <a name="troubleshooting-guide-for-common-problems"></a>일반적인 문제 해결 가이드

이 문서에서는 Azure Spring Cloud 내에서 작업하는 개발자의 몇 가지 일반적인 문제와 문제 해결 단계에 대해 자세히 설명합니다. [FAQ 문서](spring-cloud-faq.md)도 참조하는 것이 좋습니다.

## <a name="availability-performance-and-application-issues"></a>가용성, 성능 및 애플리케이션 문제

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>애플리케이션을 시작할 수 없음(예를 들어 엔드포인트를 연결할 수 없거나 몇 번 다시 시도한 후에 502를 반환함)

로그를 _Azure Log Analytics_로 내보냅니다. Spring 애플리케이션 로그에 대한 테이블 이름은 `AppPlatformLogsforSpring`입니다. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md)을 참조하세요.

로그에서 다음 오류를 발견하면 두 가지 문제 중 하나가 발생할 수 있습니다.

`org.springframework.context.ApplicationContextException: Unable to start web server`

* 빈 중 하나 또는 해당 종속성 중 하나가 누락되었습니다.
* 빈 속성 중 하나가 누락되거나 잘못되었습니다. 이 경우 `java.lang.IllegalArgumentException`이 표시될 수 있습니다.

또한 서비스 바인딩으로 인해 애플리케이션 시작 오류가 발생할 수도 있습니다. 바인딩된 서비스와 관련된 키워드를 사용하여 로그를 쿼리합니다.  예를 들어 애플리케이션에 로컬 시스템 시간으로 설정된 MySQL 인스턴스에 대한 바인딩이 있다고 가정합니다. 애플리케이션이 시작되지 않으면 로그에서 다음 오류를 발견할 수 있습니다.

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

이 오류를 해결하려면 MySql 인스턴스의 `server parameters`로 이동하여 `time_zone`을 `SYSTEM`에서 `+0:00`으로 변경합니다.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>애플리케이션이 충돌하거나 예기치 않은 오류를 throw함

애플리케이션 충돌이 디버그되면 먼저 애플리케이션의 실행 상태와 검색 상태를 확인합니다. Azure Portal에서 _앱 관리_로 이동하여 모든 애플리케이션이 _실행 중_ 및 _UP_인지 확인합니다.

* 상태가 _실행 중_이지만 검색 상태가 _UP_이 아닌 경우 [애플리케이션을 등록할 수 없음](#my-application-cannot-be-registered)으로 이동합니다.

* 검색 상태가 _UP_인 경우 _메트릭_으로 이동하여 애플리케이션의 상태를 확인합니다. 다음 메트릭을 검사합니다.


  - `TomcatErrorCount`(_tomcat.global.error_): 모든 Spring 애플리케이션 예외는 여기에서 계산됩니다. 이 수가 크면 _Azure Log Analytics_로 이동하여 애플리케이션 로그를 검사합니다.

  - `AppMemoryMax`(_jvm.memory.max_): 애플리케이션에서 사용할 수 있는 최대 메모리 양입니다. 정의되지 않았거나, 정의된 경우 시간이 지남에 따라 변경될 수 있습니다. 정의된 경우 사용되는 메모리와 커밋된 메모리의 양은 항상 최대 메모리보다 작거나 같습니다. 그러나 사용된 메모리가 최대 메모리보다 작거나 같은 경우에도 사용된 메모리가 커밋된 메모리보다 크도록 사용된 메모리를 늘리려고 하면 `OutOfMemoryError`로 인해 메모리 할당이 실패할 수 있습니다. 이 경우 `-Xmx` 매개 변수를 통해 최대 힙 크기를 늘립니다.

  - `AppMemoryUsed`(_jvm.memory.used_): 애플리케이션에서 현재 사용하고 있는 메모리의 양(바이트)입니다. 일반적인 로드 Java 애플리케이션의 경우 이 메트릭 계열은 '톱니' 패턴으로 구성됩니다. 메모리 사용이 꾸준히 증가하고 조금씩 감소하다가 급격하게 떨어지는 패턴이 반복됩니다. 이는 Java 가상 머신 내부의 가비지 수집 때문이며, 수집 작업에서 '톱니'의 하강을 나타냅니다.
    이 메트릭은 메모리 문제(예: 초기의 메모리 급증, 특정 논리 경로에 대한 서지 메모리 할당, 점진적 메모리 누수)를 식별하는 데 중요합니다.

  자세한 내용은 [메트릭](spring-cloud-concept-metrics.md)을 참조 하세요.

_Azure Log Analytics_를 시작하려면 [이 시작 문서](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)를 참조하세요.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>애플리케이션에서 높은 CPU 사용 또는 높은 메모리 사용 경험

애플리케이션에서 높은 CPU/메모리 사용량이 발생하는 경우 다음 두 가지 상황 중 하나에 해당합니다.
* 모든 앱 인스턴스에서 높은 CPU/메모리 사용량이 발생합니다. 또는
* 일부 앱 인스턴스에서 높은 CPU/메모리 사용량이 발생합니다.

어떤 상황인지 확인하려면 다음을 수행합니다.

1. _메트릭_으로 이동하고 `Service CPU Usage Percentage` 또는 `Service Memory Used`를 선택합니다.
2. `App=` 필터를 추가하여 모니터링할 애플리케이션을 지정합니다.
3. `Instance`를 기준으로 메트릭을 분할합니다.

모든 앱 인스턴스에서 높은 CPU/메모리 사용량이 발생하는 경우 애플리케이션을 확장하거나 CPU/메모리를 강화해야 합니다. 자세한 내용은 [애플리케이션 크기 조정](spring-cloud-tutorial-scale-manual.md)을 참조하세요.

일부 인스턴스에서 높은 CPU/메모리 사용량이 발생하는 경우 인스턴스 상태와 검색 상태를 확인합니다.

자세한 내용은 [메트릭](spring-cloud-concept-metrics.md)을 참조하세요.

모든 인스턴스가 시작되어 실행되고 있는 경우 _Azure Log Analytics_로 이동하여 애플리케이션 로그를 쿼리하고, 코드 논리를 검토하여 크기 조정 분할에 영향을 줄 수 있는 로그를 확인합니다. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md)을 참조하세요.

_Azure Log Analytics_를 시작하려면 [이 시작 문서](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)를 참조하세요. [Kusto 쿼리 언어](https://docs.microsoft.com/azure/kusto/query/)를 사용하여 로그를 쿼리합니다.

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Spring 애플리케이션을 Azure Spring Cloud에 온보딩하기 전의 검사 목록

* 지정된 Java 런타임 버전을 사용하여 애플리케이션을 로컬로 실행할 수 있습니다.
* 환경 구성(CPU/RAM/인스턴스)에서 애플리케이션 공급자가 설정한 최소 요구 사항을 충족합니다.
* 구성 항목에 예상 값이 있습니다. 자세한 내용은 [Config 서버](spring-cloud-tutorial-config-server.md)를 참조하세요.
* 환경 변수에 필요한 값이 있습니다.
* JVM 매개 변수에 필요한 값이 있습니다.
* 애플리케이션 패키지에서 포함된 _Config 서버_ 및 _Spring 서비스 레지스트리_ 서비스를 사용하지 않도록 설정하거나 제거하는 것이 좋습니다.
* Azure 리소스가 _서비스 바인딩_을 통해 바인딩될 경우 대상 리소스가 실행 중인지 확인합니다.

## <a name="configuration-and-management"></a>구성 및 관리

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring Cloud 서비스 인스턴스를 만드는 동안 문제가 발생함

포털을 통해 _Azure Spring Cloud_ 서비스 인스턴스를 프로비저닝하려고 하면 Azure Spring Cloud에서 유효성 검사를 수행합니다.

그러나 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 또는 [Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/)을 통해 _Azure Spring Cloud_ 서비스 인스턴스를 프로비저닝하려고 하면 다음을 확인합니다.

* 구독이 활성 상태입니다.
* 위치가 _Azure Spring Cloud_에서 [지원](spring-cloud-faq.md)합니다.
* 인스턴스에 대한 리소스 그룹이 이미 만들어졌습니다.
* 리소스 이름이 명명 규칙을 준수합니다. (소문자, 숫자 및 하이픈만 포함할 수 있습니다. 첫 번째 자리는 반드시 문자여야 합니다. 마지막 문자는 문자 또는 숫자여야 합니다. 값은 2~32자여야 합니다.)

Resource Manager 템플릿을 통해 _Azure Spring Cloud_ 서비스 인스턴스를 프로비저닝하려는 경우 https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates 를 참조하여 템플릿 구문을 확인합니다.

_Azure Spring Cloud_ 서비스 인스턴스의 이름은 `azureapps.io`에서 하위 도메인 이름을 요청하는 데 사용되므로 해당 이름이 기존 이름과 충돌하면 프로비저닝이 실패합니다. 활동 로그에서 추가 정보를 확인할 수 있습니다.

### <a name="i-cannot-deploy-a-jar-package"></a>JAR 패키지를 배포할 수 없음

JAR/소스 패키지는 포털 또는 Resource Manager 템플릿을 통해 업로드할 수 없습니다.

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 애플리케이션 패키지를 배포하면 배포 진행 상황이 정기적으로 폴링되고, 결국에는 배포 결과가 표시됩니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

애플리케이션이 올바른 [jar 형식 실행 파일](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)로 패키지되어 있는지 확인합니다. 그렇지 않은 경우 다음과 같은 오류가 표시됩니다.

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>소스 패키지를 배포할 수 없음

JAR/소스 패키지는 포털 또는 Resource Manager 템플릿을 통해 업로드할 수 없습니다.

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 통해 애플리케이션 패키지를 배포하면 배포 진행 상황이 주기적으로 폴링되고 이 끝에 배포 결과가 표시됩니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 빌드 및 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

그러나 하나의 _Azure Spring Cloud_ 서비스 인스턴스는 한 번에 하나의 소스 패키지에 대한 하나의 빌드 작업만 트리거할 수 있습니다. 자세한 내용은 [애플리케이션 배포](spring-cloud-quickstart-launch-app-portal.md) 및 [스테이징 환경 가이드](spring-cloud-howto-staging-environment.md)를 참조하세요.

### <a name="my-application-cannot-be-registered"></a>애플리케이션을 등록할 수 없음

대부분의 경우 필수 종속성/서비스 검색이 POM 파일에서 올바르게 구성되지 않은 경우에 발생합니다. 구성된 후에는 기본 제공 서비스 레지스트리 서버 엔드포인트가 애플리케이션에서 환경 변수로 삽입됩니다. 그런 다음, 애플리케이션에서 자체를 서비스 레지스트리 서버에 등록하고, 다른 종속 마이크로서비스를 검색합니다.

새로 등록된 인스턴스가 트래픽을 받기 시작할 때까지 2분 이상 기다립니다.

기존 Spring Cloud 기반 솔루션을 Azure로 마이그레이션하는 경우 _Azure Spring Cloud_에서 제공하는 관리형 인스턴스와 충돌하지 않도록 임시 _서비스 레지스트리_ 및 _Config 서버_ 인스턴스를 제거하거나 사용하지 않도록 설정해야 합니다.

_Azure Log Analytics_에서 _서비스 레지스트리_ 클라이언트 로그를 확인할 수도 있습니다. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md)을 참조하세요.

_Azure Log Analytics_를 시작하려면 [이 시작 문서](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)를 참조하세요. [Kusto 쿼리 언어](https://docs.microsoft.com/azure/kusto/query/)를 사용하여 로그를 쿼리합니다.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>애플리케이션에 대한 메트릭 또는 로그를 찾을 수 없음

_앱 관리_로 이동하여 애플리케이션이 _실행 중_ 및 _UP_ 상태인지 확인합니다.

_JVM_에서 메트릭을 볼 수 있지만 _Tomcat_에서는 메트릭을 볼 수 없는 경우 애플리케이션 패키지에서 `spring-boot-actuator` 종속성을 사용하도록 설정되어 있고 성공적으로 부팅되는지 확인합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

애플리케이션 로그를 스토리지 계정에 보관할 수 있지만 _Azure Log Analytics_로 보낼 수 없는 경우 [작업 영역을 올바르게 설정](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)했는지 확인합니다. _Azure Log Analytics_의 체험 계층을 사용하는 경우 [SLA는 체험 계층에 제공되지 않습니다](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).