---
title: 페이지 매김을 사용하여 더 많은 항목 또는 레코드 가져오기
description: Azure Logic Apps의 커넥터 동작에 대한 기본 페이지 크기 제한을 초과하는 페이지 매김 설정
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090266"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Azure Logic Apps에서 페이지 매김을 사용하여 더 많은 데이터, 항목 또는 레코드 가져오기

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 커넥터 작업을 사용하여 데이터, 항목 또는 레코드를 검색하는 경우 작업에서 결과를 동시에 반환하지 않는 매우 큰 결과 집합을 가져올 수 있습니다. 일부 작업의 경우 결과 수가 커넥터의 기본 페이지 크기를 초과할 수 있습니다. 이 경우 작업은 결과의 첫 번째 페이지만 반환합니다. 예를 들어 SQL Server 커넥터의 **행 가져오기** 작업의 기본 페이지 크기는 2048이지만 다른 설정에 따라 달라질 수 있습니다.

일부 작업을 사용하면 *페이지 매김* 설정을 켜서 논리 앱이 페이지 매김 제한까지 더 많은 결과를 검색할 수 있지만 작업이 완료되면 해당 결과를 단일 메시지로 반환할 수 있습니다. 페이지 매김을 사용하는 경우 작업에서 반환할 결과의 대상 수인 *임계값*을 지정해야 합니다. 작업은 지정된 임계값에 도달할 때까지 결과를 검색합니다. 총 항목 수가 지정된 임계값보다 적으면 작업에서 모든 결과를 검색합니다.

페이지 매김 설정을 켜면 커넥터의 페이지 크기에 따라 결과의 페이지를 검색합니다. 이 동작은 때때로 지정된 임계값보다 더 많은 결과를 얻을 수 있습니다. 예를 들어 페이지 매김 설정을 지원하는 SQL Server **행 가져오기** 작업을 사용하는 경우

* 작업의 기본 페이지 크기는 페이지당 2048개 레코드입니다.
* 10000개 레코드가 있고 5000개 레코드를 최솟값으로 지정했다고 가정합니다.
* 페이지 매김은 레코드의 페이지를 가져오며 최소한 지정된 최솟값을 얻기 위해 이 작업은 5000개 레코드가 아닌 6144개 레코드(3페이지 x 2048개 레코드)를 반환합니다.

다음은 특정 작업에서 기본 페이지 크기를 초과할 수 있는 일부 커넥터가 포함된 목록입니다.

* [Azure Blob Storage](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft 팀](/connectors/teams/)
* [Oracle Database](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 페이지 매김을 켜려는 논리 앱 및 작업. 논리 앱이 없는 경우 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="turn-on-pagination"></a>페이지 매김 켜기

작업이 논리 앱 디자이너에서 페이지 매김을 지원하는지 확인하려면 **페이지 매김** 설정에 대한 작업 설정을 확인합니다. 이 예에서는 SQL Server의 **행 가져오기** 작업에서 페이지 매김을 켜는 방법을 보여 줍니다.

1. 작업의 오른쪽 위 모서리에서 줄임표( **...** ) 단추를 선택하고 **설정**을 선택합니다.

   ![작업의 설정 열기](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   작업에서 페이지 매김을 지원하는 경우 작업에 **페이지 매김** 설정이 표시됩니다.

1. **페이지 매김** 설정을 **꺼짐**에서 **켜짐**으로 변경합니다. **임계값** 속성에서 작업이 반환할 결과의 대상 수에 정수 값을 지정합니다.

   ![반환할 결과의 최소 수를 지정합니다.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 준비가 되면 **완료**를 선택합니다.

## <a name="workflow-definition---pagination"></a>워크플로 정의 - 페이지 매김

이 기능을 지원하는 작업에 페이지 매김을 켜는 경우 논리 앱의 워크플로 정의에는 해당 작업의 `"runtimeConfiguration"` 속성에 `"minimumItemCount"` 속성과 함께 `"paginationPolicy"` 속성이 포함됩니다. 예를 들면 다음과 같습니다.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>지원 받기

질문하려면 [Azure Logic Apps에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-logic-apps.html)를 방문하세요.
