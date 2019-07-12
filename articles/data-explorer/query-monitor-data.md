---
title: Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor의 데이터를 쿼리 합니다.
description: 이 항목에서는 Application Insights 및 Log Analytics를 사용 하 여 교차곱 쿼리에 대 한 Azure 데이터 탐색기 프록시를 만들어 Azure Monitor의 데이터 쿼리
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811194"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor의 데이터를 쿼리 합니다.

Azure 데이터 탐색기 프록시 (ADX 프록시) 클러스터가 Azure Data Explorer 간의 교차곱 쿼리를 수행할 수 있는 엔터티의 [AI (Application Insights)](/azure/azure-monitor/app/app-insights-overview), 및 [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) 에 [Azure Monitor](/azure/azure-monitor/) 서비스입니다. Azure Monitor Log Analytics 작업 영역 또는 Application Insights 앱 프록시 클러스터로 매핑할 수 있습니다. 그런 다음 Azure 데이터 탐색기 도구를 사용 하는 프록시 클러스터 하 고 간 클러스터 쿼리에서 참조할 수 있습니다. 이 아티클에서 프록시 클러스터에 연결 하 고 프록시 클러스터를 Azure 데이터 탐색기 웹 ui에 추가 Azure 데이터 탐색기에서 AI 앱 또는 LA 작업 영역에 대해 쿼리를 실행 하는 방법을 설명 합니다.

Azure 데이터 탐색기 프록시 흐름: 

![ADX 프록시 흐름](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>전제 조건

> [!NOTE]
> ADX 프록시 미리 보기 모드입니다. 이 기능을 사용 하려면에 게 문의 합니다 [ADXProxy](mailto:adxproxy@microsoft.com) 팀입니다.

## <a name="connect-to-the-proxy"></a>프록시에 연결

1. Azure Data Explorer 네이티브 클러스터 확인 (같은 *도움말* 클러스터) Log Analytics 또는 Application Insights 클러스터에 연결 하기 전에 왼쪽된 메뉴에 표시 됩니다.

    ![기본 클러스터 ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Azure 데이터 탐색기 ui에서 (https://dataexplorer.azure.com/clusters) 을 선택 **클러스터 추가**합니다.

1. 에 **클러스터 추가** 창:

    * LA 또는 AI 클러스터에 URL을 추가 합니다. 예: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * **추가**를 선택합니다.

    ![클러스터 추가](media/adx-proxy/add-cluster.png)

    둘 이상의 프록시 클러스터에 대 한 연결을 추가 하는 경우 각각에 다른 이름을 지정 합니다. 그렇지 않으면 모든 해야 이름이 왼쪽된 창에서.

1. 연결이 설정 되 면 LA 또는 AI 클러스터 네이티브 ADX 클러스터를 사용 하 여 왼쪽된 창에 나타납니다. 

    ![Log Analytics 및 Azure 데이터 탐색기 클러스터](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>쿼리 실행

프록시 클러스터 쿼리 탐색기 Kusto ADX 웹 탐색기 Jupyter Kqlmagic 또는 REST API를 사용할 수 있습니다. 

> [!TIP]
> * 데이터베이스 이름에는 프록시 클러스터의 지정 된 리소스와 동일한 이름이 있어야 합니다. 이름은 대/소문자를 구분 합니다.
> * 크로스 쿼리 클러스터에 있는지 확인 합니다 [앱 및 작업 영역 이름은](#application-insights-app-and-log-analytics-workspace-names) 올바른지 합니다.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>기본 Azure 데이터 탐색기 클러스터에 대해 쿼리 

Azure 데이터 탐색기 클러스터에서 쿼리 실행 (같은 *StormEvents* 테이블에 *도움말* 클러스터). 쿼리를 실행 하는 경우 기본 Azure 데이터 탐색기 클러스터 왼쪽된 창에서 선택 되어 있는지 확인 합니다.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![StormEvents 테이블 쿼리](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>LA 또는 AI 클러스터에 대해 쿼리

LA 또는 AL 클러스터에서 쿼리를 실행할 때 LA 또는 AI 클러스터 왼쪽된 창에서 선택 되어 있는지 확인 합니다. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![쿼리 LA 작업 영역](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>쿼리 ADX 프록시에서 LA 또는 AI 클러스터  

프록시에서 LA 또는 AI 클러스터에서 쿼리를 실행 하는 경우 왼쪽된 창에서 선택한 ADX 기본 클러스터를 확인 합니다. 다음 예제에서는 네이티브 ADX 클러스터를 사용 하 여 LA 영역의 쿼리를 보여 줍니다.

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Azure Data Explorer 프록시에서 쿼리](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>ADX 프록시에서 LA 또는 AI 클러스터 및 ADX 클러스터의 쿼리 간 

프록시에서 간 클러스터 쿼리를 실행할 때 왼쪽된 창에서 선택한 ADX 기본 클러스터를 확인 합니다. 다음 예제에 나와 ADX 결합 테이블 클러스터 (사용 하 여 `union`) LA 작업 영역을 사용 하 여 합니다.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Azure Data Explorer 프록시의 쿼리 간](media/adx-proxy/cross-query-adx-proxy.png)

사용 하는 [ `join` 연산자](/azure/kusto/query/joinoperator), union, 대신 Azure 데이터 탐색기 기본 클러스터에서 (그리고 프록시에 없는) 실행 하는 힌트 필요할 수 있습니다. 

## <a name="additional-syntax-examples"></a>추가 구문 예제

다음 구문 옵션은 AI (Application Insights) 또는 Log Analytics (LA) 클러스터를 호출할 때 사용할 수 있습니다.

|구문 설명  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 이 구독에 정의 된 리소스에만 포함 하는 클러스터 내에서 데이터베이스 (**간 클러스터 쿼리에 대 한 권장**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 모든 앱/작업 영역에서이 구독을 포함 하는 클러스터    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|모든 앱/작업 영역에서 구독을 포함 하 고이 리소스 그룹의 구성원 인 클러스터입니다.    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|이 구독에 정의 된 리소스에만 포함 하는 클러스터      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Application Insights 앱 및 Log Analytics 작업 영역 이름

* 이름에 특수 문자가 포함, 프록시 클러스터 이름에서 인코딩 되는 URL로 대체 하는. 
* 이름에 맞지 않는 문자를 포함 하는 경우 [KQL 식별자 이름 규칙](/azure/kusto/query/schema-entities/entity-names), 대시로 대체 됩니다 **-** 문자입니다.

## <a name="next-steps"></a>다음 단계

[쿼리 작성](write-queries.md)