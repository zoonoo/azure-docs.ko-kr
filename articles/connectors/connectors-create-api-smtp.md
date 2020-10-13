---
title: Azure Logic Apps에서 SMTP에 연결
description: Azure Logic Apps를 사용하여 SMTP(Simple Mail Transfer Protocol) 계정을 통해 이메일을 전송하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283948"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 SMTP 계정에서 이메일 보내기

Azure Logic Apps 및 SMTP(Simple Mail Transfer Protocol) 커넥터를 사용하여 SMTP 계정에서 이메일을 전송하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 또한 다른 작업에서 SMTP 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 SMTP에서 이메일을 보낸 후 Slack 커넥터를 사용하여 Slack 팀에 알릴 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* SMTP 계정 및 사용자 자격 증명

  자격 증명을 통해 SMTP 계정에 대한 연결을 만들고 액세스하는 권한이 논리 앱에 부여됩니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SMTP 계정에 액세스하려는 논리 앱입니다. SMTP 작업을 사용하려면 Salesforce 계정이 있는 경우 Salesforce 트리거와 같은 트리거를 사용하여 논리 앱을 시작합니다.

  예를 들어 **레코드가 만들어지는 경우** Salesforce 트리거를 사용하여 논리 앱을 시작할 수 있습니다. 
  이 트리거는 잠재 고객과 같은 새 레코드가 Salesforce에서 만들어질 때마다 발생합니다. 
  그런 다음, SMTP **이메일 보내기** 작업을 사용하여 이 트리거를 따를 수 있습니다. 이런 방식으로 새 레코드가 만들어질 때 논리 앱은 새 레코드에 대한 SMTP 계정에서 이메일을 보냅니다.

## <a name="connect-to-smtp"></a>SMTP에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. SMTP 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가**를 선택합니다.

1. 검색 상자에서 필터로 "smtp"를 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. 메시지가 표시되면 이 연결 정보를 제공합니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **연결 이름** | 예 | SMTP 서버에 대한 연결의 이름 | 
   | **SMTP 서버 주소** | 예 | SMTP 서버에 대한 주소 | 
   | **사용자 이름** | 예 | SMTP 계정에 대한 사용자 이름 | 
   | **암호** | 예 | SMTP 계정에 대한 암호 | 
   | **SMTP 서버 포트** | 아니요 | 사용하려는 SMTP 서버의 특정 포트 | 
   | **SSL을 사용 하도록 설정 하 시겠습니까?** | 아니요 | TLS/SSL 암호화를 설정 하거나 해제 합니다. | 
   |||| 

1. 선택한 작업에 필요한 정보를 입력합니다. 

1. 논리 앱을 저장하거나 논리 앱의 워크플로 빌드를 계속합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명 된 대로 트리거, 작업 및 제한과 같은이 커넥터에 대 한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/smtpconnector/)를 참조 하세요.

> [!NOTE]
> [Ise (통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md))의 논리 앱의 경우이 커넥터의 ise 레이블 버전은 [ise 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 대신 사용 합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
