---
title: Outlook.com에 연결
description: Azure Logic Apps를 사용하여 Outlook.com의 메일, 일정, 연락처를 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75707189"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Outlook.com에서 메일, 일정, 연락처 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Outlook.com 커넥터](/connectors/outlook/)를 사용하여 로직 앱 빌드를 통해 @outlook.com 또는 @hotmail.com 계정을 관리하는 자동화된 작업 및 워크로드를 만들 수 있습니다. 예를 들어 다음과 같은 작업을 자동화합니다.

* 메일을 가져오고 보내고 회신합니다.
* 일정에서 모임을 예약합니다.
* 연락처를 추가하고 편집합니다.

트리거를 사용하여 워크플로를 시작할 수 있습니다. 예를 들어 새 메일이 도착하거나 일정 항목이 업데이트되거나 다른 서비스에서 이벤트가 발생하는 경우를 들 수 있습니다. 트리거 이벤트에 응답하는 작업(예: 메일 보내기 또는 새 일정 이벤트 만들기)을 사용할 수 있습니다.

> [!NOTE]
> @fabrikam.onmicrosoft.com과 같은 Microsoft 회사 계정에 대한 작업을 자동화하려면 [Office 365 Outlook 커넥터](../connectors/connectors-create-api-office365-outlook.md)를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Outlook.com 계정](https://outlook.live.com/owa/)

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Outlook.com 계정에 액세스하려는 논리 앱입니다. Outlook.com 트리거를 사용하여 워크플로를 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 있어야 합니다. 워크플로에 Outlook.com 작업을 추가하려면 논리 앱에 트리거가 이미 있어야 합니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 이 예제 논리 앱은 지정된 간격 및 빈도에 따라 메일 계정에서 모든 새 메일을 확인하는 “폴링” 트리거를 사용합니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 “outlook.com”을 필터로 입력합니다. 이 예제에서는 **새 메일이 도착하는 경우** 를 선택합니다.

1. 로그인하라는 메시지가 표시되면 논리 앱이 계정에 연결할 수 있도록 Outlook.com 자격 증명을 제공합니다. 연결이 이미 존재하는 경우 트리거의 속성에 대한 정보를 제공합니다.

1. 트리거에서 **빈도** 및 **간격** 값을 설정합니다.

   예를 들어 15분마다 폴링을 트리거하려면 **빈도** 를 **분** 으로, **간격** 을 **15** 로 설정합니다.

1. 디자이너 도구 모음에서 논리 앱을 저장하는 **저장** 을 선택합니다.

트리거에 응답하려면 다른 작업을 추가합니다. 예를 들어 메일이 도착할 때 텍스트를 보내는 Twilio **메시지 보내기** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 논리 앱의 워크플로에 의해 수행되는 작업입니다. 해당 예제 논리 앱은 Outlook.com 계정에서 메일을 보냅니다. 다른 트리거의 출력을 사용하여 작업을 채울 수 있습니다. 예를 들어 로직 앱에서 SalesForce **개체를 만들 때** 트리거를 사용한다고 가정합니다. Outlook.com **메일 보내기** 작업을 추가하고 SalesForce 트리거의 출력을 메일에서 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 작업을 추가하려면 **새 단계** 를 선택합니다. 

   단계 사이에 작업을 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 검색 상자에 필터로 “outlook.com”을 입력합니다. 이 예제에서는 **메일 보내기** 를 선택합니다. 

1. 로그인하라는 메시지가 표시되면 논리 앱이 계정에 연결할 수 있도록 Outlook.com 자격 증명을 제공합니다. 연결이 이미 존재하는 경우 작업 속성에 대한 정보를 제공합니다.

1. 디자이너 도구 모음에서 논리 앱을 저장하는 **저장** 을 선택합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/outlook/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
