---
title: "시스템과 클라우드 서비스 간에 첫 번째 자동화된 워크플로 만들기 - Azure Logic Apps | Microsoft Docs"
description: "논리 앱을 만들고 실행하여 시스템 통합 및 EAI(Enterprise Application Integration) 시나리오에 대한 비즈니스 프로세스 및 워크플로 자동화"
author: ecfan
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Azure Portal을 통해 워크플로 및 프로세스를 자동화하기 위한 첫 번째 논리 앱 만들기

코드를 작성하지 않고도 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)를 사용하여 자동화된 워크플로를 빌드하여 실행하면 시스템 및 서비스를 통합할 수 있습니다. 워크플로를 사용하여 작업을 손쉽게 자동화할 수 있는 방법을 보여 주기 위해 이 자습서에서는 웹 사이트에서 새 콘텐츠에 대한 RSS 피드를 확인하고 피드의 새로운 각 항목에 대해 전자 메일을 보내는 기본 논리 앱을 만듭니다. 

![개요 - 첫 번째 논리 앱 예제](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 빈 논리 앱을 만듭니다.
> * RSS 피드 항목이 게시된 경우 논리 앱을 시작하는 트리거를 추가합니다.
> * RSS 피드 항목에 대한 세부 정보와 함께 전자 메일을 보내는 작업을 추가합니다.
> * 논리 앱을 실행하고 테스트합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 그렇지 않으면 [종량제 구독에 등록](https://azure.microsoft.com/pricing/purchase-options/)할 수 있습니다.

* 알림 전송을 위한 [Azure Logic Apps에서 지원하는 전자 메일 공급자](../connectors/apis-list.md)의 전자 메일 계정. 예를 들어 Office 365 Outlook, Outlook.com, Gmail 또는 다른 지원되는 공급자를 사용할 수 있습니다. 이 자습서에서는 Office 365 Outlook을 사용합니다.

  > [!TIP]
  > 개인 [Microsoft 계정](https://account.microsoft.com/account)이 있는 경우 Outlook.com 계정이 있습니다. 그렇지 않은 경우 Azure 직장 또는 학교 계정이 있으면 Office 365 Outlook 계정이 있는 것입니다.

* 웹 사이트의 RSS 피드에 대한 링크입니다. 이 예제에서는 [CNN.com 웹 사이트의 주요 기사에 대한 RSS 피드](http://rss.cnn.com/rss/cnn_topstories.rss)(`http://rss.cnn.com/rss/cnn_topstories.rss`)를 사용합니다.

## <a name="1-create-a-blank-logic-app"></a>1. 빈 논리 앱 만들기 

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다.

2. Azure 주 메뉴에서 **새로 만들기** > **엔터프라이즈 통합** > **논리 앱**을 선택합니다.

   ![Azure Portal, New, 엔터프라이즈 통합, Logic App](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 테이블에 지정된 설정을 사용하여 논리 앱을 만듭니다.

   ![논리 앱 세부 정보 제공](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *your-logic-app-name* | 고유한 논리 앱 이름을 제공합니다. | 
   | **구독** | *your-Azure-subscription* | 사용할 Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *your-Azure-resource-group* | Azure 리소스 그룹을 만들거나 선택합니다. 이를 통해 관련 Azure 리소스를 구성하고 관리할 수 있습니다. | 
   | **위치**: | *your-Azure-region* | 논리 앱을 배포하기 위한 데이터 센터 지역을 선택합니다. | 
   |||| 

4. 준비가 되면 **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

   이제 논리 앱에 대한 Azure 리소스를 만들었습니다. 
   Azure에서 논리 앱을 배포하면 Logic Apps 디자이너에서 일반적인 패턴에 대한 템플릿을 표시하므로 더 빨리 시작할 수 있습니다.

   > [!NOTE] 
   > **대시보드에 고정**을 선택하면 논리 앱이 배포 후에 Azure 대시보드에 표시되고 Logic Apps 디자이너에서 자동으로 열립니다. 그렇지 않으면 논리 앱을 수동으로 찾아 열 수 있습니다.

5. 이제 **템플릿** 아래에서 **비어 있는 논리 앱**을 선택하면 논리 앱을 처음부터 빌드할 수 있습니다.

   ![논리 앱 템플릿 선택](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps 디자이너에서는 사용할 수 있는 [*커넥터*](../connectors/apis-list.md) 및 해당 [*트리거*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)가 표시됩니다. 이는 논리 앱 워크플로를 시작하는 데 사용합니다.

   ![논리 앱 트리거](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. 워크플로 시작을 위한 트리거 추가

모든 논리 앱은 [*트리거*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)로 시작해야 합니다. 트리거는 특정 이벤트가 발생하는 경우 또는 사용자가 설정한 조건을 새 데이터가 충족하는 경우 발생합니다. Logic Apps 엔진은 워크플로를 실행하기 위해 논리 앱 인스턴스를 만듭니다. 트리거가 발생할 때마다 엔진은 논리 앱 워크플로를 실행하는 다른 별도의 인스턴스를 만듭니다.

1. 검색 상자에서 필터로 “rss”를 입력합니다. **RSS - 피드 항목이 게시되는 경우** 트리거를 선택합니다. 

   ![“RSS - 피드 항목이 게시되는 경우” 트리거를 선택합니다.](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 추적하려는 웹 사이트의 RSS 피드에 대한 링크를 입력합니다(예: `http://rss.cnn.com/rss/cnn_topstories.rss`). 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매일 피드를 확인하도록 이러한 속성을 설정합니다. 

   ![RSS 피드, 빈도 및 간격을 사용하여 트리거 설정](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. 이제 작업을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.
트리거의 세부 정보를 축소하고 숨기려면 트리거의 제목 표시줄을 선택합니다.

   ![논리 앱 저장](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   논리 앱은 현재 라이브 상태이지만 워크플로에 작업을 추가하기 전까지는 RSS 피드의 새 항목을 검사하는 것 외에 다른 작업은 수행하지 않습니다. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. 트리거에 응답하는 작업 추가

이제 [*작업*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)을 추가합니다. 논리 앱 워크플로를 수행하는 작업입니다. 이 예제에서는 RSS 피드에 새 항목이 표시될 때 전자 메일을 보내는 작업을 추가합니다.

1. Logic Apps 디자이너에서 트리거에 있는 **+ 새 단계** > **작업 추가**를 선택합니다.

   ![작업 추가](./media/logic-apps-create-a-logic-app/add-new-action.png)

   디자이너에서는 [사용 가능한 커넥터](../connectors/apis-list.md)를 표시하여 트리거가 발생할 때 수행할 작업을 선택할 수 있습니다.

   ![작업 목록에서 선택](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 검색 상자에서 필터로 “전자 메일 보내기”를 입력합니다. 전자 메일 공급자에 따라 일치하는 커넥터를 찾아 선택합니다. 그런 다음 커넥터에 대한 "전자 메일 보내기" 작업을 선택합니다. 예: 

   * 예를 들어 Azure 회사 또는 학교 계정의 경우 Office 365 Outlook 커넥터를 선택합니다. 
   * Microsoft 개인 계정의 경우 Outlook.com 커넥터를 선택합니다. 
   * Gmail 계정의 경우 Gmail 커넥터를 선택합니다. 

   Office 365 Outlook 커넥터를 계속 사용합니다. 
   다른 공급자를 사용하는 경우 단계는 동일하지만 UI의 표시가 다를 수 있습니다. 

   ![“Office 365 Outlook - 전자 메일 보내기” 작업을 선택합니다.](./media/logic-apps-create-a-logic-app/actions.png)

3. 자격 증명을 묻는 메시지가 표시되는 경우 전자 메일 계정의 사용자 이름 및 암호를 사용하여 로그인합니다. 

4. 테이블에 지정된 세부 정보를 제공하고 메일에 포함할 필드를 선택합니다.

   | 받는 사람 | 단계 | 
   | -- | ----- | 
   | 워크플로에 사용할 수 있는 필드를 선택합니다. | **동적 콘텐츠** 목록이 열리는 편집 상자를 클릭하거나 **동적 콘텐츠 추가**를 선택합니다. | 
   | 사용 가능한 다른 필드를 봅니다. | **동적 콘텐츠** 목록에서 각 섹션에 대한 **자세히 보기**를 선택합니다.  | 
   | 편집 상자에 빈 줄을 추가합니다. | <Shift + Enter> 키를 누릅니다. | 
   | **동적 콘텐츠** 목록을 닫습니다. | **동적 콘텐츠 추가**를 다시 선택합니다. | 
   ||| 

   ![전자 메일에 포함할 데이터 선택](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **To** | *recipient-email-address* | 수신자의 이메일 주소를 입력합니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. | 
   | **제목** | 새 CNN 게시물: **피드 제목** | 전자 메일의 제목에 콘텐츠를 입력합니다. <p>이 자습서의 경우 제안된 텍스트를 입력하고 피드 항목의 제목을 표시하는 트리거의 **피드 제목** 필드를 선택합니다. | 
   | **본문** | 제목: **피드 제목** <p>게시 날짜: **피드 기본 링크** <p>링크: **주 피드 링크** | 전자 메일의 본문에 콘텐츠를 입력합니다. <p>이 자습서의 경우 제안된 텍스트를 입력한 후, 다음과 같은 트리거 필드를 선택합니다. <p>- **피드 제목**, 피드 항목의 제목을 다시 표시 </br>- **피드 게시 날짜**, 항목의 게시 날짜 및 시간을 표시 </br>- **기본 피드 링크**, 피드 항목에 대한 URL 표시 | 
   |||| 

   > [!NOTE] 
   > 배열을 저장하는 필드를 선택하면 디자이너에서 배열을 참조하는 작업 주위에 “For each” 루프를 자동으로 추가합니다. 그렇게 하면 논리 앱에서 각 배열 항목에 대한 해당 작업을 수행합니다.

5. 완료되면 변경 사항을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   ![완료된 논리 앱](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   이제 논리 앱을 테스트하려면 다음 섹션으로 이동하세요.

## <a name="4-run-and-test-your-workflow"></a>4. 워크플로 실행 및 테스트

1. 테스트를 위해 수동으로 논리 앱을 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 그렇지 않은 경우 사용자가 설정한 일정에 따라 논리 앱이 지정된 RSS 피드를 확인하도록 할 수 있습니다.

   ![논리 앱 실행](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   논리 앱이 새 항목을 찾으면 논리 앱은 사용자가 선택한 데이터를 포함하는 전자 메일을 보냅니다.

   ![새로운 RSS 피드 항목에 대해 전송된 전자 메일](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   논리 앱이 새 항목을 찾지 못한 경우 논리 앱은 전자 메일을 보내는 작업을 건너뛰고 다시 검사하기 전 다음 간격까지 대기합니다. 

2. 논리 앱의 실행 및 트리거 기록을 검토하려면 논리 앱 메뉴에서 **개요**를 선택합니다.
실행에 대한 자세한 세부 정보를 보려면 해당 실행에 대한 행을 선택합니다.

   ![논리 앱 실행 및 트리거 기록 모니터링 및 보기](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 예상하는 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침**을 선택합니다.

   실행의 성공 여부에 따라 실행 세부 정보 보기에 성공 또는 실패한 단계가 표시됩니다. 

   ![논리 앱 실행에 대한 세부 정보 보기](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   논리 앱의 상태, 실행 기록 및 트리거 기록에 대한 자세한 내용을 알아보거나 논리 앱을 진단하려면 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

3. 각 단계에 대한 입력 및 출력을 보려면 검토하려는 단계를 확장합니다. 이 정보는 논리 앱의 문제를 진단하고 디버깅하는 데 유용합니다. 예:

   ![단계 세부 정보 보기](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   자세한 내용은 [논리 앱 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)을 참조하세요.

축하드립니다. 첫 번째 기본 논리 앱을 만들고 실행했습니다. 이 예제는 코드 없이 시스템 및 서비스를 통합하는 프로세스를 자동화하는 워크플로를 쉽게 만드는 방법을 보여 줍니다.

> [!NOTE]
> 논리 앱은 앱을 해제할 때까지 계속 실행합니다. 앱을 일시적으로 끄려면 다음 섹션으로 계속 진행합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 리소스를 사용하고 Azure 구독에 요금을 부과하는 작업을 수행합니다. 따라서 이 자습서를 완료하고 테스트할 때 요금이 부과되지 않도록 리소스 사용을 해제하거나 삭제해야 합니다.

앱을 삭제하지 않고도 논리 앱을 중지하여 실행되거나 전자 메일을 보내지 않도록 할 수 있습니다. 논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **사용 안 함**을 선택합니다.

![논리 앱 사용 해제](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>FAQ

**Q:** 내 논리 앱으로 무엇을 수행할 수 있나요? </br>
**A:** 예를 들어, 편집, JSON 정의 보기, 활동 로그 검토 또는 논리 앱 삭제와 같은 다른 작업을 수행할 수 있습니다.

다른 논리 앱 관리 작업을 찾으려면 논리 앱 메뉴에서 다음과 같은 명령을 검토합니다.

| 작업 | 단계 | 
| ---- | ----- | 
| 앱의 상태, 실행 및 트리거 기록 및 일반 정보 보기 | **개요**를 선택합니다. | 
| 앱 편집 | **Logic App Designer**를 선택합니다. | 
| 앱의 워크플로 JSON 정의 보기 | **Logic App 코드 보기**를 선택합니다. | 
| 논리 앱에 수행된 작업 보기 | **활동 로그**를 선택합니다. | 
| 논리 앱의 이전 버전 보기 | **버전**을 선택합니다. | 
| 일시적으로 앱 해제 | **개요**를 선택한 다음 도구 모음에서 **사용 안 함**을 선택합니다. | 
| 앱 삭제 | **개요**를 선택한 다음 도구 모음에서 **삭제**를 선택합니다. 논리 앱의 이름을 입력하고 **삭제**를 선택합니다. | 
||| 

## <a name="get-support"></a>지원 받기

* Azure Logic Apps에 대한 질문은 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.

* Azure Logic Apps 및 커넥터 개선에 참여하시겠습니까? [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하거나 아이디어를 제출해 주세요.

## <a name="next-steps"></a>다음 단계

* [Visual Studio로 논리 앱 만들기](../logic-apps/logic-apps-deploy-from-vs.md)
* [조건 추가 및 워크플로 실행](../logic-apps/logic-apps-use-logic-app-features.md)
*   [논리 앱 템플릿](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Azure Resource Manager 템플릿에서 논리 앱 만들기](../logic-apps/logic-apps-arm-provision.md)
* [Logic Apps 사용량 측정](../logic-apps/logic-apps-pricing.md) 
* [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)
