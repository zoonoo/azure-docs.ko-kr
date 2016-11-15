---
title: "Logic App 만들기 | Microsoft Docs"
description: "SaaS 서비스에 연결하는 논리 앱을 만드는 방법에 대해 알아보세요."
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ab26637d0f49abd73b55e3ccf9ace0f33f6f8731


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>SaaS 서비스에 연결하는 새 논리 앱 만들기
이 항목에서는 단 몇 분 안에 [Azure Logic Apps](app-service-logic-what-are-logic-apps.md)를 시작할 수 있는 방법을 보여 줍니다. 전자 메일에 흥미로운 트윗을 보낼 수 있는 간단한 워크플로를 안내합니다.

이 시나리오를 사용하려면 다음이 필요합니다.

* Azure 구독
* Twitter 계정
* Outlook.com 또는 호스팅된 Office 365 사서함

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>트윗으로 전자 메일을 보낼 새 논리 앱 만들기
1. [Azure 포털 대시보드](https://portal.azure.com)에서 **새로 만들기**를 선택합니다. 
2. 검색 표시줄에서 '논리 앱'을 검색한 다음 **논리 앱**을 선택합니다. 또한 **새로 만들기**, **웹 + 모바일**을 선택하고 **논리 앱**을 선택할 수도 있습니다. 
3. 논리 앱의 이름을 입력하고 위치, 리소스 그룹을 선택한 다음 **만들기**를 선택합니다.  **대시보드에 고정** 을 선택하는 경우 논리 앱이 배포된 후에 자동으로 열립니다.  
4. 논리 앱을 처음으로 연 후에 시작하려는 템플릿에서 선택할 수 있습니다.  지금은 **빈 논리 앱** 을 클릭하여 처음부터 구축합니다. 
5. 만들어야 하는 첫 번째 항목은 트리거입니다.  논리 앱을 시작하는 이벤트입니다.  트리거 검색 상자에서 **twitter** 를 검색하고 선택합니다.
6. 이제는 트리거할 검색 단어에 입력할 수 있습니다.  **주파수** 및 **간격**은 논리 앱이 새 트윗을 확인(및 시간 범위 동안 모든 트윗을 반환)하는 빈도를 결정합니다.
    ![Twitter 검색](./media/app-service-logic-create-a-logic-app/twittersearch.png)
7. **새 단계** 단추를 선택한 다음 **작업 추가** 또는 **조건 추가**를 선택합니다.
8. **동작 추가**를 선택하면 [사용 가능한 커넥터](../connectors/apis-list.md)에서 검색하여 작업을 선택할 수 있습니다. 예를 들어 **Outlook.com - 전자 메일 보내기**를 선택하여 outlook.com 주소에서 메일을 보낼 수 있습니다.  
    ![actions](./media/app-service-logic-create-a-logic-app/actions.png)
9. 이제 원하는 전자 메일에 대한 매개 변수를 입력해야 합니다.  ![매개 변수](./media/app-service-logic-create-a-logic-app/parameters.png)
10. 마지막으로 **저장** 을 선택하여 논리 앱을 라이브 상태로 만듭니다.

## <a name="manage-your-logic-app-after-creation"></a>논리 앱 생성 후 관리
이제 논리 앱이 실행됩니다. 정기적으로 입력한 검색 용어를 포함한 트윗을 확인합니다. 일치하는 트윗을 찾으면 전자 메일을 전송합니다. 마지막으로, 앱을 비활성화하는 방법 또는 그 작동 방법을 살펴보겠습니다.

1.  [Azure 포털](https://portal.azure.com)
2. 화면의 왼쪽에서 **찾아보기**를 클릭하고 **Logic Apps**을 선택합니다.
3. 방금 만든 새 논리 앱을 클릭하여 현재 상태 및 일반 정보를 표시합니다.
4. 새 논리 앱을 편집하려면 **편집**을 클릭합니다.
5. 앱을 끄려면 명령 모음에서 **사용 안 함** 을 클릭합니다.
6. 논리 앱이 실행되는 경우 모니터링할 실행 및 트리거 기록을 봅니다.  최신 데이터를 확인하려면 **새로 고침** 을 클릭하면 됩니다.

5분 이내에 클라우드에서 간단한 논리 앱이 실행되도록 설정할 수 있습니다. 논리 앱 기능을 사용하는 방법에 대한 자세한 내용은 [논리 앱 기능 사용]을 참조하세요. 논리 앱 정의 자체에 대해 알아보려면 [논리 응용 프로그램 정의 작성](app-service-logic-author-definitions.md)을 참조하세요.

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[논리 앱 기능 사용]: app-service-logic-create-a-logic-app.md



<!--HONumber=Nov16_HO2-->


