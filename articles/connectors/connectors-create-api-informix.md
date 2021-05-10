---
title: IBM Informix 데이터베이스에 연결
description: Azure Logic Apps를 사용하여 IBM Informix에 저장된 리소스를 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761647"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 IBM Informix 데이터베이스 리소스 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Informix 커넥터](/connectors/informix/)를 사용하여 IBM Informix 데이터베이스의 리소스를 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 이 커넥터에는 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 사용할 때 Azure 가상화 및 온-프레미스 데이터베이스에서 실행되는 클라우드 기반 데이터베이스(예: Windows용 IBM Informix)를 비롯하여 TCP/IP 네트워크를 통해 원격 Informix 서버 컴퓨터와 통신하는 Microsoft 클라이언트가 포함됩니다. DRDA(분산 관계형 데이터베이스 아키텍처) 클라이언트 연결을 지원하도록 구성된 경우 다음과 같은 Informix 플랫폼 및 버전에 연결할 수 있습니다.

* IBM Informix 12.1
* IBM Informix 11.7

이 항목에서는 논리 앱에서 커넥터를 사용하여 데이터베이스 작업을 처리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 온-프레미스 데이터베이스의 경우 로컬 컴퓨터에서 [온-프레미스 데이터 게이트웨이를 다운로드하여 설치](../logic-apps/logic-apps-gateway-install.md)한 다음 [Azure Portal에서 Azure 데이터 게이트웨이 리소스를 만듭니다](../logic-apps/logic-apps-gateway-connection.md).

* Informix 데이터베이스에 액세스해야 하는 논리 앱. 이 커넥터는 작업만 제공하므로 논리 앱이 트리거(예: [되풀이 트리거](../connectors/connectors-native-recurrence.md))로 이미 시작된 상태여야 합니다. 

## <a name="add-an-informix-action"></a>Informix 작업 추가

1. [Azure Portal](https://portal.azure.com)의 Logic App 디자이너에서 논리 앱을 엽니다(아직 열려 있지 않은 경우).

1. Informix 작업을 추가하려는 단계에서 **새 단계** 를 선택합니다.

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 검색 상자에서 필터로 `informix`을 입력합니다. 작업 목록에서 원하는 작업을 선택합니다. 예를 들면 다음과 같습니다.

   ![실행할 Informix 작업 선택](./media/connectors-create-api-informix/select-informix-connector-action.png)

   커넥터는 해당 데이터베이스 작업을 실행하는 다음 작업을 제공합니다.

   * 테이블 가져오기 - `CALL` 문을 사용하여 데이터베이스 테이블 나열
   * 여러 행 가져오기 - `SELECT *` 문을 사용하여 모든 행 읽기
   * 행 가져오기 - `SELECT WHERE` 문을 사용하여 행 1개 읽기
   * `INSERT` 문을 사용하여 행 추가
   * `UPDATE` 문을 사용하여 행 편집
   * `DELETE` 문을 사용하여 행 삭제

1. Informix 데이터베이스의 연결 정보를 제공하라는 메시지가 표시되면 [연결을 만드는 단계](#create-connection)를 수행하고 다음 단계를 계속 진행합니다.

1. 선택한 작업 정보를 제공합니다.

   | 작업 | 설명 | 속성 및 설명 |
   |--------|-------------|-----------------------------|
   | **테이블 가져오기** | Informix CALL 문을 실행하여 데이터베이스 테이블을 나열합니다. | 없음 |
   | **행 가져오기** | Informix `SELECT *` 문을 실행하여 지정된 테이블의 모든 행을 가져옵니다. | **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <p><p>이 작업에 다른 속성을 추가하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택합니다. 자세한 내용은 [커넥터의 참조 항목](/connectors/informix/)을 참조하세요. |
   | **행 가져오기** | Informix `SELECT WHERE` 문을 실행하여 지정된 테이블의 행 1개를 가져옵니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **행 ID**: 행의 고유 ID입니다(예: `9999`). |
   | **행 삽입** | Informix `INSERT` 문을 실행하여 지정된 Informix 테이블에 행을 추가합니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **항목**: 추가할 값이 있는 행입니다. |
   | **행 업데이트** | Informix `UPDATE` 문을 실행하여 지정된 Informix 테이블의 행을 변경합니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **행 ID**: 업데이트할 행의 고유 ID입니다(예: `9999`). <br>- **행**: 업데이트된 값이 있는 행입니다(예: `102`). |
   | **행 삭제** | Informix `DELETE` 문을 실행하여 지정된 Informix 테이블에서 행을 제거합니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **행 ID**: 삭제할 행의 고유 ID입니다(예: `9999`). |
   ||||

1. 논리 앱을 저장합니다. 이제 [논리 앱을 테스트](#test-logic-app)하거나 논리 앱을 계속 빌드합니다.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Informix에 연결

1. 논리 앱이 온-프레미스 데이터베이스에 연결하는 경우 **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택합니다.

1. 이 연결 정보를 입력하고 **만들기** 를 선택합니다.

   | 속성 | JSON 속성 | 필수 | 예제 값 | Description |
   |----------|---------------|----------|---------------|-------------|
   | 연결 이름 | `name` | 예 | `informix-demo-connection` | Informix 데이터베이스 연결에 사용할 이름입니다. |
   | 서버 | `server` | 예 | - 클라우드: `informixdemo.cloudapp.net:9089` <br>- 온-프레미스: `informixdemo:9089` | IPv4 또는 IPv6 형식인 TCP/IP 주소나 별칭으로, 뒤에 콜론과 TCP/IP 포트 번호가 추가됩니다. |
   | 데이터베이스 | `database` | 예 | `nwind` | DRDA 관계형 데이터베이스 이름(RDBNAM) 또는 Informix 데이터베이스 이름(dbname)입니다. Informix는 128바이트 문자열을 허용합니다. |
   | 인증 | `authentication` | 온-프레미스 전용 | **기본** 또는 **Windows**(Kerberos) | Informix 데이터베이스에 필요한 인증 유형입니다. 이 속성은 **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택한 경우에만 표시됩니다. |
   | 사용자 이름 | `username` | 예 | <*database-user-name*> | 데이터베이스의 사용자 이름입니다. |
   | 암호 | `password` | 예 | <*database-password*> | 데이터베이스의 암호입니다. |
   | 게이트웨이 | `gateway` | 온-프레미스 전용 | - <*Azure-subscription*> <br>- <*Azure-on-premises-data-gateway-resource*> | Azure Portal에서 만든 온-프레미스 데이터 게이트웨이의 Azure 구독 및 Azure 리소스 이름입니다. **게이트웨이** 속성 및 하위 속성은 **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택한 경우에만 표시됩니다. |
   ||||||

   예를 들면 다음과 같습니다.

   * **클라우드 데이터베이스**

     ![클라우드 데이터베이스 연결 정보](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **온-프레미스 데이터베이스**

     ![온-프레미스 데이터베이스 연결 정보](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. 논리 앱을 저장합니다.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱 디자이너 도구 모음에서 **실행** 을 선택합니다. 논리 앱이 실행된 후에는 실행 출력을 볼 수 있습니다.

1. 논리 앱의 메뉴에서 **개요** 를 선택합니다. 개요 창의 **요약** > **실행 기록** 에서 가장 최근 실행을 선택합니다.

1. **논리 앱 실행** 에서 **실행 정보** 를 선택합니다.

1. 작업 목록에서 보려는 출력이 있는 작업을 선택합니다(예: **Get_tables**).

   작업이 성공하면 **상태** 속성이 **성공** 으로 표시됩니다.

1. 입력을 보려면 **입력 링크** 에서 URL 링크를 선택합니다. 출력을 보려면 **출력 링크** 에서 URL 링크를 선택합니다. 몇 가지 예제 출력은 다음과 같습니다.

   * **Get_tables** - 테이블 목록을 표시합니다.

     ![“테이블 가져오기” 작업의 출력](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** - 행 목록을 표시합니다.

     ![“여러 행 가져오기” 작업의 출력](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** - 지정된 행을 표시합니다.

     ![“행 가져오기” 작업의 출력](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** - 새 행을 표시합니다.

     ![“행 삽입” 작업의 출력](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** - 업데이트된 행을 표시합니다.

     ![“행 업데이트” 작업의 출력](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** - 삭제된 행을 표시합니다.

     ![“행 삭제” 작업의 출력](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

커넥터의 Swagger 설명에 있는 트리거, 작업, 한도에 대한 기술 정보는 [커넥터의 참조 페이지](/connectors/informix/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](apis-list.md)에 대해 알아봅니다.
