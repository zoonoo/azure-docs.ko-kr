---
title: IBM MQ 서버에 연결
description: Azure 또는 온-프레미스 IBM MQ 서버 및 Azure Logic Apps로 메시지 보내기 및 검색
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/14/2020
tags: connectors
ms.openlocfilehash: e9e554fdc092e49f5a87049de0e3dc3163105f58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609506"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure Logic Apps에서 IBM MQ 서버에 연결

IBM MQ 커넥터는 IBM MQ 서버 온-프레미스 또는 Azure에 저장된 메시지를 전송하고 검색합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서는 MQ 커넥터를 사용하는 시작 가이드를 제공합니다. 먼저 큐에서 단일 메시지를 검색한 다음, 다른 작업을 시도할 수 있습니다.

IBM MQ 커넥터에는 이러한 작업이 포함되지만 트리거는 제공되지 않습니다.

- IBM MQ 서버에서 메시지를 삭제하지 않고 단일 메시지를 찾아봅니다.
- IBM MQ 서버에서 메시지를 삭제하지 않고 일괄 처리 메시지를 찾아봅니다.
- 단일 메시지 수신 및 IBM MQ 서버에서 메시지를 삭제합니다.
- 일괄 처리 메시지 수신 및 IBM MQ 서버에서 메시지를 삭제합니다.
- IBM MQ 서버에 단일 메시지를 전송합니다.

공식적으로 지원되는 IBM WebSphere MQ 버전은 다음과 같습니다.

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>사전 요구 사항

* 온-프레미스 MQ 서버를 사용하는 경우 네트워크 내의 서버에서 [온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md)합니다. 온-프레미스 데이터 게이트웨이를 설치한 서버에는 MQ 커넥터가 작동하기 위한 .NET Framework 4.6도 설치되어야 합니다.

  게이트웨이 설치가 완료되면 Azure에서 온-프레미스 데이터 게이트웨이에 대한 리소스도 만들어야 합니다. 자세한 내용은 [데이터 게이트웨이 연결 설정](../logic-apps/logic-apps-gateway-connection.md)을 참조하세요.

  MQ 서버를 공개적으로 사용할 수 있거나 Azure 내에서 사용할 수 있는 경우 데이터 게이트웨이를 사용할 필요가 없습니다.

* MQ 작업을 추가하려는 논리 앱 이 논리 앱은 온-프레미스 데이터 게이트웨이 연결과 동일한 위치를 사용해야 하며 워크플로를 시작하는 트리거가 이미 있어야 합니다.

  MQ 커넥터에는 트리거가 없으므로 먼저 논리 앱에 트리거를 추가해야 합니다. 예를 들어, 되풀이 트리거를 사용할 수 있습니다. 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만들기 위한 빠른 시작](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 사용해 보세요.

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ 연결 만들기

MQ 작업을 추가할 때 MQ 연결이 아직 없는 경우 연결을 만들라는 메시지가 표시됩니다. 예를 들면 다음과 같습니다.

![연결 정보 제공](media/connectors-create-api-mq/connection-properties.png)

1. 온-프레미스 MQ 서버에 연결하는 경우 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다.

1. MQ 서버에 대한 연결 정보를 입력합니다.

   * **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.

   * SSL(Secure Sockets Layer)을 사용하려면 **SSL 사용**을 선택합니다.

     현재, MQ 커넥터는 클라이언트 인증이 아닌 서버 인증만 지원합니다. 자세한 내용은 [연결 및 인증 문제](#connection-problems)를 참조하세요.

1. **게이트웨이** 섹션에서 다음 단계를 수행합니다.

   1. **구독** 목록에서 Azure 게이트웨이 리소스와 연결된 Azure 구독을 선택합니다.

   1. **연결 게이트웨이** 목록에서 사용하려는 Azure 게이트웨이 리소스를 선택합니다.

1. 완료되면 **만들기**를 선택합니다.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>연결 및 인증 문제

논리 앱이 온-프레미스 MQ 서버에 연결하려고 하면 다음과 같은 오류가 발생할 수 있습니다.

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Azure에서 직접 MQ 커넥터를 사용하는 경우 MQ 서버는 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/)에서 발급한 인증서를 사용해야 합니다.

* 온-프레미스 데이터 게이트웨이를 사용하는 경우 가능하면 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/)에서 발급한 인증서를 사용합니다. 그러나 이 옵션을 사용할 수 없는 경우에는 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/)에서 발급되지 않았으며 보안 수준이 낮은 것으로 간주되는 자체 서명된 인증서를 사용할 수 있습니다.

  서버의 자체 서명된 인증서를 설치하려면 **Windows 인증 관리자**(certmgr.exe) 도구를 사용할 수 있습니다. 이 시나리오의 경우 온-프레미스 데이터 게이트웨이 서비스가 실행되는 로컬 컴퓨터에서 **신뢰할 수 있는 루트 인증 기관** 수준에서 **로컬 컴퓨터** 인증서 저장소에 인증서를 설치해야 합니다.

  1. 온-프레미스 데이터 게이트웨이 서비스가 실행되고 있는 컴퓨터에서 시작 메뉴를 열고 **사용자 인증서 관리**을 찾아 선택합니다.

  1. Windows 인증 관리자 도구를 연 후 **인증서 - 로컬 컴퓨터** >  **신뢰할 수 있는 루트 인증 기관** 폴더로 이동하여 인증서를 설치합니다.

     > [!IMPORTANT]
     > **인증서 - 로컬 컴퓨터** > **신뢰할 수 있는 루트 인증 기관** 저장소에 인증서를 설치해야 합니다.

* MQ 서버를 사용 하려면 TLS/SSL 연결에 사용 하려는 암호화 사양을 정의 해야 합니다. 그러나 .NET의 SslStream에서는 암호 사양의 순서를 지정 하는 것을 허용 하지 않습니다. 이 제한을 해결 하려면 커넥터가 TLS/SSL 협상에서 전송 하는 도구 모음의 첫 번째 암호화 사양과 일치 하도록 MQ 서버 구성을 변경할 수 있습니다.

  연결을 시도하면 MQ 서버는 다른 쪽 끝에서 잘못된 암호화 사양을 사용했으므로 연결이 실패했음을 나타내는 이벤트 메시지를 기록합니다. 이벤트 메시지에는 목록에서 맨 처음 표시되는 암호화 사양이 포함됩니다. 채널 구성의 암호화 사양을 이벤트 메시지의 암호화 사양과 일치하도록 업데이트합니다.

## <a name="browse-single-message"></a>단일 메시지 찾아보기

1. 논리 앱의 트리거 또는 다른 작업에서 **새 단계**를 선택합니다.

1. 검색 상자에 `mq`를 입력하고 **메시지 찾아보기** 작업을 선택합니다.

   !["메시지 찾아보기" 작업 선택](media/connectors-create-api-mq/browse-message.png)

1. MQ 연결을 아직 만들지 않은 경우 [해당 연결을 만들라는](#create-connection) 요청 메시지가 표시됩니다.

1. 연결을 만든 후 **메시지 찾아보기** 작업의 속성을 설정합니다.

   | 속성 | Description |
   |----------|-------------|
   | **큐** | 연결에 지정된 큐와 다른 경우 해당 큐를 지정합니다. |
   | **MessageId**, **CorrelationId**, **GroupId** 및 기타 속성 | 다른 MQ 메시지 속성을 기반으로 하는 메시지 찾아보기 |
   | **IncludeInfo** | 출력에 추가 메시지 정보를 포함하려면 **true**를 선택합니다. 출력에서 추가 메시지 정보를 생략하려면 **false**를 선택합니다. |
   | **Timeout** | 비어 있는 큐에 도달하는 메시지를 기다리는 기간을 결정하는 값을 입력합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다. |
   |||

   다음은 그 예입니다.

   !["메시지 찾아보기" 작업의 속성](media/connectors-create-api-mq/browse-message-properties.png)

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다. 앱을 테스트 하려면 **실행**을 선택합니다.

   실행이 완료되면 디자이너에서 워크플로 단계와 해당 상태를 표시하여 출력을 검토할 수 있습니다.

1. 각 단계에 대한 세부 정보를 보려면 단계의 제목 표시줄을 클릭합니다. 단계의 출력에 대한 자세한 내용을 보려면 **원시 출력 표시**를 선택합니다.

   ![메시지 찾아보기 출력](media/connectors-create-api-mq/browse-message-output.png)

   다음은 샘플 원시 출력입니다.

   ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/browse-message-raw-output.png)

1. **IncludeInfo**를 **true**로 설정하면 다음과 같은 추가 출력이 표시됩니다.

   ![메시지 찾아보기 포함 정보](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기

**메시지 찾아보기** 작업은 큐에서 반환되는 메시지의 수를 나타내는 **BatchSize** 옵션을 포함합니다. **BatchSize**에 값이 없는 경우 모든 메시지가 반환됩니다. 반환된 출력은 메시지의 배열입니다.

1. 이전 단계를 수행하지만 **메시지 찾아보기** 작업을 대신 추가합니다.

1. MQ 연결을 아직 만들지 않은 경우 [해당 연결을 만들라는](#create-connection) 요청 메시지가 표시됩니다. 그러지 않으면 기본적으로 이전에 구성된 첫 번째 연결이 사용됩니다. 새 연결을 만들려면 **연결 변경**을 선택합니다. 또는 다른 연결을 선택합니다.

1. 작업에 대한 정보를 제공합니다.

1. 논리 앱을 저장하고 실행합니다.

   논리 앱의 실행이 완료된 후에는 **메시지 찾아보기** 작업에서 다음과 같은 샘플 출력이 표시됩니다.

   ![샘플 "메시지 찾아보기" 출력](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>단일 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

> [!NOTE]
> 메시지를 포함하지 않는 큐에서 찾아보기 또는 받기 작업을 실행하는 경우 다음과 같은 출력을 나타내며 작업이 실패합니다.
>
> ![MQ "메시지 오류" 없음](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>메시지 보내기

1. 이전 단계를 수행하지만 **메시지 보내기** 작업을 대신 추가합니다.

1. MQ 연결을 아직 만들지 않은 경우 [해당 연결을 만들라는](#create-connection) 요청 메시지가 표시됩니다. 그러지 않으면 기본적으로 이전에 구성된 첫 번째 연결이 사용됩니다. 새 연결을 만들려면 **연결 변경**을 선택합니다. 또는 다른 연결을 선택합니다.

1. 작업에 대한 정보를 제공합니다. **MessageType**에서 올바른 메시지 유형을 선택합니다. **데이터그램**, **회신** 또는 **요청**

   !["메시지 보내기 작업"의 속성](media/connectors-create-api-mq/send-message-properties.png)

1. 논리 앱을 저장하고 실행합니다.

   논리 앱의 실행이 완료된 후에는 **메시지 보내기** 작업에서 다음과 같은 샘플 출력이 표시됩니다.

   ![샘플 "메시지 보내기" 출력](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 설명에 나오는 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/mq/)를 검토하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
