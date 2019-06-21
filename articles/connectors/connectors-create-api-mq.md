---
title: IBM MQ 서버-Azure Logic Apps에 연결
description: 보내기 및 IBM MQ 서버를 Azure 또는 온-프레미스 및 Azure Logic Apps를 사용 하 여 메시지를 검색 합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273130"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure Logic Apps에서 IBM MQ 서버에 연결

IBM MQ 커넥터를 보내고 Azure 또는 온-프레미스에서 IBM MQ 서버에 저장 하는 메시지를 검색 합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서는 MQ 커넥터를 사용 하는 시작 가이드를 제공 합니다. 큐에서 단일 메시지를 이동 하 여 시작 수 있으며 다음 다른 작업을 시도 합니다.

IBM MQ 커넥터 이러한 작업을 포함 하지만 트리거 없음 제공:

- IBM MQ 서버에서 메시지를 삭제 하지 않고 단일 메시지 찾아보기
- IBM MQ 서버에서 메시지를 삭제 하지 않고 일괄 처리 메시지 찾아보기
- 단일 메시지 수신 및 IBM MQ 서버에서 메시지를 삭제 합니다.
- 일괄 처리 메시지를 수신 및 IBM MQ 서버에서 메시지 삭제
- IBM MQ 서버에서 단일 메시지 보내기

## <a name="prerequisites"></a>필수 조건

* 온-프레미스 MQ 서버를 사용 하는 경우 [온-프레미스 데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md) 네트워크 내의 서버에서. 온-프레미스 데이터 게이트웨이 설치한 서버에.NET Framework 4.6 MQ 커넥터에 대 한 설치 작업에 있어야 합니다. 온-프레미스 데이터 게이트웨이에 대 한 azure에서 리소스를 만들어야 합니다. 자세한 내용은 [데이터 게이트웨이 연결을 설정](../logic-apps/logic-apps-gateway-connection.md)합니다.

  그러나 MQ 서버가 공개적으로 사용할 수 있거나 Azure 내에서 사용할 수 있는 경우에 데이터 게이트웨이 사용할 필요가 없습니다.

* 공식적으로 지원되는 IBM WebSphere MQ 버전:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* MQ 작업을 추가 하려는 논리 앱. 이 논리 앱 온-프레미스 데이터 게이트웨이 연결이 동일한 위치를 사용 해야 하 고 워크플로 시작 하는 트리거를 이미 있어야 합니다. 

  MQ 커넥터를 논리 앱에 트리거를 먼저 추가 해야 합니다는 트리거가 없습니다. 예를 들어, 되풀이 트리거를 사용할 수 있습니다. 논리 앱을 처음 접하는 경우 사용해 보세요 [첫 번째 논리 앱을 만드는 빠른 시작](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다. 

## <a name="browse-a-single-message"></a>단일 메시지 찾아보기

1. 논리 앱 트리거 또는 다른 작업에서에서 선택 **새 단계**합니다. 

1. 검색 상자에 "mq"를 입력 하 고이 작업을 선택 합니다. **메시지 찾아보기**

   ![메시지 찾아보기](media/connectors-create-api-mq/Browse_message.png)

1. 기존 MQ 연결이 없으면 연결을 만듭니다.  

   1. 작업에서 선택 **온-프레미스 데이터 게이트웨이 통해 연결**합니다.
   
   1. MQ 서버의 속성을 입력 합니다.  

      **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.
    
   1. 엽니다는 **게이트웨이** 이전에 구성 된 모든 게이트웨이 연결을 보여 주는 목록입니다. 게이트웨이를 선택합니다.
    
   1. 작업을 완료하면 **만들기**를 선택합니다. 
   
      연결이이 예제와 비슷합니다.

      ![연결 속성](media/connectors-create-api-mq/Connection_Properties.png)

1. 작업의 속성을 설정 합니다.

   * **큐**: 연결에서 다른 큐를 지정 합니다.

   * **MessageId**하십시오 **CorrelationId**를 **GroupId**, 및 기타 속성: 다른 MQ 메시지 속성을 기반으로 하는 메시지에 대 한 찾아보기

   * **IncludeInfo**: 지정할 **True** 출력에서 추가 메시지 정보를 포함 합니다. 또는 지정할 **False** 출력에서 추가 메시지 정보를 포함 하지 않도록 합니다.

   * **시간 제한**: 빈 큐에 도착 하는 메시지를 기다리는 기간을 결정 하는 값을 입력 합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다.

     ![메시지 속성 찾아보기](media/connectors-create-api-mq/Browse_message_Props.png)

1. **저장할** 변경 내용을 차례로 **실행** 논리 앱.

   ![저장 및 실행](media/connectors-create-api-mq/Save_Run.png)

   실행 완료 되 면 실행에서 단계를 표시 되 고 출력을 검토할 수 있습니다.

1. 각 단계에 대 한 세부 정보를 검토 하려면 녹색 확인 표시를 선택 합니다. 출력 데이터에 대 한 자세한 정보를 검토 하려면 선택할 **원시 출력 표시**합니다.

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_message_output.png)  

   다음은 몇 가지 샘플 원시 출력이입니다.

   ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. 설정 하는 경우 **IncludeInfo** true 이면 다음 출력이 표시 됩니다.

   ![메시지 포함 정보 찾아보기](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기

**메시지 찾아보기** 작업은 큐에서 반환되는 메시지의 수를 나타내는 **BatchSize** 옵션을 포함합니다.  **BatchSize**에 항목이 없는 경우 모든 메시지가 반환됩니다. 반환된 출력은 메시지의 배열입니다.

1. 추가 하는 경우는 **메시지 찾아보기** 작업, 첫 번째 이전에 구성 된 연결 기본적으로 선택 됩니다. 새 연결을 만들려면 **연결 변경**합니다. 또는 다른 연결을 선택 합니다.

1. 논리 앱 실행이 완료 되 면 여기에서 몇 가지 샘플 출력은는 **메시지 찾아보기** 작업:

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>단일 메시지를 수신 합니다.

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

메시지가 없는 큐에는 찾아보기 또는 수신을 수행 하는 경우,이 출력을 사용 하 여 단계는 실패 합니다.  

![MQ 메시지 오류 없음](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>메시지 보내기

추가 하는 경우는 **메시지를 보낼** 작업, 첫 번째 이전에 구성 된 연결 기본적으로 선택 됩니다. 새 연결을 만들려면 **연결 변경**합니다. 또는 다른 연결을 선택 합니다.

1. 유효한 메시지 유형 선택: **데이터 그램**하십시오 **회신**, 또는 **요청**  

   ![메시지 속성 전송](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 논리 앱 실행이 완료 되 면 여기에서 몇 가지 샘플 출력은는 **메시지 보내기** 작업:

   ![메시지 출력 전송](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 openapi 설명 된 동작 및 제한 하는 방법에 대 한 기술 정보 (이전의 Swagger) 설명 커넥터의 검토 [참조 페이지](/connectors/mq/)합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
