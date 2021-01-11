---
title: 로그 문제 해결 Azure Monitor (미리 보기)
description: Azure Monitor를 사용 하 여 신속 하거나 정기적으로 문제를 조사 하거나, 코드 또는 구성 문제를 해결 하거나, 특정 정보에 대해 많은 양의 데이터를 검색 하는 데 사용 되는 지원 사례를 해결 하세요.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067369"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>로그 문제 해결 Azure Monitor (미리 보기)
Azure Monitor를 사용 하 여 신속 하 고/또는 정기적으로 문제를 조사 하거나, 코드 또는 구성 문제를 해결 하거나, 특정 정보에 대해 많은 양의 데이터를 검색 하는 데 사용 되는 지원 사례를 해결 하세요.

>[!NOTE]
> * 문제 해결 로그는 미리 보기 모드입니다.
>* [Log Analytics 팀](mailto:orens@microsoft.com) 에 질문 하거나 기능을 적용 합니다.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>문제 해결 및 코드 쿼리 또는 구성 문제
Azure Monitor 문제 해결 로그를 사용 하 여 레코드를 가져오고 KQL를 사용 하 여 간단 하 고 저렴 한 방법으로 문제 및 문제를 조사 합니다.
문제 해결 로그는 문제 해결을 위한 기본 기능을 제공 하 여 decrees 수 있습니다.

> [!NOTE]
>* 문제 해결 모드의 결정을 구성할 수 있습니다.
>* 현재 "컨테이너 로그" 및 "앱 추적" 테이블에 있는 특정 테이블에 문제 해결 로그를 적용할 수 있습니다.
>* 사용 가능한 기간이 4 일 이면 추가 비용으로 확장 될 수 있습니다.
>* 기본적으로 테이블은 작업 영역 보존을 상속 합니다. 추가 요금을 방지 하려면 이러한 테이블 보존 기간을 변경 하는 것이 좋습니다. [테이블 보존을 변경 하는 방법을 알아보려면 여기를 클릭](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage)하십시오.

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>테이블에 대 한 문제 해결 로그 설정

작업 영역에서 문제 해결 로그를 켜려면 다음 API 호출을 사용 해야 합니다.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>지정 된 테이블에 대 한 문제 해결 로그 기능이 사용 하도록 설정 되었는지 확인 합니다.
지정 된 테이블에 대 한 문제 해결 로그를 사용할 수 있는지 확인 하려면 다음 API 호출을 사용할 수 있습니다.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>작업 영역의 모든 테이블에 대 한 문제 해결 로그 기능이 활성화 되어 있는지 확인 합니다.
문제 해결 로그를 사용 하도록 설정 된 테이블을 확인 하려면 다음 API 호출을 사용할 수 있습니다.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>테이블에 대 한 문제 해결 로그 끄기

작업 영역에서 문제 해결 로그를 해제 하려면 다음 API 호출을 사용 해야 합니다.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* 모든 REST API 도구를 사용 하 여 명령을 실행할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/rest/api/azure/)
>* 인증을 위해 전달자 토큰을 사용 해야 합니다. [자세히 알아보기](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* "IsTroubleshootingAllowed" 플래그 – 테이블이 서비스에서 허용 되는지 여부를 설명 합니다.
>* "IsTroubleshootEnabled"은 해당 기능이 테이블에 대해 사용 하도록 설정 되어 있는지 여부를 나타냅니다 .는 설정 또는 해제할 수 있습니다 (true 또는 false).
>* 특정 테이블에 대해 "isTroubleshootEnabled" 플래그를 사용 하지 않도록 설정 하는 경우 이전 사용 날짜 이후에는 1 주일만 다시 사용 하도록 설정할 수 있습니다.
>* 현재는 아래 테이블에 대해서만 지원 됩니다 (일부 다른 Sku는 나중에도 지원 됨). [가격에 대해 자세히](https://docs.microsoft.com/services-hub/health/azure_pricing)알아보세요.

## <a name="query-limitations-for-troubleshooting"></a>문제 해결을 위한 쿼리 제한 사항
"로그 문제 해결"으로 표시 된 테이블에는 몇 가지 제한 사항이 있습니다.
*   는 처리 리소스를 줄일 수 있으므로 규모가 크고, 복잡 한 분석과 많은 동시 API 호출에는 적합 하지 않습니다.
*   쿼리는 2 일의 시간 범위로 제한 됩니다.
* 제거 작업은 작동 하지 않습니다. [제거에 대해 자세히](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge)알아보세요.
* 이 서비스를 통해 경고를 지원 하지 않습니다.
## <a name="next-steps"></a>다음 단계
* [쿼리 작성](https://docs.microsoft.com/azure/data-explorer/write-queries)


