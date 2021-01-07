---
title: 자동화된 승인 기반 워크플로 빌드
description: 자습서 - Azure Logic Apps를 사용하여 메일 그룹 구독을 처리하는 자동화된 승인 기반 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 102b1946021aff7f8ab5491ed70fbc6cf772e3a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842263"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>자습서: Azure Logic Apps를 사용하여 자동화된 승인 기반 워크플로 만들기

이 자습서에서는 승인 기반 워크플로를 자동화하는 예제 [논리 앱](../logic-apps/logic-apps-overview.md)을 빌드하는 방법을 보여줍니다. 특히 이 예제 논리 앱은 [MailChimp](https://mailchimp.com/) 서비스를 통해 관리되는 메일 그룹에 대한 구독 요청을 처리합니다. 이 논리 앱에는 요청에 대한 이메일 계정을 모니터링하고, 승인을 받기 위해 이러한 요청을 보내고, 요청이 승인되었는지 확인하고, 승인된 구성원을 메일 그룹에 추가하고, 새 구성원이 목록에 추가되었는지 확인하는 다양한 단계가 포함됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 빈 논리 앱을 만듭니다.
> * 구독 요청에 대한 이메일을 모니터링하는 트리거를 추가합니다.
> * 이러한 요청을 승인 또는 거부하는 이메일을 보내는 작업을 추가합니다.
> * 승인 응답을 확인하는 조건을 추가합니다.
> * 승인된 구성원을 메일 그룹에 추가하는 작업을 추가합니다.
> * 이러한 구성원이 목록에 성공적으로 조인되었는지 확인하는 조건을 추가합니다.
> * 이러한 구성원이 목록에 성공적으로 조인되었는지 확인하는 이메일을 보내는 작업을 추가합니다.

여기까지 모두 마치면 논리 앱이 이 워크플로와 비슷하게 보입니다.

![완료된 상위 수준 논리 앱 개요](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 논리 앱이 승인된 구성원의 이메일 주소를 추가할 수 있는 "test-members-ML"이라는 목록을 만든 MailChimp 계정. 계정이 없는 경우 [체험 계정에 가입](https://login.mailchimp.com/signup/)하고 [MailChimp 목록을 만드는 방법](https://us17.admin.mailchimp.com/lists/#)을 알아보세요.

* Logic Apps에서 지원하는 이메일 공급자(예: Office 365 Outlook, Outlook.com 또는 Gmail)의 이메일 계정. 다른 공급자에 대한 내용은 [여기서 커넥터 목록을 검토하세요](/connectors/). 이 빠른 시작에서는 회사 또는 학교 계정으로 Office 365 Outlook을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다를 수 있습니다.

* 승인 워크플로를 지원하는 Office 365 Outlook 또는 Outlook.com의 이메일 계정. 이 자습서에서는 Office 365 Outlook을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다르게 표시될 수 있습니다.

## <a name="create-your-logic-app"></a>논리 앱 만들기

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 홈 페이지에서 **리소스 만들기**를 선택합니다.

1. Azure Marketplace 메뉴에서 **통합** > **논리 앱**을 선택합니다.

   !["통합" 및 "논리 앱"이 선택된 Azure Marketplace 메뉴를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. **논리 앱** 창에서, 만들려는 논리 앱에 대해 설명하는 정보를 여기에 입력합니다.

   ![논리 앱 만들기 창 및 새 논리 앱에 대해 입력해야 하는 정보를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **구독** | <*Azure-subscription-name*> | Azure 구독 이름. 이 예에서는 `Pay-As-You-Go`를 사용합니다. |
   | **리소스 그룹** | LA-MailingList-RG | 관련 리소스를 구성하는 데 사용되는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름입니다. 이 예제는 새 리소스 그룹 `LA-MailingList-RG`를 만듭니다. |
   | **이름** | LA-MailingList | 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 괄호(`(`, `)`) 및 마침표(`.`)만 포함할 수 있는 논리 앱의 이름입니다. 이 예에서는 `LA-MailingList`를 사용합니다. |
   | **위치** | 미국 서부 | 논리 앱 정보를 저장할 지역입니다. 이 예에서는 `West US`를 사용합니다. |
   | **Log Analytics** | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. |
   ||||

1. 완료되면 **검토 + 만들기**를 선택합니다. Azure가 논리 앱에 대한 정보의 유효성을 검사한 후에는 **만들기**를 선택합니다.

1. Azure가 앱을 배포한 후 **리소스로 이동**을 선택합니다.

   Azure가 Logic Apps 템플릿 선택 창을 엽니다. 이 창은 소개 비디오, 일반적으로 사용되는 트리거 및 논리 앱 템플릿 패턴을 보여줍니다.

1. 비디오와 일반 트리거를 지나 **템플릿** 섹션이 나올 때까지 아래로 스크롤하고, **비어 있는 논리 앱**을 선택합니다.

   !["비어 있는 논리 앱"이 선택된 Logic Apps 템플릿 선택 창을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

다음으로, 구독 요청이 있는 수신 이메일을 수신 대기하는 Outlook [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 추가합니다. 각 논리 앱은 특정 이벤트가 발생하거나 새 데이터가 특정 조건을 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 자세한 내용은 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="add-trigger-to-monitor-emails"></a>이메일을 모니터링하는 트리거 추가

1. Logic Apps 디자이너 검색 상자에 `when email arrives`를 입력하고 **새 이메일이 도착하는 경우**라는 트리거를 선택합니다.

   * Azure 회사 또는 학교 계정인 경우 **Office 365 Outlook**을 선택합니다.
   * Microsoft 개인 계정인 경우 **Outlook.com**을 선택합니다.

   이 예제에서는 Office 365 Outlook을 선택하여 진행합니다.

   ![검색어로 "이메일이 도착하는 경우"를 입력하고 "새 이메일이 도착하는 경우" 트리거를 선택한 Logic Apps 디자이너 검색 상자의 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. 아직 연결이 설정되지 않은 경우 로그인하고 메시지가 표시되면 이메일 계정에 대한 액세스를 인증합니다.

   Azure Logic Apps가 이메일 계정에 연결합니다.

1. 트리거에서 새 이메일을 확인하는 기준을 입력합니다.

   1. 이메일을 확인하는 폴더를 지정하고, 다른 속성은 기본값을 유지합니다.

      !["새 이메일이 도착하는 경우" 작업 및 "폴더"가 "수신함"으로 설정된 디자이너를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. 제목 줄을 기준으로 이메일을 필터링할 수 있도록 트리거의 **제목 필터** 속성을 추가합니다. **새 매개 변수 추가** 목록을 열고 **제목 필터**를 선택합니다.

      !["새 매개 변수 추가" 목록이 열려 있고 "제목 필터"가 선택된 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      이 트리거의 속성에 대한 자세한 내용은 [Office 365 Outlook 커넥터 참조](/connectors/office365/) 또는 [Outlook.com 커넥터 참조](/connectors/outlook/)에서 확인할 수 있습니다.

   1. 트리거에 속성이 표시되면 `subscribe-test-members-ML` 텍스트를 입력합니다.

      !["subscribe-test-members-ML" 텍스트가 입력된 "제목 필터"를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. 지금은 트리거의 세부 정보를 숨기기 위해 셰이프의 제목 표시줄 내부를 클릭하여 셰이프를 축소합니다.

   ![축소된 트리거 셰이프를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 논리 앱이 라이브 상태이지만 수신 이메일을 확인하는 것 외에는 아무 것도 수행하지 않습니다. 따라서 트리거가 실행되면 응답하는 작업을 추가해 보겠습니다.

## <a name="send-approval-email"></a>승인 전자 메일 보내기

트리거가 생겼으니, 요청을 승인 또는 거부하는 이메일을 보내는 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다.

1. Logic Apps 디자이너의 **새 이메일이 도착하는 경우** 트리거에서 **새 단계**를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 `send approval`을 입력하고 **승인 이메일 보내기**라는 작업을 선택합니다.

   !["승인" 작업으로 필터링된 "작업 선택" 목록과 "승인 이메일 보내기" 작업이 선택된 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 여기에 설명된 대로 지정된 속성의 값을 입력합니다. 나머지는 기본값으로 둡니다. 이러한 속성에 대한 자세한 내용은 [Office 365 Outlook 커넥터 참조](/connectors/office365/) 또는 [Outlook.com 커넥터 참조](/connectors/outlook/)에서 확인할 수 있습니다.

   !["승인 이메일 보내기" 속성을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **수행할 작업** | <*approval-email-address*> | 승인자의 이메일 주소입니다. 자신의 주소를 사용하여 테스트할 수 있습니다. 이 예제에서는 가상의 이메일 주소 `sophiaowen@fabrikam.com`을 사용합니다. |
   | **Subject** | `Approve member request for test-members-ML` | 설명이 포함된 이메일 제목 |
   | **사용자 옵션** | `Approve, Reject` | 이 속성이 승인자가 선택할 수 있는 응답 옵션(기본적으로 **승인** 또는 **거부**)을 지정하는지 확인합니다. |
   ||||

   > [!NOTE]
   > 편집 상자 내부를 클릭하면 동적 콘텐츠 목록이 표시되는데, 지금은 무시해도 됩니다. 이 목록은 워크플로의 후속 작업에 대한 입력으로 선택할 수 있는 이전 작업의 출력을 보여줍니다.
 
1. 논리 앱을 저장합니다.

다음으로, 승인자가 선택한 응답을 확인하는 조건을 추가합니다.

## <a name="check-approval-response"></a>승인 응답 확인

1. **승인 이메일 보내기** 작업에서 **새 단계**를 선택합니다.

1. **작업 선택**에서 **기본 제공**을 선택합니다. 검색 상자에 `condition`을 입력하고 **조건**이라는 작업을 선택합니다.

   !["기본 제공"을 선택하고 "조건"을 검색어로 입력하고 "조건" 작업이 선택된 상태고 "작업 선택" 검색 상자를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. **조건** 제목 표시줄에서 **줄임표**( **...** ) 단추를 선택한 다음, **이름 바꾸기**를 선택합니다. `If request approved` 설명이 포함되도록 조건 이름을 바꿉니다.

   ![줄임표 단추가 선택되었고, "설정" 목록이 열려 있고, "이름 바꾸기" 명령이 선택된 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. 승인자가 **승인**을 선택했는지 확인하는 조건을 작성합니다.

   1. 조건의 왼쪽에서 **값 선택** 상자 내부를 클릭합니다.

   1. 표시되는 동적 콘텐츠 목록의 **승인 이메일 보내기** 아래에서 **SelectedOption** 속성을 선택합니다.

      !["승인 이메일 보내기" 섹션에서 "SelectedOption" 출력이 선택된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 가운데 비교 상자에서 **같음** 연산자를 선택합니다.

   1. 조건의 오른쪽에 있는 **값 선택** 상자에 `Approve` 텍스트를 입력합니다.

      완료되면 조건이 다음 예와 같습니다.

      ![승인된 요청 예제에 대한 완료된 조건을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. 논리 앱을 저장합니다.

다음으로, 검토자가 요청을 승인할 때 논리 앱에서 수행하는 작업을 지정합니다. 

## <a name="add-member-to-mailchimp-list"></a>MailChimp 목록에 구성원 추가

이제 메일 그룹에 승인된 구성원을 추가하는 작업을 추가합니다.

1. 조건의 **True** 분기에서 **작업 추가**를 선택합니다.

1. **작업 선택** 검색 상자에서 **모두**를 선택합니다. 검색 상자에 `mailchimp`를 입력하고 **목록에 구성원 추가**라는 작업을 선택합니다.

   ![검색어 "mailchimp"와 "목록에 구성원 추가" 작업이 선택된 "작업 선택" 상자를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. 아직 MailChimp 계정에 연결하지 않은 경우 연결하라는 메시지가 표시됩니다.

1. **목록에 구성원 추가** 작업에서, 여기에 설명된 대로 정보를 제공합니다.

   !["목록에 구성원 추가" 작업 정보를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **목록 Id** | 예 | <*mailing-list-name*> | MailChimp 메일 목록의 이름을 선택합니다. 이 예에서는 `test-members-ML`를 사용합니다. |
   | **이메일 주소** | 예 | <*new-member-email-address*> | 열리는 동적 콘텐츠 목록의 **새 이메일이 도착하는 경우** 섹션에서, 트리거의 출력인 **보낸 사람**을 선택하고 새 구성원의 이메일 주소를 지정합니다. |
   | **상태** | 예 | <*member-subscription-status*> | 새 구성원에 대해 설정할 구독 상태를 선택합니다. 이 예제에서는 `subscribed`를 선택합니다. <p>자세한 내용은 [MailChimp API로 구독자 관리](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)를 참조하세요. |
   |||||

   **목록에 구성원 추가** 작업 속성에 대한 자세한 내용은 [MailChimp 커넥터 참조](/connectors/mailchimp/)에서 확인할 수 있습니다.

1. 논리 앱을 저장합니다.

다음으로, 새 구성원이 메일 그룹에 성공적으로 조인했는지 확인할 수 있도록 조건을 추가합니다. 이렇게 하면 논리 앱에서 이 작업의 성공 또는 실패 여부를 알려줄 수 있습니다.

## <a name="check-for-success-or-failure"></a>성공 또는 실패 여부 확인

1. **true** 분기의 **목록에 구성원 추가** 작업 아래에서 **작업 추가**를 선택합니다.

1. **작업 선택**에서 **기본 제공**을 선택합니다. 검색 상자에 `condition`을 입력하고 **조건**이라는 작업을 선택합니다.

1. `If add member succeeded` 설명이 포함되도록 조건 이름을 바꿉니다.

1. 승인된 구성원이 메일 그룹에 성공적으로 조인했는지 아니면 실패했는지 확인하는 조건을 작성합니다.

   1. 조건의 왼쪽에서 **값 선택** 상자 내부를 클릭합니다. 표시되는 동적 콘텐츠 목록의 **목록에 구성원 추가** 섹션에서 **상태** 속성을 선택합니다.

      예를 들어 이제 조건은 다음 예제와 비슷합니다.

      ![조건의 왼쪽에 표시되는 "상태"가 입력된 "값 선택" 상자를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 가운데 비교 상자에서 **같음** 연산자를 선택합니다.

   1. 조건의 오른쪽에 있는 **값 선택** 상자에서 `subscribed` 텍스트를 입력합니다.

      완료되면 조건이 다음 예와 같습니다.

      ![성공 또는 실패한 구독을 확인하는 완성된 조건을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

다음으로, 승인된 구성원이 메일 그룹에 성공적으로 조인할 때 또는 조인에 실패할 때 보낼 이메일을 설정합니다.

## <a name="send-email-if-member-added"></a>구성원이 추가되면 이메일 보내기

1. **구성원 추가가 성공한 경우** 조건의 **true** 분기에서 **작업 추가**를 선택합니다.

   !["구성원 추가가 성공한 경우" 조건의 "True" 분기와 "작업 추가"가 선택된 것을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. **작업 선택** 검색 상자에 `outlook send email`을 입력하고 **이메일 보내기**라는 작업을 선택합니다.

   !["Outlook 이메일 보내기"를 입력하고 알림에 대해 "이메일 보내기" 작업을 선택한 "작업 선택" 검색 상자를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. `Send email on success` 설명이 포함되도록 작업 이름을 바꿉니다.

1. **성공 시 이메일 보내기** 작업에서, 여기에 설명된 대로 정보를 제공합니다.

   !["성공 시 이메일 보내기" 작업과 성공 이메일에 대해 제공된 정보를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **본문** | 예 | <*success-email-body*> | 성공 이메일의 본문 콘텐츠입니다. 이 자습서에서는 다음 단계를 수행합니다. <p>1. 후행 공백이 하나 포함된 `New member has joined "test-members-ML":` 텍스트를 입력합니다. <p>2. 표시되는 동적 콘텐츠 목록에서 **이메일 주소** 속성을 선택합니다. <p>**참고**: 이 속성이 표시되지 않는 경우 **목록에 구성원 추가** 섹션 머리글 옆에 있는 **자세히 보기**를 선택합니다. <p>3. 다음 행에서, 후행 공백이 하나 포함된 `Member opt-in status: ` 텍스트를 입력합니다. <p>4. 동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **상태** 속성을 선택합니다. |
   | **Subject** | 예 | <*success-email-subject*> | 성공 이메일의 제목입니다. 이 자습서에서는 다음 단계를 수행합니다. <p>1. 후행 공백이 하나 포함된 `Success! Member added to "test-members-ML": ` 텍스트를 입력합니다. <p>2. 동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **이메일 주소** 속성을 선택합니다. |
   | **수행할 작업** | 예 | <*your-email-address*> | 성공 이메일을 보낼 위치에 대한 이메일 주소입니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. |
   |||||

1. 논리 앱을 저장합니다.

## <a name="send-email-if-member-not-added"></a>구성원이 추가되지 않으면 이메일 보내기

1. **구성원 추가가 성공한 경우** 조건의 **False** 분기에서 **작업 추가**를 선택합니다.

   !["구성원 추가가 성공한 경우" 조건의 "False" 분기와 "작업 추가"가 선택된 것을 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. **작업 선택** 검색 상자에 `outlook send email`을 입력하고 **이메일 보내기**라는 작업을 선택합니다.

   !["Outlook 이메일 보내기"를 입력하고 "이메일 보내기" 작업을 선택한 "작업 선택" 검색 상자를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. `Send email on failure` 설명이 포함되도록 작업 이름을 바꿉니다.

1. 여기에 설명된 것처럼 이 작업에 대한 정보를 입력합니다.

   !["실패 시 이메일 보내기" 작업과 실패 이메일에 대해 제공된 정보를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **본문** | 예 | <*body-for-failure-email*> | 실패 이메일의 본문 콘텐츠입니다. 이 자습서에서는 다음 텍스트를 입력합니다. <p>`Member might already exist. Check your MailChimp account.` |
   | **Subject** | 예 | <*subject-for-failure-email*> | 실패 이메일의 제목입니다. 이 자습서에서는 다음 단계를 수행합니다. <p>1. 후행 공백이 하나 포함된 `Failed, member not added to "test-members-ML": ` 텍스트를 입력합니다. <p>2. 동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **이메일 주소** 속성을 선택합니다. |
   | **수행할 작업** | 예 | <*your-email-address*> | 실패 이메일을 보낼 위치에 대한 이메일 주소입니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. |
   |||||

1. 논리 앱을 저장합니다. 

다음으로, 이 예제와 비슷하게 표시되는 논리 앱을 테스트합니다.

![완료된 예제 논리 앱 워크플로를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>논리 앱 실행

1. 메일 그룹에 조인하라는 이메일 요청을 자신에게 보냅니다. 받은 편지함에 요청이 나타날 때까지 기다립니다.

1. 논리 앱을 수동으로 시작하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 

   이메일에 트리거의 제목 필터와 일치하는 제목이 있는 경우 논리 앱은 구독 요청을 승인하는 이메일을 보냅니다.

1. 받은 승인 이메일에서 **승인**을 선택합니다.

1. 구독자의 이메일 주소가 메일 그룹에 없는 경우 논리 앱은 해당 인물의 이메일 주소를 추가하고 사용자에게 다음 예제와 비슷한 이메일을 보냅니다.

   ![성공한 구독의 이메일 예제를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   논리 앱이 구독자를 추가할 수 없으면 이 예제와 비슷한 이메일을 받게 됩니다.

   ![실패한 구독의 이메일 예제를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > 이메일을 받지 못한 경우 이메일의 정크 폴더를 확인합니다. 이메일 정크 필터가 이러한 종류의 메일을 리디렉션할 수 있습니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. Azure, Microsoft 서비스 및 기타 SaaS 앱의 정보를 통합하는 논리 앱을 만들고 실행하셨습니다.

## <a name="clean-up-resources"></a>리소스 정리

앱을 해제하거나 삭제할 때까지 논리 앱이 계속 실행됩니다. 논리 앱 샘플이 더 이상 필요하지 않으면 논리 앱 및 관련 리소스가 포함된 리소스 그룹을 삭제합니다.

1. Azure Portal의 검색 상자에 앞에서 만든 리소스 그룹의 이름을 입력합니다. 결과의 **리소스 그룹**에서 리소스 그룹을 선택합니다.

   이 예제에서는 `LA-MailingList-RG`라는 리소스 그룹을 만들었습니다.

   !["la-mailinglist-rg"를 입력하고 **LA-MailingList-RG**를 선택한 Azure 검색 상자를 보여주는 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Azure 홈 페이지의 **최근 리소스**에 리소스 그룹이 표시되는 경우 홈 페이지에서 그룹을 선택할 수 있습니다.

1. 리소스 그룹 메뉴에서 **개요**가 선택되었는지 확인합니다. **개요** 창의 도구 모음에서 **리소스 그룹 삭제**를 선택합니다.

   ![리소스 그룹의 "개요" 창과 이 창의 도구 모음에서 "리소스 그룹 삭제"를 선택한 스크린샷](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 표시되는 확인 창에서 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 메일 목록 요청에 대한 승인을 처리하는 논리 앱을 만들었습니다. 지금부터는 Azure Storage 및 Azure Functions 같은 Azure 서비스를 통합하여 이메일 첨부 파일을 처리하고 저장하는 논리 앱을 만드는 방법을 알아보겠습니다.

> [!div class="nextstepaction"]
> [이메일 첨부 파일 처리](../logic-apps/tutorial-process-email-attachments-workflow.md)
