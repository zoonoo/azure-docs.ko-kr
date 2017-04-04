---
title: "첫 번째 Azure Logic App을 사용하여 워크플로 만들기 | Microsoft Docs"
description: "첫 번째 Logic App을 사용하여 앱 및 SaaS 서비스 연결 시작"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>SaaS 서비스에 연결하는 새 논리 앱 만들기
이 항목에서는 단 몇 분 안에 [Azure Logic Apps](logic-apps-what-are-logic-apps.md)를 시작할 수 있는 방법을 보여 줍니다. 전자 메일에 흥미로운 트윗을 보낼 수 있는 간단한 워크플로를 안내합니다.

이 시나리오를 사용하려면 다음이 필요합니다.

* Azure 구독
* Twitter 계정
* Outlook.com 또는 Office 365 Outlook 계정

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>트윗으로 전자 메일을 보낼 새 논리 앱 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다. 

2. 왼쪽 메뉴에서 **새로 만들기** > **엔터프라이즈 통합** > **논리 앱**을 선택합니다.

    또한 **새로 만들기**를 선택하고 검색 상자에서 `logic app`을 입력한 다음 Enter 키를 누릅니다. **논리 앱** > **만들기**를 선택합니다.

3. 논리 앱의 이름을 입력하고 Azure 구독을 선택하고 Azure 리소스 그룹을 만들거나 선택하고 위치를 선택하고 **만들기**를 선택합니다.

    **대시보드에 고정**을 선택하는 경우 논리 앱이 배포된 후에 자동으로 열립니다.

4. 논리 앱을 처음으로 연 경우 시작할 템플릿을 선택할 수 있습니다.
지금은 **빈 논리 앱** 을 클릭하여 처음부터 구축합니다. 

5. 만들어야 하는 첫 번째 항목은 트리거입니다. 논리 앱을 시작하는 이벤트입니다. 검색 상자에서 **twitter**를 검색하고 **새로운 트 윗이 게시될 때**를 선택합니다. Twitter 계정의 사용자 이름 및 암호를 사용하여 로그인합니다.

6. 이제 논리 앱을 트리거할 검색 단어를 입력합니다.

   ![Twitter 검색](media/logic-apps-create-a-logic-app/twittersearch.png)

    **주파수** 및 **간격**은 논리 앱이 새 트윗을 확인하는 빈도를 결정하고 시간 범위 동안의 모든 트윗을 반환합니다.

7. **새 단계**를 선택하고 **작업 추가** 또는 **조건 추가**를 선택합니다.

    **동작 추가**를 선택하면 [사용 가능한 커넥터](../connectors/apis-list.md)를 검색하여 작업을 선택할 수 있습니다. 

8. 검색 상자에서 **outlook**을 검색하고 **전자 메일 보내기**를 선택하여 Outlook 계정에서 지정된 전자 메일 주소로 전자 메일을 보냅니다.

   ![작업](media/logic-apps-create-a-logic-app/actions.png)

9. 이제 원하는 전자 메일의 매개 변수를 입력해야 합니다.

   ![매개 변수](media/logic-apps-create-a-logic-app/parameters.png)

10. 마지막으로 **저장** 을 선택하여 논리 앱을 라이브 상태로 만듭니다.

## <a name="manage-your-logic-app-after-creation"></a>논리 앱 생성 후 관리

이제 논리 앱이 실행됩니다. 정기적으로 입력한 검색 용어를 포함한 트윗을 확인합니다. 일치하는 트윗을 찾으면 전자 메일을 전송합니다. 마지막으로, 앱을 비활성화하는 방법 또는 그 작동 방법을 살펴보겠습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

2. 왼쪽 메뉴에서 **추가 서비스**를 클릭합니다. **엔터프라이즈 통합** 아래에서 **논리 앱**을 선택합니다. 논리 앱을 선택합니다.

    *    앱의 상태, 실행 기록 및 일반 정보를 보려면 논리 앱 메뉴에서 **개요**를 선택합니다. 예상하는 데이터를 찾을 수 없는 경우 명령 모음에서 **새로 고침**을 선택합니다.

    *    앱을 편집하려면 논리 앱 메뉴에서 **Logic App Designer**를 선택합니다.

    *    앱을 일시적으로 사용 해제하려면 논리 앱 메뉴에서 **개요**를 선택합니다. 명령 모음에서 **사용 안 함**을 선택합니다.

    *    앱을 삭제하려면 논리 앱 메뉴에서 **개요**를 선택합니다. 
    명령 모음에서 **삭제**를 선택합니다. 논리 앱의 이름을 입력하고 **삭제**를 선택합니다.

5분 이내에 클라우드에서 간단한 논리 앱이 실행되도록 설정할 수 있습니다. 논리 앱 기능을 사용하는 방법에 대한 자세한 내용은 [논리 앱 기능 사용]을 참조하세요. 논리 앱 정의 자체에 대해 알아보려면 [논리 응용 프로그램 정의 작성](../logic-apps/logic-apps-author-definitions.md)을 참조하세요.

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[논리 앱 기능 사용]: logic-apps-create-a-logic-app.md
