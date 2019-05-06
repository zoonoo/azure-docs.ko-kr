---
title: 데이터, 항목 또는 페이지 매김-Azure Logic Apps를 사용 하 여 레코드 추가
description: Azure Logic Apps에서 커넥터 작업에 대 한 기본 페이지 크기 제한을 초과 하 게 페이지 매김을 설정합니다
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476543"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Azure Logic Apps에서 페이지 매김을 사용 하 여 데이터, 항목 또는 레코드 가져오기

커넥터 작업을 사용 하 여 데이터, 항목 또는 레코드 검색 되는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md), 표시 될 수 있습니다 결과 집합이 너무 커서 작업을 동시에 모든 결과 반환 하지 않습니다. 일부 작업을 사용 하 여 결과의 수는 커넥터의 기본 페이지 크기를 초과할 수 있습니다. 이 경우 작업은 결과의 첫 번째 페이지만 반환합니다. 예를 들어, SQL Server 커넥터에 대 한 기본 페이지 크기 **행 가져오기** 작업 2048 이지만 다른 설정에 따라 달라질 수 있습니다.

일부 작업을 사용 하 여는 *페이지 매김* 논리 앱에 페이지 매김 제한까지 더 많은 결과 검색할 수 있지만 작업이 완료 되 면 단일 메시지로 결과만 반환 되도록 설정 합니다. 페이지 매김을 사용 하는 경우 지정 해야는 *임계값* 값 결과 반환할 작업의 대상입니다. 지정 된 임계값에 도달할 때까지 결과 검색 하는 작업입니다. 총 항목 수가 지정된 된 임계값 미만인 경우 작업이 모든 결과 검색 합니다.

커넥터의 페이지 크기를 기준으로 결과의 페이지 매김 설정 검색 페이지에 설정 합니다. 이 동작은 경우에 따라 발생할 수 있습니다 지정 된 임계값 보다 더 많은 결과 의미 합니다. 예를 들어, SQL Server를 사용 하는 경우 **행 가져오기** 페이지 매김 설정을 지 원하는 작업:

* 작업의 기본 페이지 크기는 페이지당 2048 레코드입니다.
* 10,000 개의 레코드로 있고 5,000 개 레코드를 최소값으로 지정 한다고 가정 합니다.
* 페이지 매김을 가져옵니다 하도록 권장 사항을 가져올 하나 이상 지정된 된 최소 레코드의 페이지, 작업 6144 (레코드 3 페이지 x 2048), 없습니다 5,000 개 레코드를 반환 합니다.

특정 작업에 대 한 기본 페이지 크기를 초과할 수 있는 커넥터 중 일부를 사용 하 여 목록을 다음과 같습니다.

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

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱 및 페이지 매김 설정 하려는 작업입니다. 논리 앱에 없는 경우 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="turn-on-pagination"></a>페이지 매김 켜기

액션 논리 앱 디자이너에서 페이지 매김을 지원 하는지 여부를 결정할에 대 한 작업의 설정을 확인 합니다 **페이지 매김** 설정 합니다. 이 예제에서 SQL Server의 페이지 매김을 설정 하는 방법을 보여 줍니다 **행 가져오기** 작업 합니다.

1. 작업의 오른쪽 위 모서리에서 줄임표를 선택 (**...** ) 단추를 선택한 **설정을**합니다.

   ![작업의 설정 열기](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   작업 페이지 매김을 지 원하는 작업을 표시 합니다 **페이지 매김** 설정 합니다.

1. 변경 된 **페이지 매김** 에서 설정 **해제** 에 **에**합니다. 에 **임계값** 속성 결과 반환할 작업의 대상 수에 대 한 정수 값을 지정 합니다.

   ![반환할 결과의 최소 수를 지정 합니다.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 준비가 되면 **완료**를 선택합니다.

## <a name="workflow-definition---pagination"></a>워크플로 정의-페이지 매김

논리 앱 워크플로 정의 포함 하는이 기능을 지 원하는 작업에 대 한 페이지 매김을 켜면 합니다 `"paginationPolicy"` 와 함께 속성을 `"minimumItemCount"` 해당 작업의 속성 `"runtimeConfiguration"` 같은 속성:

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
