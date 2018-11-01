---
title: IBM DB2에 연결 - Azure Logic Apps | Microsoft Docs
description: IBM DB2 REST API 및 Azure Logic Apps로 리소스 관리
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 9ba2476ccf2601f5d7d2c0e93c7661f740f32145
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231766"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 IBM DB2 리소스 관리

Azure Logic Apps 및 IBM DB2 커넥터를 사용하여 DB2 데이터베이스에 저장된 리소스에 따라 자동화된 작업 및 워크플로를 만들 수 있습니다. 사용자의 워크플로는 데이터베이스의 리소스에 연결하고, 데이터베이스 테이블을 읽고 나열하며, 행을 추가, 변경 및 삭제하는 등의 작업을 수행할 수 있습니다. 논리 앱에 작업을 포함시켜 데이터베이스에서 응답을 가져오고 출력을 다른 작업에 사용할 수 있게 할 수 있습니다. 

이 문서에서는 다양한 데이터베이스 작업을 수행하는 논리 앱을 만드는 방법을 보여 줍니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="supported-platforms-and-versions"></a>지원되는 플랫폼 및 버전

DB2 커넥터는 TCP/IP 네트워크를 통해 원격 DB2 서버와 통신하는 Microsoft 클라이언트를 포함합니다. 이 커넥터를 사용하여 Azure 가상화에서 실행되는 Windows용 IBM Bluemix dashDB 또는 IBM DB2와 같은 클라우드 데이터베이스에 액세스할 수 있습니다. [온-프레미스 데이터 게이트웨이를 설치 및 설정](../logic-apps/logic-apps-gateway-connection.md)한 후 온-프레미스 DB2 데이터베이스에 액세스할 수도 있습니다. 

DRDA(Distributed Relational Database Architecture) SQLAM(SQL Access Manager) 버전 10 및 11을 지원하는 IBM DB2 호환 제품(예: IBM Bluemix dashDB)과 함께 IBM DB2 커넥터에서 지원하는 IBM DB2 플랫폼 및 버전은 다음과 같습니다.

| 플랫폼 | 버전 | 
|----------|---------|
| z/OS용 IBM DB2 | 11.1, 10.1 |
| i용 IBM DB2 | 7.3, 7.2, 7.1 |
| LUW용 IBM DB2 | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>지원되는 데이터베이스 작업

IBM DB2 커넥터는 커넥터에서 해당 작업에 매핑되는 이러한 데이터베이스 작업을 지원합니다.

| 데이터베이스 작업 | 커넥터 작업 | 
|--------------------|------------------|
| 데이터베이스 테이블 목록 표시 | 테이블 가져오기 | 
| SELECT를 사용하여 단일 행 읽기 | 행 가져오기 | 
| SELECT를 사용하여 전체 행 읽기 | 행 가져오기 | 
| INSERT를 사용하여 단일 행 추가 | 행 삽입 | 
| UPDATE를 사용하여 단일 행 편집 | 행 업데이트 | 
| DELETE를 사용하여 단일 행 삭제 | 행 삭제 | 
|||

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* 클라우드 기반 또는 온-프레미스의 IBM DB2 데이터베이스

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* DB2 데이터베이스에 액세스하려는 논리 앱입니다. 이 커넥터에서는 논리 앱을 시작하고 **되풀이** 트리거 같은 별도 트리거를 선택하도록 작업만 제공합니다.
이 문서의 예제에서는 **되풀이** 트리거를 사용합니다.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2 추가 작업 - 테이블 가져오기

1. [Azure Portal](https://portal.azure.com)의 Logic App 디자이너에서 논리 앱을 엽니다(아직 열려 있지 않은 경우).

1. 트리거 아래에서 **새 단계**를 선택합니다.

1. 검색 상자에서 필터로 “db2”를 입력합니다. 이 예제의 경우 작업 목록에서 **테이블 가져오기(미리 보기)** 작업을 선택합니다.
   
   ![작업 선택](./media/connectors-create-api-db2/select-db2-action.png)

   이제 DB2 데이터베이스에 대한 연결 세부 정보를 입력하라는 메시지가 표시됩니다. 

1. [클라우드 데이터베이스](#cloud-connection) 또는 [온-프레미스 데이터베이스](#on-premises-connection)에 대한 연결을 만들기 위한 단계를 따릅니다.

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>클라우드 DB2에 연결

연결을 설정하려면 메시지가 표시될 때 이러한 연결 세부 정보를 입력하고 **만들기**를 선택한 다음, 논리 앱을 저장합니다.

| 자산 | 필수 | 설명 | 
|----------|----------|-------------| 
| **온-프레미스 게이트웨이를 통해 연결** | 아니요 | 온-프레미스 연결에만 적용됩니다. | 
| **연결 이름** | yes | 연결 이름(예: “MyLogicApp-DB2-connection”) |
| **서버** | yes | DB2 서버에 대한 주소 또는 별칭 콜론 포트 번호(예: “myDB2server.cloudapp.net:50000”) <p><p>**참고**: 이 값은 콜론과 TCP/IP 포트 번호가 뒤에 붙는 IPv4 또는 IPv6 형식 중 하나로 구성되어 TCP/IP 주소 또는 별칭을 나타내는 문자열입니다. |
| **데이터베이스** | yes | 데이터베이스의 이름 <p><p>**참고**: 이 값은 DRDA 관계형 데이터베이스 이름(RDBNAM)을 나타내는 문자열입니다. <p>- “z/OS용 IBM DB2” 위치로 인식되는 데이터베이스인 z/OS용 DB2에는 16바이트 문자열이 허용됩니다. <br>- “i용 IBM DB2” 관계형 데이터베이스로 인식되는 데이터베이스인 i용 DB2에는 18바이트 문자열이 허용됩니다. <br>- LUW용 DB2에는 8바이트 문자열이 허용됩니다. |
| **사용자 이름** | yes | 데이터베이스에 대한 사용자 이름 <p><p>**참고**:이 값은 길이가 특정 데이터베이스를 기반으로 하는 문자열입니다. <p><p>- z/OS용 DB2에는 8바이트 문자열이 허용됩니다. <br>- i용 DB2에는 10바이트 문자열이 허용됩니다. <br>- Linux 또는 UNIX용 DB2에는 8바이트 문자열이 허용됩니다. <br>- Windows용 DB2에는 30바이트 문자열이 허용됩니다. | 
| **암호** | yes | 데이터베이스의 암호 | 
|||| 

예: 

![클라우드 기반 데이터베이스에 대한 연결 세부 정보](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>온-프레미스 DB2에 연결

연결을 만들기 전에 온-프레미스 데이터 게이트웨이가 이미 설치되어 있어야 합니다. 그렇지 않은 경우, 연결 설정을 완료할 수 없습니다. 게이트웨이가 설치되어 있는 경우 연결 세부 정보 입력을 진행한 다음, **만들기**를 선택합니다.

| 자산 | 필수 | 설명 | 
|----------|----------|-------------| 
| **온-프레미스 게이트웨이를 통해 연결** | yes | 온-프레미스 연결을 원하는 경우 적용하고 온-프레미스 연결 속성을 보여 줍니다. | 
| **연결 이름** | yes | 연결 이름(예: “MyLogicApp-DB2-connection”) | 
| **서버** | yes | DB2 서버에 대한 주소 또는 별칭 콜론 포트 번호(예: “myDB2server:50000”) <p><p>**참고**: 이 값은 콜론과 TCP/IP 포트 번호가 뒤에 붙는 IPv4 또는 IPv6 형식 중 하나로 구성되어 TCP/IP 주소 또는 별칭을 나타내는 문자열입니다. | 
| **데이터베이스** | yes | 데이터베이스의 이름 <p><p>**참고**: 이 값은 DRDA 관계형 데이터베이스 이름(RDBNAM)을 나타내는 문자열입니다. <p>- “z/OS용 IBM DB2” 위치로 인식되는 데이터베이스인 z/OS용 DB2에는 16바이트 문자열이 허용됩니다. <br>- “i용 IBM DB2” 관계형 데이터베이스로 인식되는 데이터베이스인 i용 DB2에는 18바이트 문자열이 허용됩니다. <br>- LUW용 DB2에는 8바이트 문자열이 허용됩니다. | 
| **인증** | yes | 연결에 대한 인증 유형(예: “기본”) <p><p>**참고**: 기본 또는 Windows(Kerberos)를 포함하는 목록에서 이 값을 선택합니다. | 
| **사용자 이름** | yes | 데이터베이스에 대한 사용자 이름 <p><p>**참고**:이 값은 길이가 특정 데이터베이스를 기반으로 하는 문자열입니다. <p><p>- z/OS용 DB2에는 8바이트 문자열이 허용됩니다. <br>- i용 DB2에는 10바이트 문자열이 허용됩니다. <br>- Linux 또는 UNIX용 DB2에는 8바이트 문자열이 허용됩니다. <br>- Windows용 DB2에는 30바이트 문자열이 허용됩니다. | 
| **암호** | yes | 데이터베이스의 암호 | 
| **게이트웨이** | yes | 설치된 온-프레미스 데이터 게이트웨이의 이름 <p><p>**참고**: Azure 구독 및 리소스 그룹 내에 설치된 모든 데이터 게이트웨이를 포함하는 목록에서 이 값을 선택합니다. | 
|||| 

예: 

![온-프레미스 데이터베이스에 대한 연결 세부 정보](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>출력 테이블 보기

논리 앱을 수동으로 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 논리 앱 실행이 완료되면 실행에서 출력을 볼 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 

1. **요약**의 **실행 기록** 섹션에서 목록에서 첫 번째 항목인 가장 최근 실행을 선택합니다. 

   ![실행 기록 보기](./media/connectors-create-api-db2/run-history.png)

1. **논리 앱 실행**에서 사용자 논리 앱의 각 단계에 대한 상태, 입력 및 출력을 검토할 수 있습니다. **테이블 가져오기** 작업을 확장합니다.

   ![작업 확장](./media/connectors-create-api-db2/expand-action-step.png)

1. 입력을 보려면 **원시 입력 표시**를 선택합니다. 

1. 출력을 보려면 **원시 출력 표시**를 선택합니다. 

   출력은 테이블의 목록을 포함합니다. 
   
   ![출력 테이블 보기](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>행 가져오기

DB2 데이터베이스 테이블에서 한 개의 레코드를 페치하려면 논리 앱에서 **행 가져오기** 작업을 사용합니다. 이 작업은 `SELECT FROM AREA WHERE AREAID = '99999'`와 같은 DB2 `SELECT WHERE` 문을 실행합니다.

1. 논리 앱에서 이전에 DB2 작업을 사용한 적이 없는 경우 [DB2 작업 추가 - 테이블 가져오기](#add-db2-action) 섹션의 단계를 검토하지만, 대신 **행 가져오기** 작업을 추가한 다음, 여기로 돌아와 계속합니다. 

   **행 가져오기** 작업을 추가한 후에는 논리 앱 예제가 다음과 같이 나타납니다.

   ![행 가져오기 작업](./media/connectors-create-api-db2/db2-get-row-action.png)

1. 모든 필수 속성(*)에 대한 값을 지정합니다. 테이블을 선택하면 작업이 해당 테이블의 레코드와 관련된 관련 속성을 보여 줍니다.

   | 자산 | 필수 | 설명 | 
   |----------|----------|-------------| 
   | **테이블 이름** | yes | 원하는 레코드가 있는 테이블(이 예제에서는 “AREA”) | 
   | **영역 ID** | yes | 원하는 레코드의 ID(이 예제에서는 “99999”) | 
   |||| 

   ![테이블 선택](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다. 

### <a name="view-output-row"></a>출력 행 보기

논리 앱을 수동으로 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 논리 앱 실행이 완료되면 실행에서 출력을 볼 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 

1. **요약**의 **실행 기록** 섹션에서 목록에서 첫 번째 항목인 가장 최근 실행을 선택합니다. 

1. **논리 앱 실행**에서 사용자 논리 앱의 각 단계에 대한 상태, 입력 및 출력을 검토할 수 있습니다. **행 가져오기** 작업을 확장합니다.

1. 입력을 보려면 **원시 입력 표시**를 선택합니다. 

1. 출력을 보려면 **원시 출력 표시**를 선택합니다. 

   출력에 지정된 행이 포함됩니다. 
   
   ![출력 행 보기](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>행 가져오기

DB2 데이터베이스 테이블에서 모든 레코드를 페치하려면 논리 앱에서 **행 가져오기** 작업을 사용합니다. 이 작업은 `SELECT * FROM AREA`와 같은 DB2 `SELECT` 문을 실행합니다.

1. 논리 앱에서 이전에 DB2 작업을 사용한 적이 없는 경우 [DB2 작업 추가 - 테이블 가져오기](#add-db2-action) 섹션의 단계를 검토하지만, 대신 **행 가져오기** 작업을 추가한 다음, 여기로 돌아와 계속합니다. 

   **행 가져오기** 작업을 추가한 후에는 논리 앱 예제가 다음과 같이 나타납니다.

   ![행 가져오기 작업](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. **테이블 이름** 목록을 연 다음, 원하는 테이블을 선택합니다(이 예제에서는 “AREA”). 

   ![테이블 선택](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. 결과에 대한 필터 또는 쿼리를 지정하려면 **고급 옵션 표시**를 선택합니다.

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다. 

### <a name="view-output-rows"></a>출력 행 보기

논리 앱을 수동으로 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 논리 앱 실행이 완료되면 실행에서 출력을 볼 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 

1. **요약**의 **실행 기록** 섹션에서 목록에서 첫 번째 항목인 가장 최근 실행을 선택합니다. 

1. **논리 앱 실행**에서 사용자 논리 앱의 각 단계에 대한 상태, 입력 및 출력을 검토할 수 있습니다. **행 가져오기** 작업을 확장합니다.

1. 입력을 보려면 **원시 입력 표시**를 선택합니다. 

1. 출력을 보려면 **원시 출력 표시**를 선택합니다. 

   출력에는 지정된 테이블의 모든 레코드가 포함됩니다.
   
   ![출력 행 보기](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>행 삽입

DB2 데이터베이스 테이블에 단일 레코드를 추가하려면 논리 앱에서 **행 삽입** 작업을 사용합니다. 이 작업은 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`와 같은 DB2 `INSERT` 문을 실행합니다.

1. 논리 앱에서 이전에 DB2 작업을 사용한 적이 없는 경우 [DB2 작업 추가 - 테이블 가져오기](#add-db2-action) 섹션의 단계를 검토하지만, 대신 **행 삽입** 작업을 추가한 다음, 여기로 돌아와 계속합니다. 

   **행 삽입** 작업을 추가한 후에는 논리 앱 예제가 다음과 같이 나타납니다.

   ![행 삽입 작업](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. 모든 필수 속성(*)에 대한 값을 지정합니다. 테이블을 선택하면 작업이 해당 테이블의 레코드와 관련된 관련 속성을 보여 줍니다. 

   이 예제에서 속성은 다음과 같습니다.

   | 자산 | 필수 | 설명 | 
   |----------|----------|-------------| 
   | **테이블 이름** | yes | 레코드를 추가할 테이블(예: “AREA”) | 
   | **영역 ID** | yes | 추가할 영역에 대한 ID(예: “99999”) | 
   | **영역 설명** | yes | 추가할 영역에 대한 설명(예: “Area 99999”) | 
   | **지역 ID** | yes | 추가할 지역에 대한 ID(예: “102”) | 
   |||| 

   예: 

   ![테이블 선택](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다. 

### <a name="view-insert-row-outputs"></a>행 삽입 출력 보기

논리 앱을 수동으로 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 논리 앱 실행이 완료되면 실행에서 출력을 볼 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 

1. **요약**의 **실행 기록** 섹션에서 목록에서 첫 번째 항목인 가장 최근 실행을 선택합니다. 

1. **논리 앱 실행**에서 사용자 논리 앱의 각 단계에 대한 상태, 입력 및 출력을 검토할 수 있습니다. **행 삽입** 작업을 확장합니다.

1. 입력을 보려면 **원시 입력 표시**를 선택합니다. 

1. 출력을 보려면 **원시 출력 표시**를 선택합니다. 

   출력에는 지정된 테이블에서 추가된 레코드가 포함됩니다.
   
   ![삽입된 행과 함께 출력 보기](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>행 업데이트

DB2 데이터베이스 테이블에서 단일 레코드를 업데이트하려면 논리 앱에서 **행 업데이트** 작업을 사용합니다. 이 작업은 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`와 같은 DB2 `UPDATE` 문을 실행합니다.

1. 논리 앱에서 이전에 DB2 작업을 사용한 적이 없는 경우 [DB2 작업 추가 - 테이블 가져오기](#add-db2-action) 섹션의 단계를 검토하지만, 대신 **행 업데이트** 작업을 추가한 다음, 여기로 돌아와 계속합니다. 

   **행 업데이트** 작업을 추가한 후에는 논리 앱 예제가 다음과 같이 나타납니다.

   ![행 업데이트 작업](./media/connectors-create-api-db2/db2-update-row-action.png)

1. 모든 필수 속성(*)에 대한 값을 지정합니다. 테이블을 선택하면 작업이 해당 테이블의 레코드와 관련된 관련 속성을 보여 줍니다. 

   이 예제에서 속성은 다음과 같습니다.

   | 자산 | 필수 | 설명 | 
   |----------|----------|-------------| 
   | **테이블 이름** | yes | 레코드를 업데이트할 테이블(예: “AREA”) | 
   | **행 ID** | yes | 업데이트할 레코드의 ID(예: “99999”) | 
   | **영역 ID** | yes | 새로운 영역 ID(예: “99999”) | 
   | **영역 설명** | yes | 새 영역 설명(“99999 업데이트됨”) | 
   | **지역 ID** | yes | 새로운 지역 ID(예: “102”) | 
   |||| 

   예: 

   ![테이블 선택](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다. 

### <a name="view-update-row-outputs"></a>업데이트 행 출력 보기

논리 앱을 수동으로 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 논리 앱 실행이 완료되면 실행에서 출력을 볼 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 

1. **요약**의 **실행 기록** 섹션에서 목록에서 첫 번째 항목인 가장 최근 실행을 선택합니다. 

1. **논리 앱 실행**에서 사용자 논리 앱의 각 단계에 대한 상태, 입력 및 출력을 검토할 수 있습니다. **행 업데이트** 작업을 확장합니다.

1. 입력을 보려면 **원시 입력 표시**를 선택합니다. 

1. 출력을 보려면 **원시 출력 표시**를 선택합니다. 

   출력에는 지정된 테이블에서 업데이트된 레코드가 포함됩니다.
   
   ![업데이트된 행과 함께 출력 보기](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>행 삭제

DB2 데이터베이스 테이블에서 단일 레코드를 삭제하려면 논리 앱에서 **행 삭제** 작업을 사용합니다. 이 작업은 `DELETE FROM AREA WHERE AREAID = '99999'`와 같은 DB2 `DELETE` 문을 실행합니다.

1. 논리 앱에서 이전에 DB2 작업을 사용한 적이 없는 경우 [DB2 작업 추가 - 테이블 가져오기](#add-db2-action) 섹션의 단계를 검토하지만, 대신 **행 삭제** 작업을 추가한 다음, 여기로 돌아와 계속합니다. 

   **행 삭제** 작업을 추가한 후에는 논리 앱 예제가 다음과 같이 나타납니다.

   ![행 삭제 작업](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. 모든 필수 속성(*)에 대한 값을 지정합니다. 테이블을 선택하면 작업이 해당 테이블의 레코드와 관련된 관련 속성을 보여 줍니다. 

   이 예제에서 속성은 다음과 같습니다.

   | 자산 | 필수 | 설명 | 
   |----------|----------|-------------| 
   | **테이블 이름** | yes | 레코드를 삭제할 테이블(예: “AREA”) | 
   | **행 ID** | yes | 삭제할 레코드의 ID(예: “99999”) | 
   |||| 

   예: 

   ![테이블 선택](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다. 

### <a name="view-delete-row-outputs"></a>행 삭제 출력 보기

논리 앱을 수동으로 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 논리 앱 실행이 완료되면 실행에서 출력을 볼 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 

1. **요약**의 **실행 기록** 섹션에서 목록에서 첫 번째 항목인 가장 최근 실행을 선택합니다. 

1. **논리 앱 실행**에서 사용자 논리 앱의 각 단계에 대한 상태, 입력 및 출력을 검토할 수 있습니다. **행 삭제** 작업을 확장합니다.

1. 입력을 보려면 **원시 입력 표시**를 선택합니다. 

1. 출력을 보려면 **원시 출력 표시**를 선택합니다. 

   출력에는 지정된 테이블에서 삭제된 레코드가 더 이상 포함되지 않습니다.
   
   ![삭제된 행이 없는 출력 보기](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/db2/)를 참조하세요. 

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
