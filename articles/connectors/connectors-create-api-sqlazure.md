---
title: "논리 앱에 Azure SQL 데이터베이스 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Azure SQL 데이터베이스 커넥터 개요"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: ce3a622db8667df8b3f1d1391c2aa0d7e1e012a5
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Azure SQL 데이터베이스 커넥터 시작
Azure SQL 데이터베이스 커넥터를 사용하여 테이블의 데이터를 관리하는 조직의 워크플로를 만듭니다. 

SQL 데이터베이스를 사용하여 다음과 같은 작업을 수행합니다.

* 고객 데이터베이스에 새 고객을 추가하거나 주문 데이터베이스에서 주문을 업데이트하여 워크플로를 작성합니다.
* 데이터의 행을 가져오고, 새 행을 삽입하고, 삭제하는 작업을 사용합니다. 예를 들어 Dynamics CRM Online에서 레코드가 만들어지면(트리거) Azure SQL Database에 행을 삽입합니다(작업). 

이 항목에서는 논리 앱에서 SQL Database 커넥터를 사용하는 방법을 보여 주고, 트리거 및 작업을 나열합니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 GA(일반 공급)에 적용됩니다. 
> 
> 

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-what-are-logic-apps.md) 및 [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="connect-to-azure-sql-database"></a>Azure SQL 데이터베이스에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 SQL Database에 연결하려면 먼저 SQL Database *연결*을 만듭니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 SQL 데이터베이스에서 SQL 데이터베이스 자격 증명을 입력하여 연결을 만듭니다. 

#### <a name="create-the-connection"></a>연결 만들기
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>트리거 사용
이 연결에는 트리거가 필요하지 않습니다. 다른 트리거(되풀이 트리거, HTTP 웹후크 트리거, 다른 커넥터와 함께 사용할 수 있는 트리거 포함)를 사용하여 논리 앱을 시작합니다. [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)에서 예제를 제공하고 있습니다.

## <a name="use-an-action"></a>작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 중 하나 등 몇 가지가 표시됩니다.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. **작업 추가**를 선택합니다.
3. 텍스트 상자에 "sql"을 입력하여 사용 가능한 모든 작업의 목록을 표시합니다.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. 이 예제에서는 **SQL Server - 행 가져오기**를 선택합니다. 연결이 이미 있는 경우 드롭다운 목록에서 **테이블 이름**을 선택하고 반환할 **행 ID**를 입력합니다.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 항목의 [연결 만들기](connectors-create-api-sqlazure.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다. 
   
   > [!NOTE]
   > 이 예제에서는 테이블의 행을 반환합니다. 이 행의 데이터를 보려면 테이블의 필드를 사용하여 파일을 만드는 다른 작업을 추가합니다. 예를 들어 FirstName 및 LastName 필드를 사용하여 클라우드 저장소 계정에 새 파일을 만드는 OneDrive 작업을 추가합니다. 
   > 
   > 
5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="technical-details"></a>기술 세부 정보
## <a name="sql-database-actions"></a>SQL 데이터베이스 작업
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. SQL 데이터베이스 커넥터에는 다음 작업이 포함됩니다. 

| 작업 | 설명 |
| --- | --- |
| [ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure) |SQL의 저장 프로시저를 실행합니다. |
| [GetRow](connectors-create-api-sqlazure.md#get-row) |SQL 테이블에서 단일 행을 검색합니다. |
| [GetRows](connectors-create-api-sqlazure.md#get-rows) |SQL 테이블에서 행을 검색합니다. |
| [InsertRow](connectors-create-api-sqlazure.md#insert-row) |SQL 테이블에 새 행을 삽입합니다. |
| [DeleteRow](connectors-create-api-sqlazure.md#delete-row) |SQL 테이블에서 행을 삭제합니다. |
| [GetTables](connectors-create-api-sqlazure.md#get-tables) |SQL 데이터베이스에서 테이블을 검색합니다. |
| [UpdateRow](connectors-create-api-sqlazure.md#update-row) |SQL 테이블에서 기존 행을 업데이트합니다. |

### <a name="action-details"></a>작업 세부 정보
이 섹션에서는 모든 필수 또는 선택적 입력 속성 및 커넥터와 연결된 모든 해당 출력을 비롯한 각 작업에 대한 특정 세부 정보를 참조하세요.

#### <a name="execute-stored-procedure"></a>저장 프로시저 실행
SQL의 저장 프로시저를 실행합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| procedure * |프로시저 이름 |실행할 저장 프로시저의 이름 |
| parameters * |입력 매개 변수 |매개 변수는 동적이며 선택한 저장 프로시저를 기준으로 합니다. <br/><br/> 예를 들어 Adventure Works 샘플 데이터베이스를 사용하는 경우 *ufnGetCustomerInformation* 저장 프로시저를 선택합니다. **고객 ID** 입력 매개 변수가 표시됩니다. "6" 또는 다른 고객 ID 중 하나를 입력합니다. |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
ProcedureResult: 저장 프로시저 실행 결과 전달

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| OutputParameters |object |출력 매개 변수 값 |
| ReturnCode |정수 |프로시저의 반환 코드 |
| ResultSets |object |결과 집합 |

#### <a name="get-row"></a>행 가져오기
SQL 테이블에서 단일 행을 검색합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| table * |테이블 이름 |SQL 테이블의 이름 |
| id * |행 ID |검색할 행의 고유 식별자 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
항목

| 속성 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

#### <a name="get-rows"></a>행 가져오기
SQL 테이블에서 행을 검색합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| table* |테이블 이름 |SQL 테이블의 이름 |
| $skip |숫자 건너뛰며 세기 |건너뛸 항목의 수(기본값 = 0) |
| $top |최대 가져오기 수 |검색할 항목의 최대 수(기본값 = 256) |
| $filter |필터 쿼리 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |Order By |항목의 순서를 지정하는 ODATA orderBy 쿼리 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

#### <a name="insert-row"></a>행 삽입
SQL 테이블에 새 행을 삽입합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| table* |테이블 이름 |SQL 테이블의 이름 |
| item* |행 |SQL의 지정된 테이블에 삽입할 행 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
항목

| 속성 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

#### <a name="delete-row"></a>행 삭제
SQL 테이블에서 행을 삭제합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| table* |테이블 이름 |SQL 테이블의 이름 |
| id* |행 ID |삭제할 행의 고유 식별자 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

#### <a name="get-tables"></a>테이블 가져오기
SQL 데이터베이스에서 테이블을 검색합니다.  

이 호출에 대한 매개 변수는 없습니다. 

##### <a name="output-details"></a>출력 세부 정보
TablesList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

#### <a name="update-row"></a>행 업데이트
SQL 테이블에서 기존 행을 업데이트합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| table* |테이블 이름 |SQL 테이블의 이름 |
| id* |행 ID |업데이트할 행의 고유 식별자 |
| item* |행 |업데이트된 값을 가진 행 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
항목

| 속성 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

### <a name="http-responses"></a>HTTP 응답
다른 작업을 호출할 때 특정 응답이 발생할 수 있습니다. 다음 표에서는 응답 및 해당 설명을 대략적으로 설명합니다.  

| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.


