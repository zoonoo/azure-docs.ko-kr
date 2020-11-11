---
title: Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 데이터 쿼리
description: Azure 데이터 탐색기를 사용 하 여 Azure Monitor에서 Azure 데이터 탐색기, Log Analytics 작업 영역 및 클래식 Application Insights 응용 프로그램 간에 교차곱 쿼리를 수행 합니다.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 9b434c426264fcfee0dfe663a7d1b21a354badec
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491259"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 데이터 쿼리
Azure 데이터 탐색기 프록시 클러스터를 사용 하면 Azure Monitor에서 Azure 데이터 탐색기, Log Analytics 작업 영역 및 클래식 Application Insights 응용 프로그램 간에 제품 간 쿼리를 수행할 수 있습니다. Azure Monitor 또는 클래식 Application Insights 앱의 Log Analytics 작업 영역을 프록시 클러스터로 매핑할 수 있습니다. 그런 다음 Azure 데이터 탐색기 도구를 사용 하 여 프록시 클러스터를 쿼리하고 클러스터 간 쿼리에서이를 참조할 수 있습니다. 이 문서에서는 프록시 클러스터에 연결 하 고, Azure 데이터 탐색기 웹 UI에 프록시 클러스터를 추가 하 고, Azure 데이터 탐색기에서 Log Analytics 작업 영역 또는 클래식 Application Insights 앱에 대해 쿼리를 실행 하는 방법을 보여 줍니다.

다음 다이어그램에서는 Azure 데이터 탐색기 프록시 흐름을 보여 줍니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Azure 데이터 탐색기 프록시 흐름입니다.":::


> [!NOTE]
> Azure 데이터 탐색기 프록시는 공개 미리 보기로 제공 됩니다. [프록시에 연결](#connect-to-the-proxy) 하 여 클러스터에 대 한 프록시 기능을 사용 하도록 설정 합니다. 

## <a name="connect-to-the-proxy"></a>프록시에 연결
Log Analytics 작업 영역 또는 클래식 Application Insights 앱에 연결 하려면[Azure 데이터 탐색기 웹 UI](https://dataexplorer.azure.com/clusters)를 엽니다. Log Analytics 또는 Application Insights 클러스터에 연결 하기 전에 Azure 데이터 탐색기 기본 클러스터 (예: *help* cluster)가 왼쪽 메뉴에 나타나는지 확인 합니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure 데이터 탐색기 native cluster.":::

**클러스터 추가** 를 클릭 한 다음 Log Analytics 또는 Application Insights 클러스터의 URL을 다음 형식 중 하나로 추가 합니다. 
    
* Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

**추가** 를 클릭 하 여 연결을 설정 합니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="클러스터를 추가 합니다.":::
 
> [!NOTE]
> 둘 이상의 프록시 클러스터에 연결을 추가 하는 경우 각각 다른 이름을 지정 합니다. 그렇지 않으면 모두 왼쪽 창에 같은 이름을 갖게 됩니다.

연결이 설정 되 면 기본 Azure 데이터 탐색기 클러스터와 함께 Log Analytics 또는 Application Insights 클러스터가 왼쪽 창에 표시 됩니다. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics 및 Azure 데이터 탐색기 클러스터":::
 
> [!NOTE]
> 매핑될 수 있는 Azure Monitor 작업 영역 수는 100 개로 제한 됩니다.

## <a name="create-queries-using-azure-monitor-data"></a>Azure Monitor 데이터를 사용 하 여 쿼리 만들기

Kusto 탐색기, Azure 데이터 탐색기 웹 UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, 렌즈 및 REST API와 같이 Kusto 쿼리를 지 원하는 클라이언트 도구를 사용 하 여 쿼리를 실행할 수 있습니다.

> [!NOTE]
> Azure 데이터 탐색기 프록시 기능은 데이터 검색에만 사용 됩니다. 자세한 내용은 [함수 지원 가능성](#function-supportability)을 참조 하세요.

> [!TIP]
> * 데이터베이스 이름에는 프록시 클러스터에 지정 된 리소스와 같은 이름을 지정 해야 합니다. 이름은 대/소문자를 구분합니다.
> * 클러스터 간 쿼리에서 Application Insights 앱 및 Log Analytics 작업 영역의 이름이 올바른지 확인 합니다.
>     * 이름에 특수 문자가 포함 되어 있으면 프록시 클러스터 이름에서 URL 인코딩으로 바꿉니다. 
>     * 이름에 [KQL 식별자 이름 규칙](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)을 충족 하지 않는 문자가 포함 된 경우에는 대시 문자로 대체 됩니다 **-** .

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Log Analytics 또는 Application Insights 프록시 클러스터에서 직접 쿼리

Log Analytics 또는 Application Insights 클러스터에 대해 쿼리를 실행 합니다. 왼쪽 창에서 클러스터가 선택 되어 있는지 확인 합니다. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="쿼리 Log Analytics 작업 영역입니다.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Log Analytics 또는 Application Insights 프록시 클러스터와 Azure 데이터 탐색기 네이티브 클러스터의 크로스 쿼리

프록시에서 클러스터 간 쿼리를 실행 하는 경우 왼쪽 창에서 Azure 데이터 탐색기 native cluster가 선택 되어 있는지 확인 합니다. 다음 예에서는 [union](/azure/data-explorer/kusto/query/unionoperator) 연산자와 Log Analytics 작업 영역을 사용 하 여 Azure 데이터 탐색기 클러스터 테이블을 결합 하는 방법을 보여 줍니다.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Union 대신 [ `join` 연산자](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)를 사용 하는 경우에는 프록시가 아닌 Azure 데이터 탐색기 네이티브 클러스터에서 실행 하는 [힌트가](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) 필요할 수 있습니다. 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>다른 테 넌 트의 Azure 데이터 탐색기 클러스터에서 다른 Azure Monitor 리소스를 사용 하 여 데이터 조인

Azure 데이터 탐색기 프록시에서 교차 테 넌 트 쿼리를 지원 하지 않습니다. 두 리소스를 모두 확장 하는 쿼리를 실행 하기 위해 단일 테 넌 트에 로그인 되어 있습니다.

Azure 데이터 탐색기 리소스가 테 넌 트 ' A '에 있고 Log Analytics 작업 영역이 테 넌 트 ' B '에 있는 경우 다음 두 가지 방법 중 하나를 사용 합니다.

- Azure 데이터 탐색기를 사용 하면 다른 테 넌 트의 보안 주체에 대 한 역할을 추가할 수 있습니다. 테 넌 트 ' B '의 사용자 ID를 Azure 데이터 탐색기 클러스터의 권한 있는 사용자로 추가 합니다. Azure 데이터 탐색기 클러스터의 *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* 속성에 테 넌 트 ' B '가 포함 되어 있는지 확인 합니다. ' B ' 테 넌 트에서 완전히 쿼리를 실행 합니다.

- [Lighthouse](/azure/lighthouse/) 를 사용 하 여 Azure Monitor 리소스를 테 넌 트 ' A '에 프로젝션 합니다.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>다른 테 넌 트에서 Azure 데이터 탐색기 클러스터에 연결

Kusto Explorer는 사용자 계정이 원래 속한 테 넌 트에 자동으로 로그인 합니다. 동일한 사용자 계정을 사용 하 여 다른 테 넌 트의 리소스에 액세스 하려면 `tenantId` 연결 문자열에을 명시적으로 지정 해야 합니다. `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>함수 지원 가능성

Azure 데이터 탐색기 프록시 클러스터는 Log Analytics 및 Application Insights에 대 한 기능을 모두 지원 합니다. 이 기능을 통해 클러스터 간 쿼리는 Azure Monitor 테이블 형식 함수를 직접 참조할 수 있습니다.

프록시는 다음 명령을 지원 합니다.

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

다음 이미지는 Azure 데이터 탐색기 웹 UI에서 테이블 형식 함수를 쿼리 하는 예를 보여 줍니다. 함수를 사용 하려면 쿼리 창에서 이름을 실행 합니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Azure 데이터 탐색기 웹 UI에서 테이블 형식 함수를 쿼리 합니다.":::
 
> [!NOTE]
> Azure Monitor는 매개 변수를 지원 하지 않는 테이블 형식 함수만 지원 합니다.

## <a name="additional-syntax-examples"></a>추가 구문 예제

Log Analytics 또는 Application Insights 클러스터를 호출 하는 경우 다음 구문 옵션을 사용할 수 있습니다.

|구문 설명  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 이 구독에 정의 된 리소스를 포함 하는 클러스터 내의 데이터베이스 ( **클러스터 간 쿼리에 권장** ) |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| 이 구독의 모든 앱/작업 영역을 포함 하는 클러스터입니다.    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|구독의 모든 앱/작업 영역을 포함 하 고이 리소스 그룹의 구성원 인 클러스터    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|이 구독에 정의 된 리소스를 포함 하는 클러스터      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>다음 단계

- [Log Analytics 작업 영역 및 Application Insights의 데이터 구조](data-platform-logs.md)에 대해 자세히 알아보세요.
- [Azure 데이터 탐색기에서 쿼리를 작성](https://docs.microsoft.com/azure/data-explorer/write-queries)하는 방법을 알아봅니다.