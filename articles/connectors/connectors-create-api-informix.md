---
title: IBM 인포믹스 데이터베이스에 연결
description: Azure 논리 앱을 사용하여 IBM Informix에 저장된 리소스를 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757971"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Azure 논리 앱을 사용하여 IBM Informix 데이터베이스 리소스 관리

[Azure 논리 앱](../logic-apps/logic-apps-overview.md) 및 [Informix 커넥터를](/connectors/informix/)사용하면 IBM Informix 데이터베이스에서 리소스를 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 이 커넥터에는 [온-프레미스 데이터 게이트웨이를](../logic-apps/logic-apps-gateway-connection.md)사용할 때 Azure 가상화 및 온-프레미스 데이터베이스에서 실행되는 Windows용 IBM Informix와 같은 클라우드 기반 데이터베이스를 포함하여 TCP/IP 네트워크를 통해 원격 Informix 서버 컴퓨터와 통신하는 Microsoft 클라이언트가 포함됩니다. 분산 관계형 데이터베이스 아키텍처(DRDA) 클라이언트 연결을 지원하도록 구성된 경우 이러한 Informix 플랫폼 및 버전에 연결할 수 있습니다.

* IBM Informix 12.1
* IBM Informix 11.7

이 항목에서는 논리 앱에서 커넥터를 사용하여 데이터베이스 작업을 처리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 온-프레미스 데이터베이스의 경우 로컬 컴퓨터에서 [온-프레미스 데이터 게이트웨이를 다운로드하여 설치한](../logic-apps/logic-apps-gateway-install.md) 다음 [Azure Portal에서 Azure 데이터 게이트웨이 리소스를 만듭니다.](../logic-apps/logic-apps-gateway-connection.md)

* Informix 데이터베이스에 액세스해야 하는 논리 앱입니다. 이 커넥터는 작업만 제공하므로 논리 앱은 이미 되풀이 [트리거와](../connectors/connectors-native-recurrence.md)같은 트리거로 시작해야 합니다. 

## <a name="add-an-informix-action"></a>인포믹스 액션 추가

1. [Azure Portal](https://portal.azure.com)의 Logic App 디자이너에서 논리 앱을 엽니다(아직 열려 있지 않은 경우).

1. Informix 작업을 추가하려는 단계에서 **새 단계를**선택합니다.

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. 검색 상자에서 필터로 `informix`을 입력합니다. 작업 목록에서 다음과 같은 작업을 선택합니다.

   ![실행할 Informix 작업을 선택합니다.](./media/connectors-create-api-informix/select-informix-connector-action.png)

   커넥터는 해당 데이터베이스 작업을 실행하는 다음 작업을 제공합니다.

   * 테이블 받기 - `CALL` 문을 사용하여 데이터베이스 테이블 목록
   * 행 받기 - 문을 사용하여 `SELECT *` 모든 행 읽기
   * 행 받기 - `SELECT WHERE` 문을 사용하여 행 읽기
   * 문을 사용하여 행 `INSERT` 추가
   * `UPDATE` 문을 사용하여 행 편집
   * `DELETE` 문을 사용하여 행 삭제

1. Informix 데이터베이스에 대한 연결 세부 정보를 제공하라는 메시지가 표시되면 [단계를 수행하여 연결을 만든](#create-connection)다음 다음 단계를 계속합니다.

1. 선택한 작업에 대한 정보를 제공합니다.

   | 작업 | 설명 | 속성 및 설명 |
   |--------|-------------|-----------------------------|
   | **테이블 가져오기** | Informix CALL 문을 실행하여 데이터베이스 테이블을 나열합니다. | None |
   | **행 가져오기** | Informix `SELECT *` 문을 실행하여 지정된 테이블의 모든 행을 가져옵니다. | **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <p><p>이 작업에 다른 속성을 추가하려면 **새 매개 변수 추가** 목록에서 속성을 선택합니다. 자세한 내용은 [커넥터의 참조 항목을](/connectors/informix/)참조하십시오. |
   | **행 가져오기** | Informix `SELECT WHERE` 문을 실행하여 지정된 테이블에서 행을 가져옵니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **행 ID**: 행의 고유 ID(예:`9999` |
   | **행 삽입** | Informix 문을 실행하여 지정된 Informix 테이블에 `INSERT` 행을 추가합니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **항목**: 추가할 값이 있는 행 |
   | **행 업데이트** | Informix `UPDATE` 문을 실행 하여 지정 된 Informix 테이블에서 행을 변경 합니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **행 ID**: 예를 들어 업데이트할 행의 고유 ID입니다.`9999` <br>- **행**: 예를 들어, 업데이트 된 값이있는 행,`102` |
   | **행 삭제** | Informix 문을 실행하여 지정된 Informix 테이블에서 `DELETE` 행을 제거합니다. | - **테이블 이름**: 원하는 Informix 테이블의 이름입니다. <br>- **행 ID**: 예를 들어, 삭제할 행의 고유 ID입니다.`9999` |
   ||||

1. 논리 앱을 저장합니다. 이제 [논리 앱을 테스트하거나 논리 앱을](#test-logic-app) 계속 빌드합니다.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>인포믹스에 연결

1. 논리 앱이 온-프레미스 데이터베이스에 연결하는 경우 **온-프레미스 데이터 게이트웨이를 통해 연결을**선택합니다.

1. 이 연결 정보를 제공한 다음 **을 선택합니다.**

   | 속성 | JSON 속성 | 필수 | 예제 값 | 설명 |
   |----------|---------------|----------|---------------|-------------|
   | 연결 이름 | `name` | yes | `informix-demo-connection` | Informix 데이터베이스에 연결하는 데 사용할 이름 |
   | 서버 | `server` | yes | - 클라우드:`informixdemo.cloudapp.net:9089` <br>- 온-프레미스:`informixdemo:9089` | IPv4 또는 IPv6 형식의 TCP/IP 주소 또는 별칭 다음에 콜론 및 TCP/IP 포트 번호 |
   | 데이터베이스 | `database` | yes | `nwind` | DRDA 관계형 데이터베이스 이름(RDBNAM) 또는 인포믹스 데이터베이스 이름(dbname)입니다. Informix는 128바이트 문자열을 허용합니다. |
   | 인증 | `authentication` | 온-프레미스 전용 | **기본** 또는 창(커베로) **Windows** | Informix 데이터베이스에 필요한 인증 유형입니다. 이 속성은 **온-프레미스 데이터 게이트웨이를 통해 연결을**선택할 때만 나타납니다. |
   | 사용자 이름 | `username` | 예 | <*데이터베이스 사용자 이름*> | 데이터베이스의 사용자 이름 |
   | 암호 | `password` | 예 | <*데이터베이스 암호*> | 데이터베이스에 대한 암호 |
   | 게이트웨이 | `gateway` | 온-프레미스 전용 | - <*Azure 구독*> <br>- <*Azure-온-프레미스-데이터 게이트웨이-리소스*> | Azure 포털에서 만든 온-프레미스 데이터 게이트웨이에 대한 Azure 구독 및 Azure 리소스 이름입니다. **게이트웨이** 속성 및 하위 속성은 **온-프레미스 데이터 게이트웨이를 통해 연결을**선택할 때만 나타납니다. |
   ||||||

   예를 들어:

   * **클라우드 데이터베이스**

     ![클라우드 데이터베이스 연결 정보](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **온-프레미스 데이터베이스**

     ![온-프레미스 데이터베이스 연결 정보](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. 논리 앱을 저장합니다.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱 디자이너 도구 모음에서 **실행을**선택합니다. 논리 앱이 실행된 후 해당 실행의 출력을 볼 수 있습니다.

1. 논리 앱의 메뉴에서 **개요를**선택합니다. 개요 창에서 **요약** > **실행 기록**아래에서 가장 최근 실행을 선택합니다.

1. **논리 앱 실행에서** **세부 정보 실행을**선택합니다.

1. 작업 목록에서 Get_tables 등 보려는 출력이 있는 작업을 선택합니다. **Get_tables**

   작업이 성공하면 해당 **Status** 속성이 **성공**으로 표시됩니다.

1. 입력을 보려면 입력 **링크에서**URL 링크를 선택합니다. 출력을 보려면 출력 링크 에서 URL 링크를 **선택합니다.** 다음은 몇 가지 예제 출력입니다.

   * **Get_tables** 테이블 목록이 표시됩니다.

     !["테이블 받기" 작업의 출력](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** 행 의 목록을 보여줍니다 :

     !["행 받기" 작업의 출력](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** 지정된 행을 보여줍니다.

     !["행 받기" 작업의 출력](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** 새 행을 보여 주며, 다음을 보여 주게 됩니다.

     !["행 삽입" 동작의 출력](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * Update_row 업데이트된 행을 보여 주며, 다음을 보여 주시면 **됩니다.**

     !["행 업데이트" 작업의 출력](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** 삭제된 행을 보여 주며, 다음을 보여 주게 됩니다.

     !["행 삭제" 동작의 출력](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

커넥터의 Swagger 설명에 설명된 트리거, 작업 및 제한에 대한 기술적 세부 정보는 [커넥터의 참조 페이지를](/connectors/informix/)검토합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](apis-list.md)에 대해 알아봅니다.
