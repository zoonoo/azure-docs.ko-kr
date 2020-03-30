---
title: Office 365 Outlook 연결
description: Azure 논리 앱을 사용하여 Office 365 Outlook에서 전자 메일, 연락처 및 일정을 관리하는 작업 및 워크플로자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732707"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Office 365 Outlook에서 이메일, 연락처 및 일정 관리

[Azure 논리 앱](../logic-apps/logic-apps-overview.md) 및 Office [365 Outlook 커넥터를](/connectors/office365connector/)사용하면 논리 앱을 빌드하여 Office 365 계정을 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음 작업을 자동화합니다.

* 이메일 받기, 보내기 및 회신. 
* 캘린더에서 모임을 예약합니다.
* 연락처를 추가하고 편집합니다. 

예를 들어 새 전자 메일이 도착하거나 일정 항목이 업데이트되는 경우 또는 Salesforce와 같은 차이 서비스에서 이벤트가 발생하는 경우와 같이 모든 트리거를 사용하여 워크플로를 시작할 수 있습니다. 예를 들어 전자 메일을 보내거나 새 캘린더 이벤트를 만드는 등 트리거 이벤트에 응답하는 작업을 사용할 수 있습니다. 

> [!NOTE]
> 또는 계정의 작업을 자동화하려면 [Outlook.com 커넥터를](../connectors/connectors-create-api-outlook.md)사용합니다. @hotmail.com @outlook.com

## <a name="prerequisites"></a>사전 요구 사항

* [Office 365 계정](https://www.office.com/)

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Office 365 Outlook 계정에 액세스하려는 논리 앱입니다. Office 365 Outlook 트리거로 워크플로를 시작하려면 [빈 논리 앱이](../logic-apps/quickstart-create-first-logic-app-workflow.md)있어야 합니다. 워크플로에 Office 365 Outlook 작업을 추가하려면 논리 앱에 트리거가 이미 있어야 합니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거는](../logic-apps/logic-apps-overview.md#logic-app-concepts) 논리 앱에서 워크플로를 시작하는 이벤트입니다. 이 예제 논리 앱은 지정된 간격 과 빈도에 따라 이메일 계정의 업데이트된 캘린더 이벤트를 확인하는 "폴링" 트리거를 사용합니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제는 **예정된 이벤트가 곧 시작되는 시기를**선택합니다.
   
   ![로직 앱을 시작하려면 트리거 선택](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 로그인하라는 메시지가 표시되면 논리 앱이 계정에 연결할 수 있도록 Office 365 자격 증명을 제공합니다. 그렇지 않으면 연결이 이미 있는 경우 트리거의 속성에 대한 정보를 제공합니다.

   이 예제에서는 트리거가 검사하는 달력을 선택합니다.

   ![트리거의 속성 구성](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 트리거에서 **주파수** 및 **간격** 값을 설정합니다. **표준 시간대와**같은 다른 사용 가능한 트리거 속성을 추가하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택합니다.

   예를 들어 트리거가 15분마다 일정을 확인하려면 **빈도를** **분으로**설정하고 **간격을** 로 설정합니다. `15` 

   ![트리거의 빈도 및 간격 설정](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 트리거가 실행된 후 실행되는 작업을 추가합니다. 예를 들어 캘린더 이벤트가 15분 안에 시작될 때 텍스트를 보내는 Twilio **보내기 메시지** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업은](../logic-apps/logic-apps-overview.md#logic-app-concepts) 논리 앱의 워크플로에서 실행되는 작업입니다. 이 예제 논리 응용 프로그램은 Office 365 Outlook에서 새 연락처를 만듭니다. 다른 트리거 또는 작업의 출력을 사용하여 연락처를 만들 수 있습니다. 예를 들어 논리 앱에서 Dynamics 365 트리거를 사용한다고 가정할 **때 레코드가 만들어집니다.** Office 365 Outlook **연락처 만들기** 작업을 추가하고 SalesForce 트리거의 출력을 사용하여 새 연락처를 만들 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 작업을 추가하려면 **새 단계를**선택합니다. 

   단계 간에 작업을 추가하려면 포인터를 해당 단계 사이의 화살표 위로 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제는 **연락처 만들기를**선택합니다.

   ![논리 앱에서 실행할 작업을 선택합니다.](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 로그인하라는 메시지가 표시되면 논리 앱이 계정에 연결할 수 있도록 Office 365 자격 증명을 제공합니다. 그렇지 않으면 연결이 이미 있는 경우 작업 속성에 대한 정보를 제공합니다.

   이 예제에서는 작업에서 새 연락처를 만드는 연락처 폴더를 선택합니다.

   ![작업 속성 구성](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   사용 가능한 다른 작업 속성을 추가하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택합니다.

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

커넥터의 Swagger 파일에 설명된 대로 트리거, 작업 및 제한에 대한 기술 정보는 [커넥터의 참조 페이지를](/connectors/office365connector/)참조하십시오. 

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
