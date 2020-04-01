---
title: IBM MQ 서버에 연결
description: Azure 또는 온-프레미스 IBM MQ 서버 및 Azure 논리 앱으로 메시지 보내기 및 검색
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410213"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure 논리 앱에서 IBM MQ 서버에 연결

IBM MQ 커넥터는 온프레미스 또는 Azure에 있는 IBM MQ 서버에 저장된 메시지를 보내고 검색합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서에서는 MQ 커넥터를 사용하는 시작 가이드를 제공합니다. 큐에서 단일 메시지를 탐색한 다음 다른 작업을 시도할 수 있습니다.

IBM MQ 커넥터에는 이러한 작업이 포함되어 있지만 트리거는 제공하지 않습니다.

- IBM MQ 서버에서 메시지를 삭제하지 않고 단일 메시지를 찾아봅슬합니다.
- IBM MQ 서버에서 메시지를 삭제하지 않고 메시지 일괄 처리를 검색합니다.
- 단일 메시지를 수신하고 IBM MQ 서버에서 메시지를 삭제합니다.
- 메시지 일괄 처리를 수신하고 IBM MQ 서버에서 메시지를 삭제합니다.
- IBM MQ 서버에 단일 메시지를 보냅니다.

다음은 공식적으로 지원되는 IBM WebSphere MQ 버전입니다.

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>사전 요구 사항

* 온-프레미스 MQ 서버를 사용하는 경우 네트워크 내의 서버에 [온-프레미스 데이터 게이트웨이를 설치합니다.](../logic-apps/logic-apps-gateway-install.md) 온-프레미스 데이터 게이트웨이가 설치된 서버에는 MQ 커넥터가 작동하도록 .NET Framework 4.6도 설치되어 있어야 합니다.

  게이트웨이 설치를 완료한 후에는 온-프레미스 데이터 게이트웨이에 대한 Azure 리소스도 만들어야 합니다. 자세한 내용은 [데이터 게이트웨이 연결 설정을](../logic-apps/logic-apps-gateway-connection.md)참조하십시오.

  MQ 서버가 Azure 내에서 공개적으로 사용 가능하거나 사용할 수 있는 경우 데이터 게이트웨이를 사용할 필요가 없습니다.

* MQ 작업을 추가하려는 논리 앱입니다. 이 논리 앱은 온-프레미스 데이터 게이트웨이 연결과 동일한 위치를 사용해야 하며 워크플로를 시작하는 트리거가 이미 있어야 합니다.

  MQ 커넥터에는 트리거가 없으므로 논리 앱에 먼저 트리거를 추가해야 합니다. 예를 들어 되풀이 트리거를 사용할 수 있습니다. 논리 앱을 처음 접하는 경우 이 [빠른 시작을 시도하여 첫 번째 논리 앱을 만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ 연결 만들기

MQ 작업을 추가할 때 MQ 연결이 아직 없는 경우 다음과 같은 연결을 만들라는 메시지가 표시됩니다.

![연결 정보 제공](media/connectors-create-api-mq/connection-properties.png)

1. 온-프레미스 MQ 서버에 연결하는 경우 **온-프레미스 데이터 게이트웨이를 통해 연결을**선택합니다.

1. MQ 서버에 대한 연결 정보를 제공합니다.

   * **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.

   * 보안 소켓 계층(SSL)을 사용하려면 **SSL 사용(SSL)을 선택합니다.**

     MQ 커넥터는 현재 클라이언트 인증이 아닌 서버 인증만 지원합니다. 자세한 내용은 [연결 및 인증 문제를](#connection-problems)참조하십시오.

1. **게이트웨이** 섹션에서 다음 단계를 따르십시오.

   1. **구독** 목록에서 Azure 게이트웨이 리소스와 연결된 Azure 구독을 선택합니다.

   1. 연결 **게이트웨이** 목록에서 사용할 Azure 게이트웨이 리소스를 선택합니다.

1. 완료되면 **만들기**를 선택합니다.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>연결 및 인증 문제

논리 앱이 온-프레미스 MQ 서버에 연결을 시도하면 다음과 같은 오류가 발생할 수 있습니다.

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Azure에서 MQ 커넥터를 직접 사용하는 경우 MQ 서버는 신뢰할 수 있는 [인증 기관에서](https://www.ssl.com/faqs/what-is-a-certificate-authority/)발급한 인증서를 사용해야 합니다.

* 온-프레미스 데이터 게이트웨이를 사용하는 경우 가능한 경우 신뢰할 수 있는 [인증 기관에서](https://www.ssl.com/faqs/what-is-a-certificate-authority/) 발급한 인증서를 사용해 보십시오. 그러나 이 옵션을 사용할 수 없는 경우 신뢰할 수 있는 [인증 기관에서](https://www.ssl.com/faqs/what-is-a-certificate-authority/) 발급되지 않고 보안수준이 떨어지는 것으로 간주되는 자체 서명된 인증서를 사용할 수 있습니다.

  서버의 자체 서명된 인증서를 설치하려면 Windows 인증 관리자(certmgr.msc) 도구를 사용할 수 있습니다. **Windows Certification Manager** 이 시나리오에서는 온-프레미스 데이터 게이트웨이 서비스가 실행 중인 로컬 컴퓨터에서 신뢰할 수 있는 루트 인증 **기관** 수준에서 로컬 **컴퓨터** 인증서 저장소에 인증서를 설치해야 합니다.

  1. 온-프레미스-데이터 게이트웨이 서비스가 실행 중인 컴퓨터에서 시작 메뉴를 열고 사용자 인증서 관리를 찾아 **선택합니다.**

  1. Windows 인증 관리자 도구가 열리면 **인증서 - 로컬 컴퓨터** >  **신뢰할 수 있는 루트 인증 기관** 폴더로 이동하여 인증서를 설치합니다.

     > [!IMPORTANT]
     > 인증서를 설치해야 **합니다- 로컬 컴퓨터** > **신뢰할 수 있는 루트 인증 기관** 저장소.

* MQ 서버에서는 SSL 연결에 사용할 암호 사양을 정의해야 합니다. 그러나 .NET의 SsLStream에서는 암호 사양에 대한 순서를 지정할 수 없습니다. 이 제한을 해결하려면 커넥터가 SSL 협상에서 보내는 제품군의 첫 번째 암호 사양과 일치하도록 MQ 서버 구성을 변경할 수 있습니다.

  연결을 시도할 때 MQ 서버는 다른 쪽 끝에서 잘못된 암호 사양을 사용했기 때문에 연결이 실패했음을 나타내는 이벤트 메시지를 기록합니다. 이벤트 메시지에는 목록에서 먼저 나타나는 암호 사양이 포함되어 있습니다. 채널 구성의 암호 사양을 업데이트하여 이벤트 메시지의 암호 사양과 일치합니다.

## <a name="browse-single-message"></a>단일 메시지 찾아보기

1. 논리 앱에서 트리거 또는 다른 작업 에서 **새 단계를**선택합니다.

1. 검색 상자에서 을 `mq`입력하고 **메시지 찾아보기** 작업을 선택합니다.

   !["메시지 찾아보기" 작업 선택](media/connectors-create-api-mq/browse-message.png)

1. 아직 MQ 연결을 만들지 않은 경우 해당 연결을 [만들라는](#create-connection)메시지가 표시됩니다.

1. 연결을 만든 후 **찾아보기 메시지** 작업의 속성을 설정합니다.

   | 속성 | Description |
   |----------|-------------|
   | **큐** | 연결에 지정된 큐와 다른 경우 해당 큐를 지정합니다. |
   | **MessageId,** **상관 관계 ID,** **GroupId**및 기타 속성 | 다른 MQ 메시지 속성을 기반으로 하는 메시지 찾아보기 |
   | **포함정보** | 출력에 추가 메시지 정보를 포함하려면 **true**를 선택합니다. 출력에서 추가 메시지 정보를 생략하려면 **false**를 선택합니다. |
   | **타임 아웃** | 값을 입력하여 메시지가 빈 큐에 도착할 때까지 기다리는 시간(대기 시간)을 결정합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다. |
   |||

   다음은 그 예입니다.

   !["메시지 찾아보기" 작업에 대한 속성](media/connectors-create-api-mq/browse-message-properties.png)

1. 작업이 완료되면 디자이너 도구 모음에서 **저장을**선택합니다. 앱을 테스트하려면 **실행을**선택합니다.

   실행이 완료되면 디자이너는 워크플로 단계와 해당 상태를 표시하여 출력을 검토할 수 있습니다.

1. 각 단계에 대한 세부 정보를 보려면 단계의 제목 표시줄을 클릭합니다. 단계의 출력에 대한 자세한 정보를 검토하려면 **원시 출력 표시를 선택합니다.**

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/browse-message-output.png)

   다음은 몇 가지 샘플 원시 출력입니다.

   ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/browse-message-raw-output.png)

1. **IncludeInfo를** **true로**설정하면 추가 출력이 표시됩니다.

   ![메시지 포함 정보 찾아보기](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기

**찾아보기 메시지** 작업에는 큐에서 반환할 메시지 수를 나타내는 **BatchSize** 옵션이 포함되어 있습니다. **BatchSize값이** 없는 경우 모든 메시지가 반환됩니다. 반환된 출력은 메시지의 배열입니다.

1. 이전 단계를 수행하지만 대신 메시지 찾아보기 작업을 **추가합니다.**

1. 아직 MQ 연결을 만들지 않은 경우 해당 연결을 [만들라는](#create-connection)메시지가 표시됩니다. 그렇지 않으면 기본적으로 이전에 구성된 첫 번째 연결이 사용됩니다. 새 연결을 만들려면 **연결 변경을**선택합니다. 또는 다른 연결을 선택합니다.

1. 작업에 대한 정보를 제공합니다.

1. 논리 앱을 저장하고 실행합니다.

   논리 앱 실행이 끝나면 **찾아보기 메시지** 작업의 일부 샘플 출력은 다음과 같습니다.

   ![샘플 "메시지 찾아보기" 출력](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>단일 메시지 받기

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신을**사용하면 큐에서 메시지가 삭제됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 받기를**사용하면 메시지가 큐에서 삭제됩니다.

> [!NOTE]
> 메시지가 없는 큐에서 찾아보기 또는 수신 작업을 실행할 때 이 출력으로 작업이 실패합니다.
>
> ![MQ "메시지 없음" 오류](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>메시지 보내기

1. 이전 단계를 수행하지만 대신 **메시지 보내기** 작업을 추가합니다.

1. 아직 MQ 연결을 만들지 않은 경우 해당 연결을 [만들라는](#create-connection)메시지가 표시됩니다. 그렇지 않으면 기본적으로 이전에 구성된 첫 번째 연결이 사용됩니다. 새 연결을 만들려면 **연결 변경을**선택합니다. 또는 다른 연결을 선택합니다.

1. 작업에 대한 정보를 제공합니다. **MessageType의**경우 유효한 메시지 유형인 **데이터그램,** **회신**또는 **요청** 유형을 선택합니다.

   !["메시지 보내기 작업"에 대한 속성](media/connectors-create-api-mq/send-message-properties.png)

1. 논리 앱을 저장하고 실행합니다.

   논리 앱 실행이 끝나면 **메시지 보내기** 작업의 일부 샘플 출력은 다음과 같습니다.

   ![샘플 "메시지 보내기" 출력](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 설명에 설명된 작업 및 제한에 대한 기술적 세부 정보는 커넥터의 [참조 페이지를](/connectors/mq/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
