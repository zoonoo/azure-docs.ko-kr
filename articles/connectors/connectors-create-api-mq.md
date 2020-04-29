---
title: IBM MQ server에 연결
description: Azure 또는 온-프레미스 IBM MQ 서버를 사용 하 여 메시지를 보내고 검색 하 고 Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80410213"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure Logic Apps에서 IBM MQ 서버에 연결

IBM MQ 커넥터는 온-프레미스 또는 Azure에서 IBM MQ 서버에 저장 된 메시지를 보내고 검색 합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서에서는 MQ 커넥터를 사용 하기 위한 시작 가이드를 제공 합니다. 먼저 큐에서 단일 메시지를 검색 한 다음 다른 작업을 시도할 수 있습니다.

IBM MQ 커넥터에는 이러한 작업이 포함 되지만 트리거는 제공 되지 않습니다.

- IBM MQ 서버에서 메시지를 삭제 하지 않고 단일 메시지를 찾아봅니다.
- IBM MQ 서버에서 메시지를 삭제 하지 않고 메시지 일괄 처리를 찾습니다.
- 단일 메시지를 받고 IBM MQ 서버에서 메시지를 삭제 합니다.
- 메시지 일괄 처리를 받고 IBM MQ 서버에서 메시지를 삭제 합니다.
- IBM MQ 서버에 단일 메시지를 보냅니다.

다음은 공식적으로 지원 되는 IBM WebSphere 대 한 MQ 버전입니다.

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>전제 조건

* 온-프레미스 MQ 서버를 사용 하는 경우 네트워크 내의 서버에 [온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md) 합니다. MQ 커넥터가 작동 하려면 온-프레미스 데이터 게이트웨이를 설치한 서버에 .NET Framework 4.6도 설치 되어 있어야 합니다.

  게이트웨이 설치가 완료 되 면 Azure에서 온-프레미스 데이터 게이트웨이에 대 한 리소스도 만들어야 합니다. 자세한 내용은 [data gateway 연결 설정](../logic-apps/logic-apps-gateway-connection.md)을 참조 하세요.

  MQ 서버를 Azure 내에서 공개적으로 사용할 수 있거나 사용할 수 있는 경우 데이터 게이트웨이를 사용할 필요가 없습니다.

* MQ 작업을 추가 하려는 논리 앱입니다. 이 논리 앱은 온-프레미스 데이터 게이트웨이 연결과 동일한 위치를 사용 해야 하며 워크플로를 시작 하는 트리거가 이미 있어야 합니다.

  MQ 커넥터에는 트리거가 없으므로 먼저 논리 앱에 트리거를 추가 해야 합니다. 예를 들어 되풀이 트리거를 사용할 수 있습니다. 논리 앱을 처음 접하는 경우이 [빠른 시작을 사용해 서 첫 번째 논리 앱을 만드세요](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ 연결 만들기

MQ 작업을 추가할 때 MQ 연결이 아직 없는 경우 연결을 만들라는 메시지가 표시 됩니다. 예를 들면 다음과 같습니다.

![연결 정보 제공](media/connectors-create-api-mq/connection-properties.png)

1. 온-프레미스 MQ 서버에 연결 하는 경우 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택 합니다.

1. MQ 서버에 대 한 연결 정보를 제공 합니다.

   * **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.

   * SSL(Secure Sockets Layer) (SSL)을 사용 하려면 **Ssl 사용**을 선택 합니다.

     MQ 커넥터는 현재 클라이언트 인증이 아닌 서버 인증만 지원 합니다. 자세한 내용은 [연결 및 인증 문제](#connection-problems)를 참조 하세요.

1. **게이트웨이** 섹션에서 다음 단계를 수행 합니다.

   1. **구독** 목록에서 azure 게이트웨이 리소스와 연결 된 azure 구독을 선택 합니다.

   1. **연결 게이트웨이** 목록에서 사용 하려는 Azure 게이트웨이 리소스를 선택 합니다.

1. 완료되면 **만들기**를 선택합니다.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>연결 및 인증 문제

논리 앱이 온-프레미스 MQ 서버에 연결 하려고 하면 다음과 같은 오류가 발생할 수 있습니다.

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Azure에서 직접 MQ 커넥터를 사용 하는 경우 MQ 서버는 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/)에서 발급 한 인증서를 사용 해야 합니다.

* 온-프레미스 데이터 게이트웨이를 사용 하는 경우 가능 하면 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/) 에서 발급 한 인증서를 사용 합니다. 그러나이 옵션을 사용할 수 없는 경우에는 자체 서명 된 인증서를 사용할 수 있습니다 .이 인증서는 신뢰할 수 있는 [인증 기관](https://www.ssl.com/faqs/what-is-a-certificate-authority/) 에서 발급 되지 않으며 낮은 수준으로 간주 됩니다.

  서버의 자체 서명 된 인증서를 설치 하려면 **Windows 인증 관리자** (certmgr.exe) 도구를 사용할 수 있습니다. 이 시나리오의 경우 온-프레미스 데이터 게이트웨이 서비스가 실행 되는 로컬 컴퓨터에서 **신뢰할 수 있는 루트 인증 기관** 수준의 **로컬 컴퓨터** 인증서 저장소에 인증서를 설치 해야 합니다.

  1. 온-프레미스 데이터 게이트웨이 서비스가 실행 되 고 있는 컴퓨터에서 시작 메뉴를 열고 **사용자 인증서 관리**를 찾아 선택 합니다.

  1. Windows 인증 관리자 도구를 연 후 **인증서-로컬 컴퓨터** >  의**신뢰할 수 있는 루트 인증 기관** 폴더로 이동 하 여 인증서를 설치 합니다.

     > [!IMPORTANT]
     > 인증서 **-로컬 컴퓨터** > 의**신뢰할 수 있는 루트 인증 기관** 저장소에 인증서를 설치 했는지 확인 합니다.

* MQ 서버를 사용 하려면 SSL 연결에 사용 하려는 암호화 사양을 정의 해야 합니다. 그러나 .NET의 SsLStream에서는 암호 사양의 순서를 지정 하는 것을 허용 하지 않습니다. 이 제한을 해결 하려면 커넥터가 SSL 협상에서 보내는 도구 모음의 첫 번째 암호화 사양과 일치 하도록 MQ 서버 구성을 변경할 수 있습니다.

  연결을 시도 하면 MQ 서버는 다른 쪽 끝에서 잘못 된 암호 사양을 사용 하 여 연결에 실패 했음을 나타내는 이벤트 메시지를 기록 합니다. 이벤트 메시지에는 목록에 먼저 표시 되는 암호화 사양이 포함 됩니다. 채널 구성의 암호화 사양을 이벤트 메시지의 암호화 사양과 일치 하도록 업데이트 합니다.

## <a name="browse-single-message"></a>단일 메시지 찾아보기

1. 논리 앱의 트리거 또는 다른 작업에서 **새 단계**를 선택 합니다.

1. 검색 상자에를 입력 `mq`하 고 **메시지 찾아보기** 작업을 선택 합니다.

   !["메시지 찾아보기" 작업을 선택 합니다.](media/connectors-create-api-mq/browse-message.png)

1. MQ 연결을 아직 만들지 않은 경우 [해당 연결](#create-connection)을 만들라는 메시지가 표시 됩니다.

1. 연결을 만든 후 **메시지 찾아보기** 작업의 속성을 설정 합니다.

   | 속성 | Description |
   |----------|-------------|
   | **큐** | 연결에 지정 된 큐와 다른 경우 해당 큐를 지정 합니다. |
   | **MessageId**, **CorrelationId**, **GroupId**및 기타 속성 | 다른 MQ 메시지 속성을 기반으로 하는 메시지 찾아보기 |
   | **IncludeInfo** | 출력에 추가 메시지 정보를 포함 하려면 **true**를 선택 합니다. 출력에서 추가 메시지 정보를 생략 하려면 **false**를 선택 합니다. |
   | **Timeout** | 메시지가 빈 큐에 도착할 때까지 대기 하는 시간을 결정 하는 값을 입력 합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다. |
   |||

   다음은 그 예입니다.

   !["메시지 찾아보기" 작업의 속성](media/connectors-create-api-mq/browse-message-properties.png)

1. 작업이 완료 되 면 디자이너 도구 모음에서 **저장**을 선택 합니다. 앱을 테스트 하려면 **실행**을 선택 합니다.

   실행이 완료 되 면 디자이너에서 워크플로 단계와 해당 상태를 표시 하 여 출력을 검토할 수 있습니다.

1. 각 단계에 대 한 세부 정보를 보려면 단계의 제목 표시줄을 클릭 합니다. 단계의 출력에 대 한 자세한 내용을 보려면 **원시 출력 표시**를 선택 합니다.

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/browse-message-output.png)

   다음은 몇 가지 샘플 원시 출력입니다.

   ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/browse-message-raw-output.png)

1. **Includeinfo** 를 **true**로 설정 하면 다음과 같은 추가 출력이 표시 됩니다.

   ![메시지 포함 정보 찾아보기](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기

**메시지 찾아보기** 작업에는 큐에서 반환할 메시지 수를 나타내는 **BatchSize** 옵션이 포함 되어 있습니다. **BatchSize** 에 값이 없으면 모든 메시지가 반환 됩니다. 반환된 출력은 메시지의 배열입니다.

1. 이전 단계를 수행 하지만 **메시지 찾아보기** 작업을 대신 추가 합니다.

1. MQ 연결을 아직 만들지 않은 경우 [해당 연결](#create-connection)을 만들라는 메시지가 표시 됩니다. 그렇지 않은 경우에는 기본적으로 이전에 구성 된 첫 번째 연결이 사용 됩니다. 새 연결을 만들려면 **연결 변경**을 선택 합니다. 또는 다른 연결을 선택 합니다.

1. 작업에 대 한 정보를 제공 합니다.

1. 논리 앱을 저장 하 고 실행 합니다.

   논리 앱의 실행이 완료 된 후에는 **메시지 찾아보기** 작업의 몇 가지 샘플 출력을 확인할 수 있습니다.

   ![샘플 "메시지 찾아보기" 출력](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>단일 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 받기**를 사용 하면 메시지가 큐에서 삭제 됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **수신 메시지**를 사용 하면 메시지가 큐에서 삭제 됩니다.

> [!NOTE]
> 메시지를 포함 하지 않는 큐에서 찾아보기 또는 받기 동작을 실행 하는 경우 다음과 같은 출력을 사용 하 여 작업이 실패 합니다.
>
> ![MQ "메시지 없음" 오류](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>메시지 보내기

1. 이전 단계를 수행 하지만 **메시지 보내기** 작업을 대신 추가 합니다.

1. MQ 연결을 아직 만들지 않은 경우 [해당 연결](#create-connection)을 만들라는 메시지가 표시 됩니다. 그렇지 않은 경우에는 기본적으로 이전에 구성 된 첫 번째 연결이 사용 됩니다. 새 연결을 만들려면 **연결 변경**을 선택 합니다. 또는 다른 연결을 선택 합니다.

1. 작업에 대 한 정보를 제공 합니다. **MessageType**의 경우 올바른 메시지 유형 ( **데이터 그램**, **회신**또는 **요청** )을 선택 합니다.

   !["메시지 보내기 작업"의 속성](media/connectors-create-api-mq/send-message-properties.png)

1. 논리 앱을 저장 하 고 실행 합니다.

   논리 앱의 실행이 완료 된 후 다음은 **메시지 보내기** 작업의 몇 가지 샘플 출력입니다.

   ![샘플 "메시지 보내기" 출력](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 설명에서 설명 하는 작업 및 제한에 대 한 기술 세부 정보는 커넥터의 [참조 페이지를 참조](/connectors/mq/)하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
