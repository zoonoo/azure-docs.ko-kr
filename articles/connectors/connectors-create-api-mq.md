---
title: IBM MQ 서버에 연결
description: Azure 또는 온-프레미스 IBM MQ 서버 및 Azure 논리 앱으로 메시지 보내기 및 검색
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650950"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure 논리 앱에서 IBM MQ 서버에 연결

IBM MQ 커넥터는 온프레미스 또는 Azure에 있는 IBM MQ 서버에 저장된 메시지를 보내고 검색합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서에서는 MQ 커넥터를 사용하는 시작 가이드를 제공합니다. 큐에서 단일 메시지를 탐색한 다음 다른 작업을 시도할 수 있습니다.

IBM MQ 커넥터에는 이러한 작업이 포함되어 있지만 트리거는 제공하지 않습니다.

- IBM MQ 서버에서 메시지를 삭제하지 않고 단일 메시지 찾아보기
- IBM MQ 서버에서 메시지를 삭제하지 않고 메시지 일괄 처리 찾아보기
- 단일 메시지를 수신하고 IBM MQ 서버에서 메시지 삭제
- 메시지 일괄 처리 수신 및 IBM MQ 서버에서 메시지 삭제
- IBM MQ 서버에 단일 메시지 보내기

## <a name="prerequisites"></a>사전 요구 사항

* 온-프레미스 MQ 서버를 사용하는 경우 네트워크 내의 서버에 [온-프레미스 데이터 게이트웨이를 설치합니다.](../logic-apps/logic-apps-gateway-install.md) 온-프레미스 데이터 게이트웨이가 설치된 서버에는 MQ 커넥터가 작동하도록 .NET Framework 4.6도 설치되어 있어야 합니다. 또한 온-프레미스 데이터 게이트웨이에 대 한 Azure에서 리소스를 만들어야 합니다. 자세한 내용은 [데이터 게이트웨이 연결 설정을](../logic-apps/logic-apps-gateway-connection.md)참조하십시오.

  그러나 MQ 서버가 Azure 내에서 공개적으로 사용 가능하거나 사용할 수 있는 경우 데이터 게이트웨이를 사용할 필요가 없습니다.

* 공식적으로 지원되는 IBM WebSphere MQ 버전:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* MQ 작업을 추가하려는 논리 앱입니다. 이 논리 앱은 온-프레미스 데이터 게이트웨이 연결과 동일한 위치를 사용해야 하며 워크플로를 시작하는 트리거가 이미 있어야 합니다. 

  MQ 커넥터에는 트리거가 없으므로 논리 앱에 먼저 트리거를 추가해야 합니다. 예를 들어 되풀이 트리거를 사용할 수 있습니다. 논리 앱을 처음 접하는 경우 이 [빠른 시작을 시도하여 첫 번째 논리 앱을 만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="browse-a-single-message"></a>단일 메시지 찾아보기

1. 논리 앱에서 트리거 또는 다른 작업에서 **새 단계를**선택합니다. 

1. 검색 상자에서 "mq"를 입력하고 다음 **Browse message** 작업을 선택합니다.

   ![메시지 찾아보기](media/connectors-create-api-mq/Browse_message.png)

1. 기존 MQ 연결이 없는 경우 연결을 만듭니다.  

   1. 작업에서 **온-프레미스 데이터 게이트웨이를 통해 연결을**선택합니다.
   
   1. MQ 서버의 속성을 입력합니다.  

      **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.
    
   1. 이전에 구성된 게이트웨이 연결을 보여 드리는 **게이트웨이** 목록을 엽니다. 게이트웨이를 선택합니다.
    
   1. 작업을 완료하면 **만들기**를 선택합니다. 
   
      연결은 다음과 같습니다.

      ![연결 속성](media/connectors-create-api-mq/Connection_Properties.png)

1. 작업의 속성을 설정합니다.

   * **큐**: 연결과 다른 큐를 지정합니다.

   * **MessageId**, **상관 관계 Id**, **GroupId**및 기타 속성: 다른 MQ 메시지 속성을 기반으로 메시지를 찾아보십시오.

   * **IncludeInfo**: 출력에 추가 메시지 정보를 포함하도록 **True를** 지정합니다. 또는 출력에 추가 메시지 정보를 포함하지 않도록 **False를** 지정합니다.

   * **시간 시간:** 값을 입력하여 메시지가 빈 큐에 도착할 때까지 기다리는 시간을 결정합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다.

     ![메시지 속성 찾아보기](media/connectors-create-api-mq/Browse_message_Props.png)

1. 변경 내용을 **저장한** 다음 논리 앱을 **실행합니다.**

   ![저장 및 실행](media/connectors-create-api-mq/Save_Run.png)

   실행이 완료되면 실행의 단계가 표시되고 출력을 검토할 수 있습니다.

1. 각 단계에 대한 세부 정보를 검토하려면 녹색 체크 표시를 선택합니다. 출력 데이터에 대한 자세한 정보를 검토하려면 **원시 출력 표시를 선택합니다.**

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_message_output.png)  

   다음은 몇 가지 샘플 원시 출력입니다.

   ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. **IncludeInfo를** true로 설정하면 다음 출력이 표시됩니다.

   ![메시지 포함 정보 찾아보기](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기

**메시지 찾아보기** 작업은 큐에서 반환되는 메시지의 수를 나타내는 **BatchSize** 옵션을 포함합니다.  **BatchSize**에 항목이 없는 경우 모든 메시지가 반환됩니다. 반환된 출력은 메시지의 배열입니다.

1. **메시지 찾아보기** 작업을 추가하면 이전에 구성된 첫 번째 연결이 기본적으로 선택됩니다. 새 연결을 만들려면 **연결 변경을**선택합니다. 또는 다른 연결을 선택합니다.

1. 논리 앱 실행이 완료되면 **찾아보기 메시지** 작업의 일부 샘플 출력은 다음과 같습니다.

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>단일 메시지 받기

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

찾아보기 또는 수신을 수행할 때 큐에 메시지가 없는 경우 이 출력으로 단계가 실패합니다.  

![MQ 메시지 오류 없음](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>메시지 보내기

**메시지 보내기** 작업을 추가하면 이전에 구성된 첫 번째 연결이 기본적으로 선택됩니다. 새 연결을 만들려면 **연결 변경을**선택합니다. 또는 다른 연결을 선택합니다.

1. 유효한 메시지 유형 선택: **데이터그램,** **회신**또는 **요청**  

   ![메시지 속성 전송](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 논리 앱 실행이 끝나면 **메시지 보내기** 작업의 일부 샘플 출력은 다음과 같습니다.

   ![메시지 출력 전송](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 트리거, 작업 및 제한과 같은 이 커넥터에 대한 자세한 내용은 [커넥터의 참조 페이지를](https://docs.microsoft.com/connectors/mq/)참조하십시오.

> [!NOTE]
> [통합 서비스 환경(ISE)의](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)논리 앱의 경우 이 커넥터의 ISE 레이블이 지정된 버전은 [ISE 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 대신 사용합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
