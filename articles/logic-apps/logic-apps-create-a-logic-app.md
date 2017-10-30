---
title: "시스템과 클라우드 서비스 간에 워크플로 자동화 - Azure Logic Apps | Microsoft Docs"
description: "논리 앱을 만들어서 시스템 통합 및 EAI(Enterprise Application Integration) 시나리오에 대한 워크플로 자동화"
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
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>논리 앱에서 데이터를 처리하도록 첫 번째 워크플로 자동화

해당 조직에서 보다 신속하게 시스템 및 서비스를 통합하려면 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)에서 워크플로 및 비즈니스 프로세스를 자동화할 수 있습니다. 이 빠른 시작에서는 논리 앱을 만들어서 자동화된 워크플로를 쉽게 빌드하고 실행할 수 있습니다. 예제 앱에서는 새 항목의 웹 사이트 RSS 피드를 확인하고 각 항목에 전자 메일을 보내는 워크플로를 자동화하는 방법을 보여줍니다.

이 샘플 논리 앱은 이 예제와 같이 전자 메일을 보냅니다.

![새로운 RSS 피드 항목에 대해 전송된 전자 메일](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

또한 빌드하는 높은 수준의 논리 앱 워크플로는 다음과 같습니다.

![개요 - 논리 앱 예제](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 빈 논리 앱을 만듭니다.
> * 새 항목이 RSS 피드에 표시되면 워크플로를 시작하도록 트리거를 추가합니다.
> * RSS 피드 항목에 대한 세부 정보를 포함하는 전자 메일을 보내는 작업을 추가합니다.
> * 논리 앱 워크플로 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정에 등록합니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>필수 조건

* 알림 전송을 위한 Azure Logic Apps에서 지원하는 전자 메일 공급자의 전자 메일 계정 예를 들어 Office 365 Outlook, Outlook.com 또는 Gmail을 사용할 수 있습니다. 지원되는 전자 메일 커넥터는 [커넥터 목록을 검토하세요](https://docs.microsoft.com/connectors/). 이 빠른 시작에서는 Office 365 Outlook을 사용합니다.

  > [!TIP]
  > 개인 [Microsoft 계정](https://account.microsoft.com/account)이 있는 경우 Outlook.com 계정이 있습니다. 그렇지 않은 경우 Azure 직장 또는 학교 계정이 있으면 Office 365 Outlook 계정이 있는 것입니다.

* 웹 사이트의 RSS 피드에 대한 링크입니다. 이 예제에서는 [Reuters 웹 사이트의 주요 기사에 대한 RSS 피드](http://feeds.reuters.com/reuters/topNews): `http://feeds.reuters.com/reuters/topNews`를 사용합니다.

이 빠른 시작에서는 코드를 작성하지 않아도 되지만 Logic Apps에서는 코드를 사용하는 다른 시나리오(예: [Azure Functions](../azure-functions/functions-overview.md)에서 논리 앱의 고유한 코드 실행)를 지원합니다.

## <a name="create-a-blank-logic-app"></a>빈 논리 앱 만들기 

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다. 

2. Azure 주 메뉴에서 **새로 만들기** > **엔터프라이즈 통합** > **논리 앱**을 선택합니다.

   ![Azure Portal, New, 엔터프라이즈 통합, Logic App](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 이 이미지의 테이블에서 설정을 사용하여 논리 앱을 만듭니다.

   ![논리 앱 세부 정보 제공](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *your-logic-app-name* | 고유한 논리 앱 이름을 제공합니다. | 
   | **구독** | *your-Azure-subscription-name* | 사용할 Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *your-Azure-resource-group-name* | 이 논리 앱에 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만들고 이 앱과 연결된 모든 리소스를 구성합니다. | 
   | **위치**: | *your-Azure-datacenter-region* | 논리 앱을 배포하기 위한 데이터 센터 지역을 선택합니다(예: 미국 서부). | 
   | **Log Analytics** | 꺼짐 | 논리 앱에서는 진단 로깅을 켜지만 이 빠른 시작에서는 **끄기** 설정을 유지합니다. | 
   |||| 

4. 준비가 되면 **대시보드에 고정**을 선택합니다. 이런 방식으로 논리 앱은 자동으로 Azure 대시보드에 표시되고 배포 후에 열립니다. **만들기**를 선택합니다.

   > [!NOTE]
   > 논리 앱을 고정하지 않으려면 계속할 수 있도록 배포 후에 논리 앱을 수동으로 찾고 열어야 합니다.

   Azure가 논리 앱을 배포한 후에 Logic Apps 디자이너가 열리고 소개 비디오가 포함된 페이지를 보여줍니다. 
   비디오 아래에서 공통 논리 앱 패턴의 템플릿을 찾을 수 있습니다. 
   이 빠른 시작은 논리 앱을 처음부터 빌드합니다. 

5. 소개 비디오 및 공통 트리거는 넘어갑니다. **템플릿** 아래에서 **빈 Logic App**을 선택합니다.

   ![빈 논리 앱 템플릿을 선택합니다.](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps 디자이너에서는 사용할 수 있는 커넥터 및 해당 트리거가 표시됩니다. 이 기능은 논리 앱 워크플로를 시작하는 데 사용됩니다.

   ![논리 앱 트리거](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>새 항목을 검색하는 트리거를 추가합니다.

모든 논리 앱 워크플로는 [트리거](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)로 시작합니다. 트리거는 특정 이벤트가 발생하는 경우 또는 사용자가 설정한 조건을 새 데이터가 충족하는 경우 발생합니다. 트리거가 발생될 때마다 Logic Apps 엔진은 워크플로를 시작하고 실행하는 논리 앱 인스턴스를 만듭니다.

1. 검색 상자에서 필터로 "rss"를 입력합니다. **RSS - 피드 항목이 게시되는 경우** 트리거를 선택합니다. 

   ![“RSS - 피드 항목이 게시되는 경우” 트리거를 선택합니다.](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 모니터링하려는 RSS 피드에 대한 링크를 입력합니다(예: `http://feeds.reuters.com/reuters/topNews`). 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 5분마다 피드를 확인합니다.

   ![RSS 피드, 빈도 및 간격을 사용하여 트리거 설정](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Logic Apps는 RSS 피드에 대한 연결을 만듭니다.

   > [!TIP]
   > 디자이너에서 보기를 단순화하기 위해 셰이프의 세부 정보를 축소하거나 숨길 수 있습니다. 셰이프의 제목 표시줄 내부를 클릭하기만 하면 됩니다.

3. 작업을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다. 

   ![논리 앱 저장](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   이제 논리 앱이 라이브 상태이지만 RSS 피드를 확인하는 것 이외에 아무 것도 수행하지 않습니다. 따라서 트리거가 실행되는 경우 응답하는 작업을 추가해 보겠습니다.

## <a name="add-an-action-to-send-email"></a>전자 메일을 보내는 작업을 추가합니다.

이제 트리거가 생성되었으므로 RSS 피드에 새 항목이 표시될 때 전자 메일을 보내는 [작업](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)을 추가합니다. 워크플로는 트리거가 실행된 후에 이 작업을 수행합니다.

1. Logic Apps 디자이너에서 트리거에 있는 **+ 새 단계** > **작업 추가**를 선택합니다.

   ![작업 추가](./media/logic-apps-create-a-logic-app/add-new-action.png)

   디자이너에서는 트리거가 실행될 때 논리 앱이 수행할 수 있는 작업을 보여줍니다.

   ![작업 목록에서 선택](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 검색 상자에서 필터로 “전자 메일 보내기”를 입력합니다. 사용하려는 전자 메일 커넥터를 찾고 선택합니다. 그런 다음 해당 커넥터에 대한 "전자 메일 보내기" 작업을 선택합니다. 예: 

   * Azure 회사 또는 학교 계정에서 Office 365 Outlook을 선택합니다. 
   * Microsoft 개인 계정에서 Outlook.com을 선택합니다. 
   * Gmail 계정에서 Gmail을 선택합니다. 

   이 빠른 시작에서는 Office 365 Outlook을 사용합니다. 
   다른 전자 메일 공급자를 사용하는 경우 단계는 동일하지만 UI의 표시가 다를 수 있습니다. 

   ![“Office 365 Outlook - 전자 메일 보내기” 작업을 선택합니다.](./media/logic-apps-create-a-logic-app/actions.png)

3. 자격 증명을 묻는 메시지가 표시되는 경우 전자 메일 계정의 사용자 이름 및 암호를 사용하여 로그인합니다. 

   Logic Apps는 전자 메일 계정에 대한 연결을 만듭니다.

4. 이제 전자 메일에 포함하려는 데이터를 지정합니다. 

   1. **받는 사람** 상자에 받는 사람의 이메일 주소를 입력합니다. 
   자신의 이메일 주소를 사용하여 테스트할 수 있습니다.

   2. **주제** 상자에 전자 메일 제목을 입력합니다. 
   이 예에서는 다음과 같이 "새 RSS 항목"을 입력합니다.

      ![전자 메일 제목 입력](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      편집 상자 내부를 클릭할 때 **동적 콘텐츠 목록 추가**가 열리면 사용 가능한 데이터 필드를 선택하여 작업에 포함할 수 있습니다. 
      동적 콘텐츠 목록이 열리지 않으면 해당 편집 상자 아래에서 **동적 콘텐츠 추가**를 선택합니다.

   3. **동적 콘텐츠 추가** 목록에서 **피드 제목**을 선택합니다. 이 항목은 전자 메일에서 항목의 제목을 포함합니다.

      ![전자 메일 제목 입력](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      작업이 완료되면 전자 메일 제목이 다음 예제와 같이 표시됩니다.

      ![추가된 피드 제목](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > **categories-item**과 같이 배열을 포함하는 필드를 선택하게 되면 디자이너에서는 해당 필드를 참조하는 작업 주위에 "For each" 루프를 자동으로 추가합니다. 그렇게 하면 논리 앱이 각 배열 항목에서 해당 작업을 수행할 수 있습니다. 
      > 
      > 루프를 제거하려면 루프의 제목 표시줄에서 줄임표(**...**)를 선택하고 **삭제**를 선택합니다.

   4. **본문** 상자에서 전자 메일의 콘텐츠를 입력합니다. 
   예를 들어 이 텍스트를 입력하고 다음과 같은 필드를 선택합니다.

      ![전자 메일 본문에 콘텐츠 추가](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | 필드 | 설명 | 
      | ----- | ----------- | 
      | **피드 제목** | 항목의 제목을 표시합니다. | 
      | **다음에 게시된 피드** | 항목의 게시 날짜와 시간을 표시합니다. | 
      | **주 피드 링크** | 항목의 URL을 표시합니다. | 
      ||| 

      > [!TIP]
      > 편집 상자에서 빈 줄을 추가하려면 Shift + Enter 키를 누릅니다. 
      
5. 작업을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   ![완료된 논리 앱](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>논리 앱 워크플로 실행

수동으로 논리 앱을 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 그렇지 않은 경우 논리 앱이 설정한 일정에서 실행되도록 대기할 수 있습니다.

![논리 앱 실행](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

RSS 피드에 새 항목이 있는 경우 논리 앱은 새 항목 각각에 전자 메일을 보냅니다. 예를 들어 이 논리 앱이 보내는 샘플 Outlook 전자 메일은 다음과 같습니다.

![새로운 RSS 피드 항목에 대해 전송된 전자 메일](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

피드에 새 항목이 없는 경우 논리 앱은 전자 메일을 보내는 단계를 건너뛰고 다시 검사하기 전의 다음 간격까지 대기합니다. 

> [!TIP]
> 전자 메일을 받지 못한 경우 정크 메일 폴더를 확인합니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. 첫 번째 논리 앱을 만들고 실행했습니다. 이 빠른 시작에서는 시스템 및 서비스를 통합하기 위해 자동화된 워크플로를 쉽고 빠르게 만드는 방법을 보여줍니다.

## <a name="clean-up-resources"></a>리소스 정리

논리 앱은 앱을 끄거나 삭제할 때까지 Azure 구독에서 계속 실행되고 요금을 발생시킵니다. 또한 논리 앱에 연결을 만드는 경우 해당 연결은 논리 앱을 삭제한 후에도 남아 있습니다. 

작업을 완료하면 요금이 부과되거나 유지하지 않으려는 리소스를 사용하지 않도록 설정하거나 삭제해야 합니다. 이 빠른 시작에서 만든 모든 리소스를 삭제하려면 이 논리 앱에서 만든 Azure 리소스 그룹을 삭제합니다. 

### <a name="delete-resource-group"></a>리소스 그룹 삭제

논리 앱에 관련된 모든 항목을 유지하지 않으려면 이 빠른 시작에서 만든 리소스 그룹 및 모든 관련 리소스를 삭제합니다. [Azure 리소스 그룹을 관리하는 방법](../azure-resource-manager/resource-group-portal.md#manage-resources)에 대해 자세히 알아봅니다.

1. Azure 메뉴에서 **리소스 그룹**을 선택합니다.

2. 삭제하려는 리소스 그룹을 선택합니다. 아직 선택하지 않은 경우 리소스 그룹 메뉴에서 **개요**를 선택합니다. 

3. 삭제하려는 그룹에서 모든 리소스를 검토합니다. 준비된 경우 리소스 그룹 도구 모음에서 **리소스 그룹 삭제**를 선택합니다.

### <a name="turn-off-logic-app"></a>논리 앱 해제

작업을 삭제하지 않고 논리 앱의 실행을 중지하려면 앱을 사용하지 않도록 설정합니다. 

논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **사용 안 함**을 선택합니다.

  ![논리 앱 사용 해제](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 논리 앱 메뉴가 표시되지 않으면 Azure 대시보드로 돌아가서 논리 앱을 다시 엽니다.

### <a name="delete-logic-app"></a>논리 앱 삭제

논리 앱만을 삭제하고 만든 연결과 같은 다른 모든 관련된 리소스를 유지할 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **삭제**를 선택합니다. 

   ![논리 앱 삭제](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > 논리 앱 메뉴가 표시되지 않으면 Azure 대시보드로 돌아가서 논리 앱을 다시 엽니다.

2. 논리 앱을 삭제하려 한다고 확인한 후 **삭제**를 선택합니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 빌드된 템플릿에서 논리 앱 워크플로 만들기](../logic-apps/logic-apps-create-logic-apps-from-templates.md)