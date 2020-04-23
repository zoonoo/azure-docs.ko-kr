---
title: Azure 스프링 클라우드에서 로그 및 메트릭 분석 | 마이크로 소프트 문서
description: Azure Spring 클라우드에서 진단 데이터를 분석하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: adbcf28cfbbe2ea3b7cc9c7fd0d1c76246938344
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870400"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>진단 설정으로 로그 및 메트릭 분석

Azure Spring Cloud의 진단 기능을 사용하여 다음 서비스 중 한 가지로 로그 및 메트릭을 분석할 수 있습니다.

* 데이터가 Azure 저장소에 기록되는 Azure 로그 분석을 사용합니다. 로그를 로그 애널리틱스로 내보낼 때 지연이 있습니다.
* 감사 또는 수동 검사를 위해 저장소 계정에 로그를 저장합니다. 보존 시간(일)을 지정할 수 있습니다.
* 타사 서비스 또는 사용자 지정 분석 솔루션으로 수집을 위해 이벤트 허브로 로그를 스트리밍합니다.

모니터링할 로그 범주 및 메트릭 범주를 선택합니다.

> [!TIP]
> 로그를 스트리밍하고 싶으신가요? 이 [Azure CLI 명령을](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)확인하십시오!

## <a name="logs"></a>로그

|로그 | 설명 |
|----|----|
| **응용 프로그램 콘솔** | 모든 고객 응용 프로그램의 콘솔 로그입니다. | 
| **시스템 로그** | 현재 [스프링 클라우드 구성 서버만](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) 이 범주에 기록됩니다. |

## <a name="metrics"></a>메트릭

전체 메트릭 목록은 [스프링 클라우드 메트릭을](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options)참조하십시오.

시작하려면 이러한 서비스 중 하나를 사용하여 데이터를 수신할 수 있습니다. 로그 분석 구성에 대해 자세히 알아보려면 [Azure Monitor에서 로그 분석 시작](../azure-monitor/log-query/get-started-portal.md)을 참조하십시오. 

## <a name="configure-diagnostics-settings"></a>진단 설정 구성

1. Azure 포털에서 Azure 스프링 클라우드 인스턴스로 이동합니다.
1. 진단 **설정** 옵션을 선택한 다음 **진단 추가 설정을 선택합니다.**
1. 설정의 이름을 입력한 다음 로그를 보낼 위치를 선택합니다. 다음 세 가지 옵션의 조합을 선택할 수 있습니다.
    * **저장소 계정에 보관**
    * **이벤트 허브로 스트리밍**
    * **Log Analytics에 보내기**

1. 모니터링할 로그 범주 및 메트릭 범주를 선택한 다음 보존 시간(일)을 지정합니다. 보존 시간은 저장소 계정에만 적용됩니다.
1. **저장**을 선택합니다.

> [!NOTE]
> 로그 또는 메트릭이 내보내질 때와 저장 공간 계정, 이벤트 허브 또는 Log Analytics에 표시되는 시점 사이에는 최대 15분의 간격이 있을 수 있습니다.

## <a name="view-the-logs-and-metrics"></a>로그 및 메트릭 보기
다음 제목 아래에 설명된 대로 로그 및 메트릭을 보는 다양한 방법이 있습니다.

### <a name="use-logs-blade"></a>로그 블레이드 사용

1. Azure 포털에서 Azure 스프링 클라우드 인스턴스로 이동합니다.
1. **로그 검색** 창을 열려면 **로그를 선택합니다.**
1. **로그** 검색 상자에서
   * 로그를 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * 메트릭을 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AzureMetrics
    | limit 50
    ```
1. 검색 결과를 보려면 **실행을**선택합니다.

### <a name="use-log-analytics"></a>Log Analytics 사용

1. Azure 포털에서 왼쪽 창에서 **로그 분석을**선택합니다.
1. 진단 설정을 추가할 때 선택한 로그 분석 작업 영역을 선택합니다.
1. **로그 검색** 창을 열려면 **로그를 선택합니다.**
1. **로그** 검색 상자에서
   * 로그를 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * 메트릭을 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 검색 결과를 보려면 **실행을**선택합니다.
1. 필터 조건을 설정하여 특정 응용 프로그램 또는 인스턴스의 로그를 검색할 수 있습니다.

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`대/소문자를 `=~` 구분하지만 그렇지 않습니다.

로그 애널리틱스에서 사용되는 쿼리 언어에 대한 자세한 내용은 [Azure Monitor 로그 쿼리를](../azure-monitor/log-query/query-language.md)참조하십시오.

### <a name="use-your-storage-account"></a>저장소 계정 사용 

1. Azure 포털에서 왼쪽 창에서 저장소 **계정을**선택합니다.

1. 진단 설정을 추가할 때 선택한 저장소 계정을 선택합니다.
1. **Blob 컨테이너** 창을 열려면 **Blob을 선택합니다.**
1. 응용 프로그램 로그를 검토하려면 **인사이트-로그-응용 프로그램 콘솔이라는 컨테이너를 검색합니다.**
1. 응용 프로그램 메트릭을 검토하려면 **인사이트 메트릭 메트릭-pt1m라는 컨테이너를 검색합니다.**

저장소 계정으로 진단 정보를 보내는 방법에 대해 자세히 알아보려면 [Azure Storage의 진단 데이터 저장 및 보기를](../storage/common/storage-introduction.md)참조하세요.

### <a name="use-your-event-hub"></a>이벤트 허브 사용

1. Azure 포털에서 왼쪽 창에서 이벤트 **허브를 선택합니다.**

1. 진단 설정을 추가할 때 선택한 이벤트 허브를 검색하고 선택합니다.
1. **이벤트 허브 목록** 창을 열려면 이벤트 **허브를 선택합니다.**
1. 응용 프로그램 로그를 검토하려면 **인사이트-로그-응용 프로그램 콘솔이라는 이벤트 허브를 검색합니다.**
1. 응용 프로그램 메트릭을 검토하려면 **인사이트 메트릭-pt1m라는 이벤트 허브를 검색합니다.**

이벤트 허브로 진단 정보를 보내는 방법에 대한 자세한 내용은 [이벤트 허브를 사용하여 핫 경로의 Azure 진단 데이터 스트리밍을 참조합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyze-the-logs"></a>로그 분석

Azure Log Analytics는 Kusto 엔진으로 실행되므로 분석을 위해 로그를 쿼리할 수 있습니다. Kusto를 사용하여 로그 쿼리에 대한 간략한 소개를 보려면 [로그 분석 자습서를](../azure-monitor/log-query/get-started-portal.md)검토하십시오.

응용 프로그램 로그는 응용 프로그램의 상태, 성능 등에 대한 중요한 정보와 자세한 로그를 제공합니다. 다음 섹션에서는 응용 프로그램의 현재 및 과거 상태를 이해하는 데 도움이 되는 몇 가지 간단한 쿼리가 있습니다.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure 스프링 클라우드에서 응용 프로그램 로그 표시

가장 최근에 표시된 로그를 사용하여 시간별로 정렬된 Azure Spring Cloud의 응용 프로그램 로그 목록을 검토하려면 다음 쿼리를 실행합니다.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>오류 또는 예외가 포함된 로그 항목 표시

오류 또는 예외를 언급하는 정렬되지 않은 로그 항목을 검토하려면 다음 쿼리를 실행합니다.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

이 쿼리를 사용하여 오류를 찾거나 쿼리 용어를 수정하여 특정 오류 코드 또는 예외를 찾습니다. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>지난 1시간 동안 응용 프로그램에서 보고한 오류 및 예외 수 표시

지난 1시간 동안 응용 프로그램에서 기록한 오류 및 예외 수를 표시하는 원형 차트를 만들려면 다음 쿼리를 실행합니다.

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>응용 프로그램 로그 쿼리에 대해 자세히 알아보기

Azure Monitor는 로그 분석을 사용하여 응용 프로그램 로그를 쿼리하는 데 대한 광범위한 지원을 제공합니다. 이 서비스에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 시작 을](../azure-monitor/log-query/get-started-queries.md)참조하십시오. 응용 프로그램 로그를 분석하기 위한 쿼리 빌드에 대한 자세한 내용은 [Azure Monitor 의 로그 쿼리 개요를](../azure-monitor/log-query/log-query-overview.md)참조하십시오.
