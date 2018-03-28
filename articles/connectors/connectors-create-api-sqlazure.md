---
title: Logic Apps에 Azure SQL Database 커넥터 추가 | Microsoft Docs
description: REST API 매개 변수를 사용하는 Azure SQL Database 커넥터 개요
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4313ead0c31ab2e72238701d58dc2f321f116fa6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Azure SQL Database 커넥터 시작
Azure SQL Database 커넥터를 사용하여 테이블의 데이터를 관리하는 조직의 워크플로를 만듭니다. 

SQL Database를 사용하여 다음과 같은 작업을 수행합니다.

* 고객 데이터베이스에 새 고객을 추가하거나 주문 데이터베이스에서 주문을 업데이트하여 워크플로를 작성합니다.
* 데이터의 행을 가져오고, 새 행을 삽입하고, 삭제하는 작업을 사용합니다. 예를 들어 Dynamics CRM Online에서 레코드가 만들어지면(트리거) Azure SQL Database에 행을 삽입합니다(작업). 

이 아티클에서는 논리 앱에서 SQL Database 커넥터를 사용하는 방법을 보여주고 작업을 나열합니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-overview.md) 및 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="connect-to-azure-sql-database"></a>Azure SQL Database에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 SQL Database에 연결하려면 먼저 SQL Database *연결*을 만듭니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 SQL Database에서 SQL Database 자격 증명을 입력하여 연결을 만듭니다. 

#### <a name="create-the-connection"></a>연결 만들기
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>트리거 사용
이 연결에는 트리거가 필요하지 않습니다. 다른 트리거(되풀이 트리거, HTTP 웹후크 트리거, 다른 커넥터와 함께 사용할 수 있는 트리거 포함)를 사용하여 논리 앱을 시작합니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)에서 예제를 제공하고 있습니다.

## <a name="use-an-action"></a>작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 중 하나 등 몇 가지가 표시됩니다.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. **작업 추가**를 선택합니다.
3. 텍스트 상자에 "sql"을 입력하여 사용 가능한 모든 작업의 목록을 표시합니다.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. 이 예제에서는 **SQL Server - 행 가져오기**를 선택합니다. 연결이 이미 있는 경우 드롭다운 목록에서 **테이블 이름**을 선택하고 반환할 **행 ID**를 입력합니다.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 아티클의 [연결 만들기](connectors-create-api-sqlazure.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다. 
   
   > [!NOTE]
   > 이 예제에서는 테이블의 행을 반환합니다. 이 행의 데이터를 보려면 테이블의 필드를 사용하여 파일을 만드는 다른 작업을 추가합니다. 예를 들어 FirstName 및 LastName 필드를 사용하여 클라우드 저장소 계정에 새 파일을 만드는 OneDrive 작업을 추가합니다. 
   > 
   > 
5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/sql/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps에 제공되는 다른 커넥터를 탐색하세요.

