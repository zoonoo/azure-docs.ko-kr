---
title: Azure 스프링 클라우드에서 로그 및 메트릭 분석 | Microsoft Docs
description: Azure 스프링 클라우드에서 진단 데이터를 분석 하는 방법을 알아봅니다.
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038887"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>진단 설정을 사용 하 여 로그 및 메트릭 분석

Azure 스프링 클라우드의 진단 기능을 사용 하면 다음 서비스 중 하나를 사용 하 여 로그 및 메트릭을 분석할 수 있습니다.

* Azure Log Analytics을 사용 하 여 분석 하 고, 데이터를 Azure Log Analytics에 즉시 기록 하 여 먼저 저장소에 데이터를 쓸 필요가 없습니다.
* 감사 또는 수동 검사를 위해 저장소 계정에 저장 합니다. 보존 기간 (일)을 지정할 수 있습니다.
* 타사 서비스 또는 사용자 지정 분석 솔루션에서 수집 하기 위해 Event Hubs로 스트리밍합니다.

시작 하려면 데이터를 수신 하기 위해 이러한 서비스 중 하나를 사용 하도록 설정 해야 합니다.  Log Analytics를 구성 하는 방법에 대 한 자세한 내용은 [이 자습서](../azure-monitor/log-query/get-started-portal.md)를 참조 하세요.  

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

1. Azure Portal에서 Azure 스프링 클라우드 인스턴스로 이동 합니다.
1. **진단 설정** 메뉴 옵션을 선택 합니다.
1. **진단 설정 추가** 단추를 선택 합니다.
1. 설정에 사용할 이름을 입력 하 고 로그를 보낼 위치를 선택 합니다. 다음 세 가지 옵션을 임의로 조합 하 여 선택할 수 있습니다.
    * 스토리지 계정에 보관
    * 이벤트 허브로 스트림
    * Log Analytics에 보내기

1. 모니터링할 로그 범주 및 메트릭 범주를 선택 하 고 보존 기간 (일)을 지정 합니다. 보존 시간은 저장소 계정에만 적용 됩니다.
1. **저장**을 선택하여 설정을 적용합니다.

> [!NOTE]
> 로그 또는 메트릭이 내보내지고 저장소 계정/이벤트 허브/Log Analytics에 표시 될 때까지 최대 15 분이 걸릴 수 있습니다.

## <a name="viewing-logs"></a>로그 보기

### <a name="using-log-analytics"></a>Log Analytics 사용

1. Azure Portal의 왼쪽 탐색 메뉴에서 Log Analytics를 선택 합니다.
1. 진단 설정을 추가할 때 선택한 Log Analytics 작업 영역을 선택 합니다.
1. @No__t-0을 선택 하 여 로그 검색 블레이드를 엽니다.
1. 로그 검색 상자에 간단한 쿼리를 입력 합니다.  예를 들어 다음과 같은 가치를 제공해야 합니다.

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. @No__t-0을 선택 하 여 검색 결과를 확인 합니다.
1. 필터 조건을 설정 하 여 특정 응용 프로그램 또는 인스턴스의 로그를 검색할 수 있습니다.

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

[이 문서의](../azure-monitor/log-query/query-language.md) Log Analytics에서 사용 되는 쿼리 언어에 대해 자세히 알아보세요.

### <a name="using-logs-and-metrics-in-storage-account"></a>저장소 계정에서 로그 및 메트릭 사용

1. Azure Portal의 왼쪽 탐색 메뉴에서 저장소 계정을 선택 합니다.
1. 진단 설정을 추가할 때 선택한 저장소 계정을 선택 합니다.
1. @No__t-0 항목을 선택 하 여 Blob 컨테이너 블레이드를 엽니다.
1. @No__t-0 이라는 컨테이너를 찾아 응용 프로그램 로그를 검토 합니다.
1. @No__t-0 이라는 컨테이너를 찾아 응용 프로그램 메트릭을 검토 합니다.

[저장소 계정에 진단 정보를 보내는 방법에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Event Hubs 사용

1. Azure Portal의 왼쪽 탐색 메뉴에서 Event Hubs를 선택 합니다.
1. 진단 설정을 추가할 때 선택한 Event Hubs을 찾아 선택 합니다.
1. @No__t-0을 선택 하 여 이벤트 허브 목록 블레이드를 엽니다.
1. @No__t-0 이라는 이벤트 허브를 찾아 응용 프로그램 로그를 검토 합니다.
1. @No__t-0 이라는 이벤트 허브를 찾아 응용 프로그램 메트릭을 검토 합니다.

[진단 정보를 이벤트 허브로 보내는 방법에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyzing-logs"></a>로그 분석

Azure Log Analytics는 Kusto를 제공 하므로 분석을 위해 로그를 쿼리할 수 있습니다.  Kusto를 사용 하 여 로그를 쿼리 하는 방법에 대 한 간략 한 소개 [Log Analytics 자습서](../azure-monitor/log-query/get-started-portal.md) 를 검토 합니다.

응용 프로그램 로그는 응용 프로그램의 상태, 성능 등에 대 한 중요 한 정보를 제공 합니다.  다음은 응용 프로그램의 현재 및 과거 상태를 이해 하는 데 도움이 되는 몇 가지 간단한 쿼리입니다.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure 스프링 클라우드의 응용 프로그램 로그 표시

Azure 스프링 클라우드에서 응용 프로그램 로그 목록을 검토 하려면 시간별로 정렬 된 최신 로그를 기준으로 정렬 합니다.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>오류 또는 예외를 포함 하는 로그 항목 표시

이 쿼리를 사용 하면 오류 또는 예외를 언급 하는 로그 항목을 검토할 수 있습니다.  결과가 정렬 되지 않습니다.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

이 쿼리를 사용 하 여 오류를 찾거나 쿼리 용어를 수정 하 여 특정 오류 코드나 예외를 찾을 수 있습니다.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>지난 1 시간 동안 응용 프로그램에서 보고 한 오류 및 예외 수를 표시 합니다.

이 쿼리는 지난 1 시간 동안 응용 프로그램에서 기록한 오류 및 예외 수를 표시 하는 원형 차트를 만듭니다.

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>응용 프로그램 로그 쿼리에 대 한 자세한 정보

Azure Monitor Log Analytics를 사용 하 여 응용 프로그램 로그를 쿼리 하기 위한 광범위 한 지원을 제공 합니다.  이 서비스에 대 한 자세한 내용은 Azure Monitor를 사용 하 여 [로그 쿼리](../azure-monitor/log-query/get-started-queries.md) 자습서를 참조 하세요. [Azure Monitor의 로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md) 에서는 응용 프로그램 로그를 분석 하는 쿼리를 작성 하는 방법에 대 한 자세한 정보를 제공 합니다.
