---
title: Azure Monitor를 사용 하 여 리소스 간 쿼리 Azure 데이터 탐색기
description: Azure Monitor를 사용 하 여 Azure Monitor에서 Azure 데이터 탐색기, Log Analytics 작업 영역 및 클래식 Application Insights 응용 프로그램 간에 제품 간 쿼리를 수행할 수 있습니다.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5671ec68901be289a87c23b6883160f9cda2b651
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733186"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Azure Monitor를 사용 하 여 리소스 간 쿼리 Azure 데이터 탐색기
Azure Monitor는 Azure 데이터 탐색기, [Application Insights](../app/app-insights-overview.md)및 [Log Analytics](./data-platform-logs.md)간의 서비스 간 쿼리를 지원 합니다. 그런 다음 Log Analytics/Application Insights 도구를 사용 하 여 Azure 데이터 탐색기 클러스터를 쿼리하고 서비스 간 쿼리에서이를 참조할 수 있습니다. 이 문서에서는 서비스 간 쿼리를 만드는 방법을 보여 줍니다.

다음 다이어그램은 Azure Monitor 서비스 간 흐름을 보여 줍니다.

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="사용자, Azure Monitor, 프록시 및 Azure 데이터 탐색기 간의 쿼리 흐름을 보여 주는 다이어그램입니다.":::

>[!NOTE]
> Azure Monitor 서비스 간 쿼리는 공개 미리 보기로 제공 됩니다. 의문 사항이 있으면 [서비스 팀](mailto:ADXProxy@microsoft.com) 에 문의 하세요.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Log Analytics 또는 Application Insights 리소스 및 Azure 데이터 탐색기 간 쿼리

Kusto 쿼리를 지 원하는 클라이언트 도구를 사용 하 여 리소스 간 쿼리를 실행할 수 있습니다. 이러한 도구의 예로 Log Analytics 웹 UI, 통합 문서, PowerShell 및 REST API이 있습니다.

패턴 내의 쿼리에 Azure 데이터 탐색기 클러스터의 식별자와 `adx` 데이터베이스 이름 및 테이블을 차례로 입력 합니다.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="서비스 간 쿼리 예제를 보여 주는 스크린샷":::

> [!NOTE]
>* 데이터베이스 이름은 대/소문자를 구분 합니다.
>* 경고로 서의 리소스 간 쿼리는 지원 되지 않습니다.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Azure 데이터 탐색기 클러스터 테이블과 Log Analytics 작업 영역 결합

명령을 사용 `union` 하 여 클러스터 테이블을 Log Analytics 작업 영역과 결합 합니다.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Union 명령을 사용 하 여 서비스 간 쿼리 예제를 보여 주는 스크린샷":::

> [!Tip]
> 줄임 형식은 허용 됩니다: *ClusterName* / *InitialCatalog*. 예를 들어 `adx('help/Samples')` 는로 변환 됩니다 `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>다른 테넌트에서 Azure Monitor 리소스를 사용하여 한 테넌트의 Azure Data Explorer 클러스터에서 데이터 조인

서비스 간 교차 테 넌 트 쿼리는 지원 되지 않습니다. 두 리소스를 모두 포괄 하는 쿼리를 실행 하기 위해 단일 테 넌 트에 로그인 되었습니다.

Azure 데이터 탐색기 리소스가 테 넌 트 A에 있고 Log Analytics 작업 영역이 테 넌 트 B에 있는 경우 다음 방법 중 하나를 사용 합니다.

*  Azure Data Explorer를 사용하면 다른 테넌트의 보안 주체에 대한 역할을 추가할 수 있습니다. 테 넌 트 B의 사용자 ID를 Azure 데이터 탐색기 클러스터의 권한 있는 사용자로 추가 합니다. Azure 데이터 탐색기 클러스터의 [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) 속성에 테 넌 트 b가 포함 되어 있는지 확인 합니다. 테 넌 트 b에서 완전히 쿼리를 실행 합니다.
*  [Lighthouse](../../lighthouse/index.yml) 를 사용 하 여 Azure Monitor 리소스를 테 넌 트 A에 프로젝션 합니다.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>다른 테넌트에서 Azure Data Explorer 클러스터에 연결

Kusto Explorer는 사용자 계정이 원래 속한 테 넌 트에 자동으로 로그인 합니다. 동일한 사용자 계정으로 다른 테 넌 트의 리소스에 액세스 하려면 연결 문자열에을 명시적으로 지정 해야 합니다 `TenantId` .

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>다음 단계
* [쿼리 작성](/azure/data-explorer/write-queries)
* [Azure 데이터 탐색기를 사용 하 여 Azure Monitor에서 데이터 쿼리](/azure/data-explorer/query-monitor-data)
* [Azure Monitor에서 리소스 간 로그 쿼리 수행](../log-query/cross-workspace-query.md)