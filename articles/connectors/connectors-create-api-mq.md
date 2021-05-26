---
title: IBM MQ 서버에 연결
description: Azure Logic Apps를 사용하여 워크플로에서 온-프레미스 또는 Azure의 MQ 서버에 연결합니다.
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/25/2021
tags: connectors
ms.openlocfilehash: e852f0ce1584a0858f9523c1ea055d4f2cd616d2
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376258"
---
# <a name="connect-to-an-ibm-mq-server-from-a-workflow-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로에서 IBM MQ 서버에 연결

MQ 커넥터를 사용하면 논리 앱 워크플로를 온-프레미스 또는 Azure에 있는 IBM MQ 서버에 연결할 수 있습니다. 그런 다음, 워크플로에서 MQ 서버에 저장된 메시지를 수신하고 보낼 수 있습니다. 이 문서에서는 MQ 서버에 연결하고 워크플로에 MQ 작업을 추가하는 방법을 보여주어 MQ 커넥터를 시작하는 방법을 안내합니다. 예를 들어 먼저 큐에서 단일 메시지를 검색한 다음, 다른 작업을 시도할 수 있습니다.

이 커넥터에는 TCP/IP 네트워크를 통해 원격 MQ 서버와 통신하는 Microsoft MQ 클라이언트가 포함되어 있습니다. 다음 IBM WebSphere MQ 버전에 연결할 수 있습니다.

* MQ 7.5
* MQ 8.0
* MQ 9.0, 9.1, 9.2

<a name="available-operations"></a>

## <a name="available-operations"></a>사용 가능한 작업

* 다중 테넌트 Azure Logic Apps: **논리 앱(소비)** 리소스를 만들 때 *관리형* MQ 커넥터를 사용하여 MQ 서버에만 연결할 수 있습니다. 이 커넥터는 작업만 제공하고 트리거를 제공하지 않습니다.

* 단일 테넌트 Azure Logic Apps: 단일 테넌트 기반 논리 앱 워크플로를 만들 때 작업 *만* 포함하는 관리형 MQ 커넥터 또는 트리거 *및* 작업을 포함하는 *기본 제공* MQ 작업을 사용하여 MQ 서버에 연결할 수 있습니다.

관리형 커넥터와 기본 제공 작업의 차이점에 대한 자세한 내용은 [Logic Apps 주요 용어](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 검토하세요.

#### <a name="managed"></a>[관리](#tab/managed)

다음 목록에는 MQ에 제공되는 관리형 작업의 일부가 설명되어 있습니다.

* MQ 서버에서 삭제하지 않고 단일 메시지 또는 메시지 배열을 찾습니다. 여러 메시지인 경우 일괄 처리당 반환되는 최대 메시지 수를 지정할 수 있습니다. 지정하지 않으면 모든 메시지가 반환됩니다.
* MQ 서버에서 단일 메시지 또는 메시지 배열을 삭제합니다.
* 단일 메시지 또는 메시지 배열을 수신한 다음, MQ 서버에서 삭제합니다.
* MQ 서버에 단일 메시지를 전송합니다.

모든 관리형 커넥터 작업과 속성, 제한 등의 기타 기술 정보는 [MQ 커넥터의 참조 페이지](/connectors/mq/)를 검토하세요.

#### <a name="built-in"></a>[기본 제공](#tab/built-in)

다음 목록에는 MQ에 제공되는 기본 제공 작업의 일부가 설명되어 있습니다.

* 큐에서 메시지를 사용할 수 있는 경우 몇 가지 작업을 수행합니다.
* 큐에서 하나 이상의 메시지가 수신되면(자동 완성) 일부 작업을 수행합니다.
* 큐에서 하나 이상의 메시지가 수신되면(보기 잠금) 일부 작업을 수행합니다.
* 큐에서 단일 메시지 또는 메시지 배열을 수신합니다. 여러 메시지인 경우 일괄 처리당 반환되는 최대 메시지 수와 최대 일괄 처리 크기(KB)를 지정할 수 있습니다.
* MQ 서버에 단일 메시지 또는 메시지 배열을 보냅니다.

또한 이러한 기본 제공 MQ 작업은 다음과 같은 기능과 [단일 테넌트 Logic Apps 서비스](../logic-apps/single-tenant-overview-compare.md)의 논리 앱에 대한 다른 모든 기능의 이점이 있습니다.

* 전송 중 데이터의 TLS(전송 계층 보안) 암호화
* 보내기 및 받기 작업의 메시지 인코딩
* 논리 앱에서 Azure Functions 프리미엄 플랜을 사용하는 경우 Azure 가상 네트워크 통합 지원

---

## <a name="limitations"></a>제한 사항

MQ 커넥터는 메시지의 **형식** 필드를 사용하지 않으며 문자 세트를 변환하지 않습니다. 커넥터는 메시지 필드에 표시되는 모든 데이터를 JSON 메시지에 넣고 메시지를 함께 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 온-프레미스 MQ 서버를 사용하는 경우 네트워크 내의 서버에서 [온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md)합니다. MQ 커넥터가 작동하려면 온-프레미스 데이터 게이트웨이를 사용하는 서버에도 .NET Framework 4.6이 설치되어야 합니다.

  게이트웨이를 설치한 후에는 Azure에서도 데이터 게이트웨이 리소스를 만들어야 합니다. MQ 커넥터는 이 리소스를 사용하여 MQ 서버에 액세스합니다. 자세한 내용은 [데이터 게이트웨이 연결 설정](../logic-apps/logic-apps-gateway-connection.md)을 검토하세요. 

  > [!NOTE]
  > 다음 시나리오에서는 게이트웨이가 필요 없습니다.
  > 
  > * 관리형 커넥터가 아닌 기본 제공 작업을 사용할 예정입니다.
  > * MQ 서버가 공개적으로 제공되거나 Azure에서 사용할 수 있습니다.

* MQ 서버에 액세스하려는 논리 앱 워크플로. 논리 앱 리소스는 Azure의 게이트웨이 리소스와 동일한 위치에 있어야 합니다.

  MQ 커넥터에는 트리거가 없으므로 워크플로가 트리거로 시작해야 합니다. 그렇지 않으면 먼저 워크플로에 트리거를 추가해야 합니다. 예를 들어, [되풀이 트리거](../connectors/connectors-native-recurrence.md)를 사용할 수 있습니다.

  Azure Logic Apps를 처음 접하는 경우 다중 테넌트 Logic Apps 서비스에서 실행되는 이 [빠른 시작: 예제 논리 앱 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 진행하세요.

<a name="create-connection"></a>

## <a name="create-an-mq-connection"></a>MQ 연결 만들기 

MQ 작업을 처음 추가하는 경우 MQ 서버에 연결하라는 메시지가 표시됩니다.

> [!NOTE]
> 현재, MQ 커넥터는 클라이언트 인증이 아닌 서버 인증만 지원합니다. 자세한 내용은 [연결 및 인증 문제](#connection-problems)를 참조하세요.

#### <a name="managed"></a>[관리](#tab/managed)

1. 온-프레미스 MQ 서버에 연결하는 경우 **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택합니다.

1. MQ 서버에 대한 연결 정보를 입력합니다.

   | 속성 | 온-프레미스 또는 Azure | 설명 |
   |----------|----------------------|-------------|
   | **게이트웨이** | 온-프레미스 전용 | **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택합니다. |
   | **연결 이름** | 둘 다 | 연결에 사용할 이름 |
   | **Server** | 둘 다 | 다음 값 중 하나: <p><p>- MQ 서버 호스트 이름 <br>- 뒤에 콜론과 포트 번호가 붙는 IP 주소 |
   | **큐 관리자 이름** | 둘 다 | 사용하려는 큐 관리자 |
   | **채널 이름** | 둘 다 | 큐 관리자에 연결하기 위한 채널 |
   | **기본 큐 이름** | 둘 다 | 큐의 기본 이름 |
   | **연결 계정** | 둘 다 | MQ 서버에 연결하기 위한 사용자 이름 |
   | **사용자 이름** | 둘 다 | 사용자 이름 자격 증명 |
   | **암호** | 둘 다 | 암호 자격 증명 |
   | **SSL 사용?** | 온-프레미스 전용 | TLS(전송 계층 보안) 또는 SSL(Secure Sockets Layer) 사용 |
   | **게이트웨이 - 구독** | 온-프레미스 전용 | Azure에서 게이트웨이 리소스와 연결된 Azure 구독 |
   | **게이트웨이 - 연결 게이트웨이** | 온-프레미스 전용 | 사용할 게이트웨이 리소스 |
   ||||

   예를 들면 다음과 같습니다.

   ![관리형 MQ 연결 세부 정보를 보여주는 스크린샷](media/connectors-create-api-mq/managed-connection-properties.png)

1. 완료되면 **만들기** 를 선택합니다.

#### <a name="built-in"></a>[기본 제공](#tab/built-in)

1. MQ 서버에 대한 연결 정보를 입력합니다.

   | 속성 | 온-프레미스 또는 Azure | Description |
   |----------|----------------------|-------------|
   | **연결 이름** | 둘 다 | 연결에 사용할 이름 |
   | **서버 이름** | 둘 다 | MQ 서버 이름 또는 IP 주소 |
   | **포트 번호** | 둘 다 | 호스트의 큐 관리자에 연결하기 위한 TCP 포트 번호 |
   | **채널** | 둘 다 | 큐 관리자에 연결하기 위한 채널 |
   | **큐 관리자 이름** | 둘 다 | 사용하려는 큐 관리자 |
   | **기본 큐 이름** | 둘 다 | 큐의 기본 이름 |
   | **연결 계정** | 둘 다 | MQ 서버에 연결하기 위한 사용자 이름 |
   | **사용자 이름** | 둘 다 | 사용자 이름 자격 증명 |
   | **암호** | 둘 다 | 암호 자격 증명 |
   | **TLS 사용** | 둘 다 | TLS(전송 계층 보안) 사용 |
   ||||

   예를 들면 다음과 같습니다.

   ![기본 제공 MQ 연결 세부 정보를 보여주는 스크린샷](media/connectors-create-api-mq/built-in-connection-properties.png)

1. 완료되면 **만들기** 를 선택합니다.

---

<a name="add-action"></a>

## <a name="add-an-mq-action"></a>MQ 작업 추가

Azure Logic Apps에서 작업은 트리거 또는 다른 작업을 따르고 워크플로에서 일부 작업을 수행합니다. 다음은 작업을 추가하는 일반적인 방법(예: **단일 메시지 찾아보기**)을 설명하는 단계입니다.

1. 아직 워크플로를 열지 않았으면 Logic Apps 디자이너에서 워크플로를 엽니다.

1. 트리거 또는 다른 작업 아래에서 새 단계를 추가합니다.

   기존 단계 사이에 단계를 추가하려면 마우스를 화살표 위로 이동합니다. 표시되는 더하기 기호(+)를 선택한 다음, **작업 추가** 를 선택합니다.

1. 작업 검색 상자에서 `mq`를 입력합니다. 작업 목록에서 **메시지 찾아보기** 라는 작업을 선택합니다.

1. MQ 서버에 연결하라는 메시지가 표시되면 [요청된 연결 정보를 제공](#create-connection)합니다.

1. 작업에서, 작업에 필요한 속성 값을 제공합니다.

   추가 속성을 지정하려면 **새 매개 변수 추가** 목록을 열고 추가하려는 매개 변수를 선택합니다.

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 워크플로를 테스트하려면 디자이너 도구 모음에서 **실행** 을 선택합니다.

   실행이 완료되면 디자이너는 단계의 상태와 함께 워크플로의 실행 기록을 보여줍니다.

1. 실행된(건너뛰지 않은) 각 단계의 입력 및 출력을 검토하려면 단계를 확장하거나 선택합니다.

   * 더 많은 입력 정보를 검토하려면 **원시 입력 표시** 를 선택합니다.
   * 더 많은 출력 정보를 검토하려면 **원시 출력 표시** 를 선택합니다. **IncludeInfo** 를 **true** 로 설정하면 더 많은 출력이 포함됩니다.

## <a name="troubleshoot-problems"></a>문제 해결

### <a name="failures-with-browse-or-receive-actions"></a>찾아보기 또는 받기 작업이 실패했습니다.

빈 큐에서 찾아보기 또는 받기 작업을 실행하면 다음 헤더 출력과 함께 작업이 실패합니다.

![MQ "메시지 오류" 없음](media/connectors-create-api-mq/mq-no-message-error.png)

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>연결 및 인증 문제

워크플로에서 온-프레미스 MQ 서버에 연결하려고 하면 다음과 같은 오류가 발생할 수 있습니다.

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Azure에서 직접 MQ 커넥터를 사용하는 경우 MQ 서버는 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/)에서 발급한 인증서를 사용해야 합니다.

* MQ 서버를 사용하려면 TLS 연결에 사용할 암호화 사양을 정의해야 합니다. 그러나 보안을 유지하고 최상의 보안 도구 모음을 포함할 수 있도록 Windows 운영 체제는 지원되는 암호화 사양 세트를 보냅니다.

  MQ 서버가 실행되는 운영 체제에서 사용할 도구 모음을 선택합니다. 구성이 일치하도록, 암호화 사양이 TLS 협상에서 선택한 옵션과 일치 하도록 MQ 서버 설정을 변경해야 합니다.

  연결을 시도하면 MQ 서버는 MQ 서버에서 잘못된 암호화 사양을 선택하여 연결이 실패했다는 내용의 이벤트 메시지를 기록합니다. 이벤트 메시지에는 MQ 서버가 목록에서 선택한 암호화 사양이 포함됩니다. 채널 구성의 암호화 사양을 이벤트 메시지의 암호화 사양과 일치하도록 업데이트합니다.

## <a name="connector-reference"></a>커넥터 참조

모든 관리형 커넥터의 작업과 속성, 제한 등의 기타 기술 정보는 [MQ 커넥터의 참조 페이지](/connectors/mq/)를 검토하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
