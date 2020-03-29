---
title: 페이지 조정을 통해 더 많은 항목 또는 레코드 받기
description: Azure Logic Apps의 커넥터 작업에 대한 기본 페이지 크기 제한을 초과하도록 페이지 지정 설정
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792117"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Azure 논리 앱에서 페이지 삭제를 사용하여 더 많은 데이터, 항목 또는 레코드 받기

[Azure Logic Apps에서](../logic-apps/logic-apps-overview.md)커넥터 작업을 사용하여 데이터, 항목 또는 레코드를 검색하면 결과 집합이 너무 커서 작업이 동시에 모든 결과를 반환하지 않을 수 있습니다. 일부 작업의 경우 결과 수가 커넥터의 기본 페이지 크기를 초과할 수 있습니다. 이 경우 작업은 결과의 첫 번째 페이지만 반환합니다. 예를 들어 SQL Server 커넥터의 **행 받기** 작업의 기본 페이지 크기는 2048이지만 다른 설정에 따라 다를 수 있습니다.

일부 작업을 사용하면 논리 앱에서 *페이지 매김* 한계까지 더 많은 결과를 검색할 수 있도록 페이지 설정 설정을 켤 수 있지만 작업이 완료되면 해당 결과를 단일 메시지로 반환할 수 있습니다. 페이지 매김을 사용하는 경우 작업을 반환할 대상 결과 수인 *임계값을* 지정해야 합니다. 이 작업은 지정된 임계값에 도달할 때까지 결과를 검색합니다. 총 항목 수가 지정된 임계값보다 적으면 작업이 모든 결과를 검색합니다.

페이지 설정 설정을 켜면 커넥터의 페이지 크기에 따라 결과 페이지가 검색됩니다. 이 동작은 지정된 임계값보다 더 많은 결과를 얻을 수 있음을 의미합니다. 예를 들어 SQL Server **Get 행** 작업을 사용할 때 페이지 설정이 지원됩니다.

* 작업의 기본 페이지 크기는 페이지당 2048레코드입니다.
* 10,000개의 레코드가 있고 5000개의 레코드를 최소레코드로 지정한다고 가정합니다.
* 페이지 삭제는 레코드 페이지를 가져옵니다.

다음은 특정 작업의 기본 페이지 크기를 초과할 수 있는 일부 커넥터만 있는 목록입니다.

* [Azure Blob 저장소](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft 팀](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 로직 앱과 페이지 이동을 설정하려는 작업입니다. 논리 앱이 없는 경우 빠른 [시작: 첫 번째 논리 앱 만들기를](../logic-apps/quickstart-create-first-logic-app-workflow.md)참조하십시오.

## <a name="turn-on-pagination"></a>페이지 설정 켜기

작업이 논리 앱 디자이너에서 페이지 설정을 지원하는지 확인하려면 **페이지 설정에** 대한 작업 설정을 확인합니다. 이 예제에서는 SQL Server의 **행 받기** 작업에서 페이지 해제를 설정하는 방법을 보여 주며 있습니다.

1. 작업의 오른쪽 위 모서리에서 타원 **(...**) 버튼을 선택하고 **설정을**선택합니다.

   ![작업 설정 열기](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   작업이 페이지 조정을 지원하는 경우 **작업에는 페이지 설정이** 표시됩니다.

1. 페이지 **설정(해제** 에서 **켜짐)으로** 변경합니다. **On** **임계값** 속성에서 작업을 반환할 대상 결과 수에 대한 정수 값을 지정합니다.

   ![반환할 최소 결과 수 지정](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 준비가 되면 **완료**를 선택합니다.

## <a name="workflow-definition---pagination"></a>워크플로 정의 - 페이지 박리

이 기능을 지원하는 작업에 대한 페이지 설정을 설정하면 논리 앱의 워크플로 `"paginationPolicy"` 정의에 `"minimumItemCount"` 해당 작업의 `"runtimeConfiguration"` 속성과 함께 속성이 포함됩니다.

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

질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
