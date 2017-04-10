---
title: "클라우드 앱과 클라우드 서비스 간에 첫 번째 워크플로 만들기 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps에서 워크플로를 만들고 실행하여 시스템 통합 및 EAI(Enterprise Application Integration) 시나리오에 대한 비즈니스 프로세스 자동화"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "워크플로, 클라우드 앱, 클라우드 서비스, 비즈니스 프로세스, 시스템 통합, Enterprise Application Integration, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: jehollan; estfan; LADocs
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6634f0ee88e68f2fcb09fd7534a88677e8efa029
ms.lasthandoff: 04/03/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>클라우드 앱과 클라우드 서비스 간에 프로세스를 자동화하는 첫 번째 논리 앱 워크플로 만들기

[Azure Logic Apps](logic-apps-what-are-logic-apps.md)를 사용하여 워크플로를 만들고 실행하는 경우 코드를 작성하지 않고도 비즈니스 프로세스를 쉽고 신속하게 자동화할 수 있습니다. 이 첫 번째 예제에서는 웹 사이트에서 새 콘텐츠에 대한 RSS 피드를 확인하는 기본 논리 앱 워크플로를 만드는 방법을 보여 줍니다. 웹 사이트의 피드에서 새 항목이 표시되면 논리 앱은 Outlook 또는 Gmail 계정에서 전자 메일을 보냅니다.

논리 앱을 만들고 실행하려면 다음이 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 그렇지 않으면 [종량제 구독에 등록](https://azure.microsoft.com/pricing/purchase-options/)할 수 있습니다.

  Azure 구독은 논리 앱 사용 요금을 청구하는 데 사용됩니다. Azure Logic Apps에서 [사용 계량](../logic-apps/logic-apps-pricing.md) 및 [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)의 작동 방식에 대해 알아봅니다.

또한 이 예제에는 다음과 같은 항목이 필요합니다.

* Outlook.com, Office 365 Outlook 또는 Gmail 계정

    > [!TIP]
    > 개인 [Microsoft 계정](https://account.microsoft.com/account)이 있는 경우 Outlook.com 계정이 있습니다. 그렇지 않은 경우 Azure 직장 또는 학교 계정이 있으면 **Office 365 Outlook** 계정이 있습니다.

* 웹 사이트의 RSS 피드에 대한 링크입니다. 이 예제는 [MSDN Channel 9 웹 사이트](https://channel9.msdn.com/)에 RSS 피드를 사용합니다.`https://s.ch9.ms/Feeds/RSS`

## <a name="add-a-trigger-that-starts-your-workflow"></a>워크플로를 시작하는 트리거 추가

[*트리거*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)는 논리 앱 워크플로를 시작하는 이벤트이며 논리 앱에 필요한 첫 번째 항목입니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다.

2. 다음과 같이 왼쪽 메뉴에서 **새로 만들기** > **엔터프라이즈 통합** > **Logic App**을 선택합니다.

     ![Azure Portal, New, 엔터프라이즈 통합, Logic App](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > 또한 **새로 만들기**를 선택하고 검색 상자에서 `logic app`을 입력한 다음 Enter 키를 누릅니다. 그런 다음 **Logic App** > **만들기**를 선택합니다.

3. 논리 앱의 이름을 지정하고 Azure 구독을 선택합니다. 이제 Azure 리소스 그룹을 만들거나 선택합니다. 이를 통해 관련 Azure 리소스를 구성하고 관리할 수 있습니다. 마지막으로 논리 앱을 호스팅할 데이터 센터 위치를 선택합니다. 준비되면 **대시보드에 고정** 및 **만들기**를 차례로 선택합니다.

     ![논리 앱 세부 정보](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > **대시보드에 고정**을 선택하면 논리 앱이 배포 후에 Azure 대시보드에 표시되고 자동으로 열립니다. 논리 앱이 대시보드에 표시되지 않으면 **모든 리소스** 타일에서 **추가 보기**를 선택하고 논리 앱을 선택합니다. 또는 왼쪽 메뉴에서 **추가 서비스**를 선택합니다. **엔터프라이즈 통합** 아래에서 **Logic Apps**을 선택하고 논리 앱을 선택합니다.

4. 논리 앱을 처음으로 열면 Logic App Designer에서는 시작하는 데 사용할 수 있는 템플릿을 보여 줍니다. 이제 **빈 Logic App**을 선택하여 처음부터 논리 앱을 빌드할 수 있습니다.

    Logic App Designer가 열리고 논리 앱에서 사용할 수 있는 서비스 및 *트리거*를 표시합니다.

5. 검색 상자에 `RSS`를 입력하고 다음 트리거를 선택합니다. **RSS - 피드 항목이 게시되는 경우** 

    ![RSS 트리거](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. 추적하려는 웹 사이트의 RSS 피드에 대한 링크를 입력합니다. 

     **주파수** 및 **간격**을 변경할 수도 있습니다. 
     이러한 설정은 논리 앱이 새 항목을 확인하는 빈도를 결정하고 시간 범위 동안 찾은 모든 항목을 반환합니다.

     이 예제에서는 MSDN Channel 9 웹 사이트에 게시된 새 항목을 매일 확인해 보겠습니다.

     ![RSS 피드, 빈도 및 간격을 사용하여 트리거 설정](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. 이제 작업을 저장합니다. (디자이너 명령 모음에서 **저장**을 선택합니다.)

   ![논리 앱 저장](media/logic-apps-create-a-logic-app/save-logic-app.png)

   논리 앱을 저장하면 실시간으로 전송되지만 현재 논리 앱은 지정된 RSS 피드에서 새 항목을 확인하기만 합니다. 
   이 예제를 더 유용하게 사용하기 위해 트리거가 발생한 후에 논리 앱이 수행하는 작업을 추가합니다.

## <a name="add-an-action-that-responds-to-your-trigger"></a>트리거에 응답하는 작업 추가

[*작업*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)은 논리 앱 워크플로에서 수행하는 태스크입니다. 논리 앱에 트리거를 추가한 후에 해당 트리거에 의해 생성된 데이터를 사용하여 작업을 수행하는 작업을 추가할 수 있습니다. 예를 들어 이제 웹 사이트의 RSS 피드에 새 항목이 표시되면 전자 메일을 보내는 작업을 추가합니다.

1. 디자이너의 트리거 아래에서 **새 단계** > **작업 추가**를 다음과 같이 선택합니다.

   ![작업 추가](media/logic-apps-create-a-logic-app/add-new-action.png)

   디자이너에서는 [사용 가능한 커넥터](../connectors/apis-list.md)를 표시하여 트리거가 발생할 때 수행할 작업을 선택할 수 있습니다.

2. 전자 메일 계정에 따라 Outlook 또는 Gmail에 대한 단계를 따릅니다.

   * Outlook 계정에서 전자 메일을 보내려면 검색 상자에서 `outlook`을 입력합니다. **서비스** 아래에서 개인 Microsoft 계정에 **Outlook.com**을 선택하거나 Azure 회사 또는 학교 계정에 **Office 365 Outlook**을 선택합니다. 
   **작업** 아래에서 **전자 메일 보내기**를 선택합니다.

       ![Outlook "전자 메일 보내기" 작업 선택](media/logic-apps-create-a-logic-app/actions.png)

   * Gmail 계정에서 전자 메일을 보내려면 검색 상자에서 `gmail`을 입력합니다. 
   **작업** 아래에서 **전자 메일 보내기**를 선택합니다.

       !["Gmail - 전자 메일 보내기" 선택](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. 자격 증명을 묻는 메시지가 표시되는 경우 전자 메일 계정의 사용자 이름 및 암호를 사용하여 로그인합니다. 

4. 예를 들어 대상 전자 메일 주소와 간이 이 작업에 대한 세부 정보를 제공하고 전자 메일에 포함할 데이터의 매개 변수를 선택합니다.

   ![전자 메일에 포함할 데이터 선택](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    따라서 Outlook을 선택한 경우 논리 앱은 다음 예제와 같습니다.

    ![완료된 논리 앱](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    변경 내용을 저장합니다. (디자이너 명령 모음에서 **저장**을 선택합니다.)

6. 이제 테스트하기 위해 논리 앱을 수동으로 실행할 수 있습니다. 디자이너 명령 모음에서 **실행**을 선택합니다. 그렇지 않은 경우 논리 앱에서 설정한 일정에 따라 지정된 RSS 피드를 확인하도록 할 수 있습니다.

   논리 앱이 새 항목을 찾으면 선택한 데이터를 포함하는 전자 메일을 보냅니다. 
   새 항목이 발견되면 논리 앱이 전자 메일을 보내는 작업을 건너뜁니다.

7. 논리 앱의 실행 및 트리거 기록을 모니터링하고 확인하려면 **개요**를 선택합니다.

   ![논리 앱 실행 및 트리거 기록 모니터링 및 보기](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 예상하는 데이터를 찾을 수 없는 경우 명령 모음에서 **새로 고침**을 선택합니다.

   논리 앱의 상태 또는 실행 및 트리거 기록에 대한 자세한 내용을 알아보거나 논리 앱을 진단하려면 [논리 앱 문제 해결](logic-apps-diagnosing-failures.md)을 참조하세요.

      > [!NOTE]
      > 논리 앱은 앱을 해제할 때까지 계속 실행합니다. 지금 앱을 해제하려면 논리 앱 메뉴에서 **개요**를 선택합니다. 명령 모음에서 **사용 안 함**을 선택합니다.

축하드립니다. 첫 번째 기본 논리 앱을 설정하고 실행했습니다. 또한 코드 없이 프로세스를 자동화하는 워크플로를 쉽게 만들고 클라우드 앱 및 클라우드 서비스를 통합하는 방법에 대해 알아보았습니다.

## <a name="manage-your-logic-app"></a>논리 앱 관리

앱을 관리하기 위해 상태 검사, 편집, 기록 보기, 해제 또는 논리 앱 삭제와 같은 태스크를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다.

2. 왼쪽 메뉴에서 **추가 서비스**를 선택합니다. **엔터프라이즈 통합** 아래에서 **논리 앱**을 선택합니다. 논리 앱을 선택합니다. 

   논리 앱 메뉴에서 다음과 같은 논리 앱 관리 태스크를 찾을 수 있습니다.

   |작업|단계| 
   |:---|:---| 
   | 앱의 상태, 실행 기록 및 일반 정보 보기| **개요**를 선택합니다.| 
   | 앱 편집 | **Logic App Designer**를 선택합니다. | 
   | 앱의 워크플로 JSON 정의 보기 | **Logic App 코드 보기**를 선택합니다. | 
   | 논리 앱에 수행된 작업 보기 | **활동 로그**를 선택합니다. | 
   | 논리 앱의 이전 버전 보기 | **버전**을 선택합니다. | 
   | 일시적으로 앱 해제 | **개요**를 선택한 다음 명령 모음에서 **사용 안 함**을 선택합니다. | 
   | 앱 삭제 | **개요**를 선택한 다음 명령 모음에서 **삭제**를 선택합니다. 논리 앱의 이름을 입력하고 **삭제**를 선택합니다. | 

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 알아보려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

## <a name="next-steps"></a>다음 단계

*  [조건 추가 및 워크플로 실행](../logic-apps/logic-apps-use-logic-app-features.md)
*     [논리 앱 템플릿](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Azure Resource Manager 템플릿에서 논리 앱 만들기](../logic-apps/logic-apps-arm-provision.md)

