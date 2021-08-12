---
title: Azure App Configuration 모니터링
description: 여기에서 App Configuration 모니터링 방법을 알아보기 시작합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: 7edddaac52c569bf29c1be7e173ce966adb0c17b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969742"
---
# <a name="monitoring-app-configuration"></a>App Configuration 모니터링
Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 

이 문서에서는 App Configuration에서 생성된 모니터링 데이터를 설명합니다. App Configuration에서는 [Azure Monitor](../azure-monitor/overview.md)를 사용합니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우, [Azure Monitor로 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 읽어 보세요.

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure Portal의 모니터링 개요 페이지
Azure Portal의 **개요** 페이지에는 총 요청 수, 제한된 요청 수 및 구성 저장소당 요청 기간 등 자원 배정 현황에 대한 간략한 보기가 포함되어 있습니다. 이 정보는 유용하지만, 사용할 수 있는 모니터링 데이터의 양이 조금만 표시됩니다. 이 모니터링 데이터 중 일부는 자동으로 수집되며 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![개요 페이지의 모니터링](./media/monitoring-overview-page.png)

## <a name="monitoring-data"></a>데이터 모니터링 
App Configuration은 [Azure 리소스의 모니터링 데이터](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. App Configuration에서 만들어진 메트릭 및 로그 메트릭에 대한 자세한 내용은 [App Configuration 모니터링 데이터 참조](./monitor-app-configuration-reference.md)를 확인하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅
플랫폼 메트릭 및 활동 로그는 자동으로 수집 및 저장되지만, 진단 설정을 사용하여 다른 위치로 라우팅될 수 있습니다.

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다. 예를 들어 Azure Monitor에서 거의 실시간으로 구성 저장소의 로그와 메트릭을 보려면 Log Analytics 작업 영역에서 리소스 로그를 수집합니다. 아직 없는 경우 [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md)을 만들고, 다음 단계에 따라 진단 설정을 만들어 사용하도록 설정합니다. 

 #### <a name="portal"></a>[포털](#tab/portal)

1. Azure Portal에 로그인합니다.

1. App Configuration 저장소로 이동합니다.

1. **모니터링** 섹션에서 **진단 설정** 을 선택한 다음, **+진단 설정 추가** 를 선택합니다. 
    > [!div class="mx-imgBorder"]
    > ![진단 설정 추가](./media/diagnostic-settings-add.png)

1. **진단 설정** 페이지에서 설정의 이름을 입력한 다음, **HttpRequest** 를 선택하고 로그를 보낼 대상을 선택합니다. Log Analytics 작업 영역으로 보내려면 **Log Analytics 작업 영역으로 보내기** 를 선택합니다.
 
    > [!div class="mx-imgBorder"]
    > ![진단 설정의 세부 정보](./media/monitoring-diagnostic-settings-details.png)

1. **구독** 및 **Log Analytics 작업 영역** 의 이름을 입력합니다. 
1. **저장** 을 선택하고, 이제 진단 설정 페이지에 새 진단 설정이 나열되는지 확인합니다. 
    

 ### <a name="azure-cli"></a>[Azure CLI](#tab/cli)
    
1. Azure Cloud Shell을 열거나 Azure CLI를 로컬로 설치한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

1. ID가 둘 이상의 구독과 연결되어 있으면, 로그를 사용할 스토리지 계정의 구독으로 활성 구독을 설정합니다.

    ```Azure CLI
    az account set --subscription <your-subscription-id>
    ```

1. az monitor [diagnostic-settings create 명령](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create&preserve-view=true)을 사용하여 로그를 사용하도록 설정합니다.

    ```Azure CLI
    az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <app-configuration-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
    ```

 ### <a name="powershell"></a>[PowerShell](#tab/PowerShell)
    
1. Windows PowerShell 명령 창을 열고, Connect-AzAccount 명령을 사용하여 Azure 구독에 로그인합니다. 그런 다음, 화면에 나타나는 지침에 따릅니다.

    ```PowerShell
    Connect-AzAccount
    ```

1. 로깅을 사용하도록 설정할 App Configuration의 구독으로 활성 구독을 설정합니다.

    ```PowerShell
    Set-AzContext -SubscriptionId <subscription-id>
    ```
    
1. Log Analytics 작업 영역의 로그를 사용하도록 설정하려면 [Set-AzDiagnosticSetting PowerShell](/previous-versions/azure/mt631625(v=azure.100)?redirectedfrom=MSDN) cmdlet을 사용합니다. 

    ```PowerShell
    Set-AzDiagnosticSetting -ResourceId <app-configuration-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true
    ```
1. 진단 설정이 올바르게 설정되고 로그 범주가 사용하도록 설정되어 있는지 확인합니다. 

    ```PowerShell
    Get-AzureRmDiagnosticSetting -ResourceId <app-configuration-resource-id> 
    ```
---
Azure Portal, CLI 또는 PowerShell을 사용하여 진단 설정 만들기의 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 

진단 설정을 만들 때 수집할 로그 범주를 지정합니다. App Configuration 로그 범주에 대한 자세한 내용은 [App Configuration 모니터링 데이터 참조](./monitor-app-configuration-reference.md#resourcelogs)를 참조하세요.

## <a name="analyzing-metrics"></a>메트릭 분석

**Azure Monitor** 메뉴에서 **메트릭** 을 열면 메트릭 탐색기를 사용해 다른 Azure 서비스의 메트릭으로 App Configuration 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. App Configuration에서는 다음 메트릭이 수집됩니다. 

* Http 들어오는 요청 수 
* Http 들어오는 요청 기간 
* 제한된 Http 요청 수(Http 상태 코드 429 응답)

포털에서 **메트릭** 섹션으로 이동하여 분석할 **메트릭 네임스페이스** 와 **메트릭** 을 선택합니다. 이 스크린샷에는 구성 저장소에서 **Http 들어오는 요청 수** 를 선택하면 표시되는 메트릭 보기가 나와 있습니다.

> [!div class="mx-imgBorder"]
> ![App Config 메트릭 사용 방법](./media/monitoring-analyze-metrics.png)

App Configuration에 수집된 플랫폼 메트릭 목록은[App Configuration 모니터링 데이터 참조 메트릭](./monitor-app-configuration-reference.md#metrics)을 확인하세요. 참조에서는 [Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md)의 목록도 확인할 수 있습니다.

## <a name="analyzing-logs"></a>로그 분석
Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에서 설명합니다. 

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  
App Configuration에 수집된 리소스 로그 유형 목록은 [App Configuration 모니터링 데이터 참조](./monitor-app-configuration-reference.md#resourcelogs)를 확인하세요. Azure Monitor 로그에서 사용하고 Log Analytics에서 쿼리할 수 있는 테이블 목록은 [App Configuration 모니터링 데이터 참조](./monitor-app-configuration-reference.md#azuremonitorlogstables)를 확인하세요.  

>[!IMPORTANT]
> App Configuration 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 앱 구성 리소스로 설정된 상태로 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 


다른 구성의 데이터 또는 다른 Azure 리소스의 데이터가 포함된 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/)를 참조하세요.

포털에서 **로그** 섹션으로 이동한 다음 쿼리 편집기로 이동합니다. 왼쪽의 **테이블** 탭에서 **AACHttpRequest** 를 선택하여 구성 저장소의 로그를 확인합니다. 편집기에 Kusto 쿼리를 입력하면 결과가 아래에 표시됩니다.  

> [!div class="mx-imgBorder"]
> ![로그에 kusto 쿼리 작성](./media/monitoring-writing-queries.png)

다음은 App Configuration 리소스를 모니터링하는 데 사용할 수 있는 샘플 쿼리입니다. 



* 지난 3일 동안의 모든 Http 요청 나열 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
    ```

* 지난 3일 동안의 제한된 모든 요청 나열(요청이 너무 많은 경우 Http 상태 코드 429 반환) 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | where StatusCode == "429"
    ```

* IP 주소별로 지난 3일 동안 보낸 요청 수 나열 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount= count() by ClientIPAddress
        | order by requestCount desc 
    ```

* 지난 3일 동안 받은 상태 코드 형식의 원형 차트 만들기
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount=count() by StatusCode
        | order by requestCount desc 
        | render piechart 
    ```

* 지난 14일 동안 일별로 전송된 요청 수 나열
    ```Kusto
    AACHttpRequest
        | where TimeGenerated > ago(14d)
        | extend Day = startofday(TimeGenerated)
        | summarize requestcount=count() by Day
        | order by Day desc  
    ```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다.
다음 표에는 App Configuration에 대한 일반적인 경고 규칙과 권장되는 경고 규칙이 나와 있습니다.

| 경고 유형 | 조건 | Description  |
|:---|:---|:---|
|Http 요청에 대한 속도 제한 | 상태 코드 = 429  | 구성 저장소가 [시간별 요청 할당량](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration)을 초과했습니다. 표준 저장소로 업그레이드하거나 [모범 사례](./howto-best-practices.md#reduce-requests-made-to-app-configuration)에 따라 사용량을 최적화합니다. |



## <a name="next-steps"></a>다음 단계

* App Configuration에서 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조는 [App Configuration 모니터링 데이터 참조](./monitor-app-configuration-reference.md)를 확인하세요.

* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.