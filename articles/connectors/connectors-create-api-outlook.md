---
title: Outlook.com에 연결
description: Azure Logic Apps를 사용하여 Outlook.com 전자 메일, 일정 및 연락처를 관리하는 작업 및 워크플로자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707189"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Azure Logic 앱을 사용하여 Outlook.com 이메일, 캘린더 및 연락처 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 Outlook.com [커넥터를](/connectors/outlook/)사용하면 논리 앱을 빌드하여 @outlook.com @hotmail.com 또는 계정을 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음 작업을 자동화합니다.

* 이메일 받기, 보내기 및 회신.
* 캘린더에서 모임을 예약합니다.
* 연락처를 추가하고 편집합니다.

트리거를 사용하여 워크플로를 시작할 수 있습니다(예: 새 전자 메일이 도착할 때, 일정 항목이 업데이트되는 경우 또는 차이 서비스에서 이벤트가 발생하는 경우). 예를 들어 전자 메일을 보내거나 새 캘린더 이벤트를 만드는 등 트리거 이벤트에 응답하는 작업을 사용할 수 있습니다.

> [!NOTE]
> 과 같은 @fabrikam.onmicrosoft.comMicrosoft 작업 계정에 대한 작업을 자동화하려면 [Office 365 Outlook 커넥터를](../connectors/connectors-create-api-office365-outlook.md)사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Outlook.com 계정](https://outlook.live.com/owa/)

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Outlook.com 계정에 액세스하려는 논리 앱입니다. Outlook.com 트리거로 워크플로를 시작하려면 빈 [논리 앱이](../logic-apps/quickstart-create-first-logic-app-workflow.md)있어야 합니다. 워크플로에 Outlook.com 작업을 추가하려면 논리 앱에 트리거가 이미 있어야 합니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거는](../logic-apps/logic-apps-overview.md#logic-app-concepts) 논리 앱에서 워크플로를 시작하는 이벤트입니다. 이 예제 논리 앱은 지정된 간격 과 빈도에 따라 이메일 계정의 새 전자 메일을 확인하는 "폴링" 트리거를 사용합니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 “outlook.com”을 필터로 입력합니다. 이 예제에서는 **새 전자 메일이 도착하는 시기를 선택합니다.**

1. 로그인하라는 메시지가 표시되면 로직 앱이 계정에 연결할 수 있도록 Outlook.com 자격 증명을 제공합니다. 그렇지 않으면 연결이 이미 있는 경우 트리거 속성에 대한 정보를 제공합니다.

1. 트리거에서 **주파수** 및 **간격** 값을 설정합니다.

   예를 들어 트리거가 15분마다 폴링하도록 하려면 **빈도를** **분으로**설정하고 **간격을** **15로**설정합니다.

1. 디자이너 도구 모음에서 논리 앱을 저장하는 **저장을**선택합니다.

트리거에 응답하려면 다른 작업을 추가합니다. 예를 들어 이메일이 도착하면 텍스트를 보내는 Twilio **보내기 메시지** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업은](../logic-apps/logic-apps-overview.md#logic-app-concepts) 논리 앱의 워크플로에서 실행되는 작업입니다. 이 예제 논리 앱은 Outlook.com 계정에서 이메일을 보냅니다. 다른 트리거의 출력을 사용하여 작업을 채울 수 있습니다. 예를 들어 논리 앱에서 개체가 만들어지면 SalesForce **트리거가 사용한다고** 가정합니다. Outlook.com **전자 메일** 보내기 작업을 추가하고 전자 메일에서 SalesForce 트리거의 출력을 사용할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 작업을 추가하려면 **새 단계를**선택합니다. 

   단계 간에 작업을 추가하려면 포인터를 해당 단계 사이의 화살표 위로 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. 검색 상자에 필터로 "outlook.com"를 입력합니다. 이 예제에서는 **전자 메일 보내기를**선택합니다. 

1. 로그인하라는 메시지가 표시되면 로직 앱이 계정에 연결할 수 있도록 Outlook.com 자격 증명을 제공합니다. 그렇지 않으면 연결이 이미 있는 경우 작업 속성에 대한 정보를 제공합니다.

1. 디자이너 도구 모음에서 논리 앱을 저장하는 **저장을**선택합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/outlook/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
