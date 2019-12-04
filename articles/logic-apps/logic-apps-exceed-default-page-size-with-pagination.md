---
title: 페이지 매김을 사용 하 여 더 많은 항목 또는 레코드 가져오기
description: 의 커넥터 동작에 대 한 기본 페이지 크기 제한을 초과 하는 페이지 매김을 설정 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792117"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Azure Logic Apps에서 페이지 매김을 사용 하 여 더 많은 데이터, 항목 또는 레코드 가져오기

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 커넥터 작업을 사용 하 여 데이터, 항목 또는 레코드를 검색 하는 경우 작업에서 모든 결과를 동시에 반환 하지 않도록 결과 집합을 가져올 수 있습니다. 일부 작업의 경우 결과 수가 커넥터의 기본 페이지 크기를 초과할 수 있습니다. 이 경우 작업은 결과의 첫 번째 페이지만 반환합니다. 예를 들어 SQL Server 커넥터의 **Rows Get** 작업에 대 한 기본 페이지 크기는 2048 이지만 다른 설정에 따라 달라질 수 있습니다.

일부 동작을 사용 하면 논리 앱에서 페이지 매김 제한까지 더 많은 결과를 검색할 수 있지만 작업이 완료 되 면 해당 결과를 단일 메시지로 반환 하도록 *페이지 매김* 설정을 켤 수 있습니다. 페이지 매김을 사용 하는 경우 작업에서 반환할 결과의 대상 수 인 *임계값* 을 지정 해야 합니다. 작업은 지정 된 임계값에 도달할 때까지 결과를 검색 합니다. 총 항목 수가 지정 된 임계값 보다 적으면 작업에서 모든 결과를 검색 합니다.

페이지 매김 설정을 켜면 연결선의 페이지 크기에 따라 결과의 페이지를 검색 합니다. 이 동작은 때때로 지정 된 임계값 보다 더 많은 결과를 얻을 수 있음을 의미 합니다. 예를 들어 SQL Server **행 가져오기** 작업을 사용 하는 경우 페이지 매김 설정을 지원 합니다.

* 작업의 기본 페이지 크기는 페이지당 2048 레코드입니다.
* 1만 레코드가 있고 5000 레코드를 최소로 지정 했다고 가정 합니다.
* 페이지 매김은 레코드의 페이지를 가져오며, 최소한 지정 된 최소를 얻기 위해이 작업은 5000 레코드가 아닌 6144 레코드 (3 페이지 x 2048 레코드)를 반환 합니다.

다음은 특정 작업에 대 한 기본 페이지 크기를 초과할 수 있는 일부 커넥터를 포함 하는 목록입니다.

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft 팀](https://docs.microsoft.com/connectors/teams/)
* [Oracle 데이터베이스](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>전제 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 페이지 매김을 설정 하려는 논리 앱 및 작업입니다. 논리 앱이 없는 경우 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

## <a name="turn-on-pagination"></a>페이지 매김 설정

작업에서 논리 앱 디자이너의 페이지 매김을 지원 하는지 확인 하려면 **페이지 매김** 설정에 대 한 작업 설정을 확인 합니다. 이 예에서는 SQL Server의 **행 가져오기** 작업에서 페이지 매김을 설정 하는 방법을 보여 줍니다.

1. 작업의 오른쪽 위 모서리에서 줄임표 ( **...** ) 단추를 선택 하 고 **설정**을 선택 합니다.

   ![작업의 설정 열기](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   동작에서 페이지 매김을 지 원하는 경우 작업에 **페이지 매김** 설정이 표시 됩니다.

1. **페이지 매김** 설정을 **해제** 에서 **켜기**로 변경 합니다. **임계값** 속성에서 작업에서 반환할 결과의 대상 수에 대 한 정수 값을 지정 합니다.

   ![반환할 결과의 최소 수를 지정 합니다.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 준비가 되면 **완료**를 선택합니다.

## <a name="workflow-definition---pagination"></a>워크플로 정의-페이지 매김

이 기능을 지 원하는 작업에 대해 페이지 매김 기능을 설정 하는 경우 논리 앱의 워크플로 정의에는 해당 작업의 `"runtimeConfiguration"` 속성에 `"minimumItemCount"` 속성과 함께 `"paginationPolicy"` 속성이 포함 됩니다. 예를 들면 다음과 같습니다.

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
