---
title: Azure Spring Cloud 문제 해결 가이드 | Microsoft Docs
description: Azure Spring Cloud 문제 해결 가이드
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277573"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>일반적인 Azure 스프링 클라우드 문제 해결

이 문서에서는 Azure Spring 클라우드 개발 문제 해결에 대한 지침을 제공합니다. 자세한 내용은 [Azure 스프링 클라우드 FAQ를](spring-cloud-faq.md)참조하십시오.

## <a name="availability-performance-and-application-issues"></a>가용성, 성능 및 애플리케이션 문제

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>응용 프로그램을 시작할 수 없습니다(예: 끝점을 연결할 수 없거나 몇 번다시 시도한 후 502를 반환).

로그를 Azure Log Analytics로 내보냅니다. 스프링 응용 프로그램 로그에 대한 테이블은 *AppPlatformLogsforSpring입니다.* 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석을](diagnostic-services.md)참조하십시오.

로그에 다음과 같은 오류 메시지가 나타날 수 있습니다.

> "org.springframework.context.ApplicationContext예외: 웹 서버를 시작할 수 없습니다."

이 메시지는 다음과 같은 두 가지 문제 중 하나를 나타냅니다. 
* 빈 중 하나 또는 해당 종속성 중 하나가 누락되었습니다.
* 빈 속성 중 하나가 누락되거나 잘못되었습니다. 이 경우 "java.lang.IllegalArgumentException"이 표시될 수 있습니다.

서비스 바인딩으로 인해 응용 프로그램 시작 오류가 발생할 수도 있습니다. 로그를 쿼리하려면 바인딩된 서비스와 관련된 키워드를 사용합니다. 예를 들어 응용 프로그램에 로컬 시스템 시간으로 설정된 MySQL 인스턴스에 대한 바인딩이 있다고 가정해 보겠습니다. 응용 프로그램을 시작하지 못하면 로그에 다음과 같은 오류 메시지가 나타날 수 있습니다.

> "java.sql.SQLException: 서버 표준 시간대 값 '조정된 유니버설 타임'을 인식하지 않거나 두 개 이상의 표준 시간대를 나타냅니다."

이 오류를 해결하려면 MySQL `server parameters` 인스턴스로 이동하여 `time_zone` *값을 SYSTEM에서* *+0:00로*변경합니다.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>애플리케이션이 충돌하거나 예기치 않은 오류를 throw함

응용 프로그램 충돌을 디버깅하는 경우 응용 프로그램의 실행 상태 및 검색 상태를 확인하는 것으로 시작합니다. 이렇게 하려면 Azure 포털의 _앱 관리로_ 이동하여 모든 응용 프로그램의 상태가 _실행 되고_ _UP인지_확인합니다.

* 상태가 실행 _중이지만_ 검색 상태가 _UP이_아닌 경우 ["내 응용 프로그램을 등록할 수 없습니다"](#my-application-cant-be-registered) 섹션으로 이동합니다.

* 검색 상태가 _UP_인 경우 메트릭으로 이동하여 애플리케이션의 상태를 확인합니다. 다음 메트릭을 검사합니다.


  - `TomcatErrorCount`_(tomcat.global.error):_ 모든 스프링 응용 프로그램 예외는 여기에 계산됩니다. 이 수가 크면 Azure Log Analytics로 이동하여 애플리케이션 로그를 검사합니다.

  - `AppMemoryMax`_(jvm.memory.max):_ 응용 프로그램에서 사용할 수 있는 최대 메모리 양입니다. 금액이 정의되지 않았거나 정의된 경우 시간이 지남에 따라 변경될 수 있습니다. 정의된 경우 사용된 메모리와 커밋된 메모리의 양은 항상 최대보다 작거나 동일합니다. 그러나 할당이 `OutOfMemoryError` 사용된 메모리를 늘리려고 하면 메시지에 실패할 수 > > *사용됨*= max는 여전히 true *<입니다.* 이러한 경우 `-Xmx` 매개 변수를 사용하여 최대 힙 크기를 늘리십시오.

  - `AppMemoryUsed`_(jvm.memory.used):_ 현재 응용 프로그램에서 사용하는 바이트의 메모리 양입니다. 일반 로드 Java 응용 프로그램의 경우 이 메트릭 계열은 *톱니* 패턴을 형성하여 메모리 사용량이 작은 단위로 꾸준히 증가하고 감소하고 갑자기 많이 떨어지고 패턴이 다시 발생합니다. 이 메트릭 계열은 수집 작업이 톱니 패턴의 삭제를 나타내는 Java 가상 시스템 내부의 가비지 수집으로 인해 발생합니다.
    
    이 메트릭은 다음과 같은 메모리 문제를 식별하는 데 중요합니다.
    * 처음에 메모리 폭발.
    * 특정 논리 경로에 대한 서지 메모리 할당입니다.
    * 점진적인 메모리 누수.

  자세한 내용은 [메트릭 을](spring-cloud-concept-metrics.md)참조하십시오.

Azure 로그 분석에 대해 자세히 알아보려면 [Azure 모니터에서 로그 분석 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조하십시오.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>애플리케이션에서 높은 CPU 사용 또는 높은 메모리 사용 경험

응용 프로그램에서 CPU 또는 메모리 사용량이 높은 경우 다음 두 가지 중 하나가 사실입니다.
* 모든 앱 인스턴스는 높은 CPU 또는 메모리 사용량을 경험합니다.
* 일부 앱 인스턴스는 CPU 또는 메모리 사용량이 높습니다.

어떤 상황이 적용되는지 확인하려면 다음을 수행하십시오.

1. 메트릭으로 이동한 다음 **서비스 CPU 사용률** 또는 **사용된 서비스 메모리**중 하나를 **선택합니다.**
2. **App=** 필터를 추가하여 모니터링할 응용 프로그램을 지정합니다.
3. 메트릭을 **인스턴스**로 분할합니다.

*모든 인스턴스에* CPU 또는 메모리 사용량이 높은 경우 응용 프로그램을 확장하거나 CPU 또는 메모리 사용량을 확장해야 합니다. 자세한 내용은 [자습서: Azure 스프링 클라우드에서 응용 프로그램 확장](spring-cloud-tutorial-scale-manual.md)을 참조하세요.

*일부 인스턴스에서* CPU 또는 메모리 사용량이 높은 경우 인스턴스 상태와 검색 상태를 확인합니다.

자세한 내용은 [Azure 스프링 클라우드에 대한 메트릭을](spring-cloud-concept-metrics.md)참조하십시오.

모든 인스턴스가 실행 중인 경우 Azure Log Analytics로 이동하여 응용 프로그램 로그를 쿼리하고 코드 논리를 검토합니다. 이렇게 하면 규모 분할에 영향을 줄 수 있는 지 여부를 확인할 수 있습니다. 자세한 내용은 [진단 설정을 사용하는 로그 및 메트릭 분석을](diagnostic-services.md)참조하십시오.

Azure 로그 분석에 대해 자세히 알아보려면 [Azure 모니터에서 로그 분석 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조하십시오. [Kusto 쿼리 언어를](https://docs.microsoft.com/azure/kusto/query/)사용하여 로그를 쿼리합니다.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Azure 스프링 클라우드에 스프링 응용 프로그램을 배포하기 위한 검사 목록

응용 프로그램을 온보온하기 전에 다음 기준을 충족하는지 확인하십시오.

* 지정된 Java 런타임 버전을 사용하여 애플리케이션을 로컬로 실행할 수 있습니다.
* 환경 구성(CPU/RAM/인스턴스)에서 애플리케이션 공급자가 설정한 최소 요구 사항을 충족합니다.
* 구성 항목에 예상 된 값이 있습니다. 자세한 내용은 [Config 서버](spring-cloud-tutorial-config-server.md)를 참조하세요.
* 환경 변수에 예상 값이 있습니다.
* JVM 매개 변수에는 예상 값이 있습니다.
* 응용 프로그램 패키지에서 포함된 _Config 서버_ 및 _스프링 서비스 레지스트리_ 서비스를 사용하지 않도록 설정하거나 제거하는 것이 좋습니다.
* Azure 리소스가 _서비스 바인딩_을 통해 바인딩될 경우 대상 리소스가 실행 중인지 확인합니다.

## <a name="configuration-and-management"></a>구성 및 관리

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring 클라우드 서비스 인스턴스를 만드는 데 문제가 발생했습니다.

Azure 포털을 사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하는 경우 Azure Spring 클라우드 유효성 검사를 수행 합니다.

그러나 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 또는 [Azure 리소스 관리자 템플릿을](https://docs.microsoft.com/azure/azure-resource-manager/)사용하여 Azure Spring Cloud 서비스 인스턴스를 설정하려고 하면 다음을 확인합니다.

* 구독이 활성 상태입니다.
* 위치는 Azure 스프링 클라우드에서 [지원됩니다.](spring-cloud-faq.md)
* 인스턴스에 대한 리소스 그룹이 이미 만들어졌습니다.
* 리소스 이름이 명명 규칙을 준수합니다. 소문자, 숫자 및 하이픈만 포함해야 합니다. 첫 번째 자리는 반드시 문자여야 합니다. 마지막 문자는 문자 또는 숫자여야 합니다. 값은 2에서 32자까지 포함되어야 합니다.

리소스 관리자 템플릿을 사용하여 Azure Spring Cloud 서비스 인스턴스를 설정하려면 먼저 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)참조하십시오.

Azure Spring Cloud 서비스 인스턴스의 이름은 에서 `azureapps.io`하위 도메인 이름을 요청하는 데 사용되므로 이름이 기존 이름과 충돌하면 설정이 실패합니다. 활동 로그에서 자세한 내용을 찾을 수 있습니다.

### <a name="i-cant-deploy-a-jar-package"></a>JAR 패키지를 배포할 수 없습니다.

Azure 포털 또는 리소스 관리자 템플릿을 사용하여 JAVA 아카이브 파일(JAR)/원본 패키지를 업로드할 수 없습니다.

[Azure CLI를](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)사용하여 응용 프로그램 패키지를 배포할 때 Azure CLI는 배포 진행률을 주기적으로 폴링하고 결국 배포 결과를 표시합니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

응용 프로그램이 올바른 실행 파일 [JAR 형식으로](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)패키징되었는지 확인합니다. 올바르게 패키징되지 않으면 다음과 유사한 오류 메시지가 나타납니다.

> "오류: 유효하지 않거나 손상된 항아리/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>원본 패키지를 배포할 수 없습니다.

Azure 포털 또는 리소스 관리자 템플릿을 사용하여 JAR/원본 패키지를 업로드할 수 없습니다.

[Azure CLI를](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)사용하여 응용 프로그램 패키지를 배포할 때 Azure CLI는 배포 진행률을 주기적으로 폴링하고 결국 배포 결과를 표시합니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 빌드 및 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

그러나 하나의 Azure Spring Cloud 서비스 인스턴스는 한 번에 하나의 소스 패키지에 대해 하나의 빌드 작업만 트리거할 수 있습니다. 자세한 내용은 Azure Spring 클라우드에서 [응용 프로그램 배포](spring-cloud-quickstart-launch-app-portal.md) 및 [스테이징 환경 설정을](spring-cloud-howto-staging-environment.md)참조하세요.

### <a name="my-application-cant-be-registered"></a>신청서를 등록할 수 없습니다.

대부분의 경우 *필요한 종속성* 및 *서비스 검색이* POM(프로젝트 개체 모델) 파일에서 제대로 구성되지 않은 경우 이러한 상황이 발생합니다. 일단 구성되면 기본 제공 서비스 레지스트리 서버 끝점은 응용 프로그램과 함께 환경 변수로 주입됩니다. 그런 다음 응용 프로그램은 서비스 레지스트리 서버에 자신을 등록하고 다른 종속 마이크로 서비스를 검색합니다.

새로 등록된 인스턴스가 트래픽 수신을 시작하기 전에 최소 2분 정도 기다립니다.

기존 Spring 클라우드 기반 솔루션을 Azure로 마이그레이션하는 경우 Azure Spring Cloud에서 제공하는 관리되는 인스턴스와 충돌하지 않도록 임시 _서비스 레지스트리_ 및 _Config Server_ 인스턴스가 제거(또는 비활성화)되어 있는지 확인합니다.

Azure Log Analytics에서 _서비스 레지스트리_ 클라이언트 로그를 확인할 수도 있습니다. 자세한 내용은 [진단 설정으로 로그 및 메트릭 분석을](diagnostic-services.md) 참조하십시오.

Azure 로그 분석에 대해 자세히 알아보려면 [Azure 모니터에서 로그 분석 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조하십시오. [Kusto 쿼리 언어를](https://docs.microsoft.com/azure/kusto/query/)사용하여 로그를 쿼리합니다.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>내 애플리케이션의 환경 변수를 검사하려고 합니다.

환경 변수는 Azure Spring Cloud 프레임워크에 알려 주어 응용 프로그램을 구성하는 서비스를 구성하는 위치와 방법을 Azure가 이해하도록 합니다. 환경 변수가 올바른지 확인하는 것은 잠재적인 문제 해결에 필수적인 첫 번째 단계입니다.  Spring Boot Actuator 엔드포인트를 사용하여 환경 변수를 검토할 수 있습니다.  

> [!WARNING]
> 이 절차에서 테스트 끝점을 사용하여 환경 변수를 노출합니다.  테스트 엔드포인트에 공개적으로 액세스할 수 있거나 애플리케이션에 도메인 이름을 할당한 경우에는 더이상 진행하지 마십시오.

1. [https://editor.swagger.io](`https://<your application test endpoint>/actuator/health`) 로 이동합니다.  
    - `{"status":"UP"}`과 유사한 응답은 엔드포인트가 사용하도록 설정되었음을 나타냅니다.
    - 응답이 음수이면 *POM.xml* 파일에 다음 종속성을 포함합니다.

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 스프링 부팅 액추에이터 끝점을 사용하도록 설정하면 Azure 포털로 이동하여 응용 프로그램의 구성 페이지를 찾습니다.  이름과 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 값이 `*` 있는 환경 변수를 추가합니다. 

1. 애플리케이션을 다시 시작합니다.

1. 로 `https://<your application test endpoint>/actuator/env` 이동하여 응답을 검사합니다.  다음과 같이 표시되어야 합니다.

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

`systemEnvironment`라는 자식 노드를 찾습니다.  이 노드에는 애플리케이션의 환경 변수가 포함됩니다.

> [!IMPORTANT]
> 애플리케이션에 대한 공용 액세스를 가능하게 하려면 환경 변수의 노출을 역방향으로 바꾸어야 합니다.  Azure 포털로 이동하여 응용 프로그램의 구성 페이지를 찾고 이 환경 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`변수를 삭제합니다.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>응용 프로그램에 대한 메트릭 또는 로그를 찾을 수 없습니다.

앱 **관리로** 이동하여 응용 프로그램 상태가 _실행_ 되고 _UP인지_확인합니다.

_JVM의_ 메트릭이 표시되지만 _Tomcat의_메트릭이 없는 경우 응용 `spring-boot-actuator` 프로그램 패키지에서 종속성이 활성화되어 있는지, 성공적으로 부팅되었는지 확인합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

응용 프로그램 로그를 저장소 계정에 보관할 수 있지만 Azure Log Analytics로 전송되지 않은 경우 [작업 영역을 올바르게 설정했는지 확인합니다.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Azure Log Analytics의 프리 티어를 사용하는 경우 [프리 티어는 서비스 수준 계약(SLA)을 제공하지 않습니다.](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)
