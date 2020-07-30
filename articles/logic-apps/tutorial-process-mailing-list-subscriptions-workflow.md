---
title: 자동화된 승인 기반 워크플로 빌드
description: 자습서 - Azure Logic Apps를 사용하여 메일 그룹 구독을 처리하는 자동화된 승인 기반 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: d9d2f29ffc34c203e5f3b3ebf094e73fb9cdfb75
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132401"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>자습서: Azure Logic Apps를 사용하여 자동화된 승인 기반 워크플로 만들기

이 자습서에서는 승인 기반 워크플로를 자동화하는 [논리 앱](../logic-apps/logic-apps-overview.md)을 빌드하는 방법을 보여줍니다. 특히 이 논리 앱은 [MailChimp](https://mailchimp.com/) 서비스를 통해 관리되는 메일 그룹에 대한 구독 요청을 처리합니다. 이 논리 앱은 이러한 요청에 대한 이메일 계정을 모니터링하고, 이러한 승인 요청을 보내고, 승인된 구성원을 메일 그룹에 추가합니다.

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

* Azure 구독 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정에 가입합니다](https://azure.microsoft.com/free/).

* 논리 앱이 승인된 구성원에 대한 이메일 주소를 추가할 수 있는 "test-members-ML"이라는 목록이 포함된 MailChimp 계정입니다. 계정이 없는 경우 [체험 계정에 가입](https://login.mailchimp.com/signup/)하고 [MailChimp 목록을 만드는 방법](https://us17.admin.mailchimp.com/lists/#)을 알아보세요.

* 승인 워크플로를 지원하는 Office 365 Outlook 또는 Outlook.com의 이메일 계정. 이 문서에서는 Office 365 Outlook을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다르게 표시될 수 있습니다.

## <a name="create-your-logic-app"></a>논리 앱 만들기

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 주 메뉴에서 **리소스 만들기** > **통합** > **논리 앱**을 차례로 선택합니다.

   ![새 논리 앱 리소스 만들기](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. 그림에 설명된 대로 **논리 앱 만들기** 아래에서 논리 앱에 대한 정보를 제공합니다. 완료되면 **만들기**를 선택합니다.

   ![논리 앱에 대한 정보 제공](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **이름** | LA-MailingList | 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 괄호(`(`, `)`) 및 마침표(`.`)만 포함할 수 있는 논리 앱의 이름입니다. 이 예제에서는 "LA-MailingList"를 사용합니다. |
   | **구독** | <*your-Azure-subscription-name*> | Azure 구독 이름 |
   | **리소스 그룹** | LA-MailingList-RG | 관련 리소스를 구성하는 데 사용되는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름입니다. 이 예제에서는 "LA-MailingList-RG"를 사용합니다. |
   | **위치** | 미국 서부 | 논리 앱 정보를 저장할 지역입니다. 이 예제에서는 “미국 서부”를 사용합니다. |
   | **Log Analytics** | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. |
   ||||

1. Azure가 앱을 배포한 후 Azure 도구 모음에서 배포된 논리 앱에 대해 **알림** > **리소스로 이동**을 선택합니다.

   ![새 논리 앱 리소스로 이동](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   또는 검색 상자에 이름을 입력하여 논리 앱을 찾아 선택할 수 있습니다.

   Logic Apps 디자이너가 열려 소개 비디오 및 일반적으로 사용되는 트리거와 논리 앱 패턴이 포함된 페이지가 표시됩니다. **템플릿** 아래에서 **빈 논리 앱**을 선택합니다.

   ![빈 논리 앱 템플릿 선택](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

다음으로, 구독 요청이 있는 수신 이메일을 수신 대기하는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 추가합니다. 각 논리 앱은 특정 이벤트가 발생하거나 새 데이터가 특정 조건을 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 자세한 내용은 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="add-trigger-to-monitor-emails"></a>이메일을 모니터링하는 트리거 추가

1. 논리 앱 디자이너의 검색 상자에서 `when email arrives`를 필터로 입력합니다. **트리거** 목록에서 해당 이메일 공급자에 대한 **새 이메일이 도착하는 경우** 트리거를 선택합니다.

   이 예제에서는 다음과 같이 Office 365 Outlook 트리거를 사용합니다.

   ![이메일 공급자에 대해 "새 이메일이 도착하는 경우" 트리거 선택](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Azure 회사 또는 학교 계정에서 Office 365 Outlook을 선택합니다.
   * Microsoft 개인 계정에서 Outlook.com을 선택합니다.

1. 로그인 메시지가 나타나면 Logic Apps가 이메일 계정에 연결할 수 있도록 자격 증명을 사용하여 이메일 계정에 로그인합니다.

1. 트리거에서 새 이메일을 모두 확인하는 기준을 입력합니다.

   1. 이메일 확인에 사용할 폴더, 간격 및 빈도를 지정합니다.

      ![이메일 확인에 사용할 폴더, 간격 및 빈도 지정](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | 속성 | 값 | 설명 |
      |----------|-------|-------------|
      | **폴더** | `Inbox` | 모니터링할 이메일 폴더 |
      | **간격** | `1` | 검사 간에 대기하는 간격의 수 |
      | **빈도** | `Hour` | 되풀이에 사용할 시간 단위 |
      ||||

   1. 이제 이메일 제목 줄을 기준으로 필터링할 수 있도록 트리거에 또 다른 속성을 추가합니다. **새 매개 변수 목록 추가**를 열고 **제목 필터** 속성을 선택합니다.

      ![트리거할 "제목 필터" 속성 추가](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      이 트리거의 속성에 대한 자세한 내용은 [Office 365 Outlook 커넥터 참조](/connectors/office365/) 또는 [Outlook.com 커넥터 참조](/connectors/outlook/)에서 확인할 수 있습니다.

   1. 트리거에 속성이 표시되면 `subscribe-test-members-ML` 텍스트를 입력합니다.

      !["제목 필터" 속성에 텍스트 입력](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. 트리거 세부 정보를 숨기려면 트리거의 제목 표시줄을 클릭합니다.

   ![세부 정보를 숨기려면 셰이프 축소](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 논리 앱이 라이브 상태이지만 수신 이메일을 확인하는 것 외에는 아무 것도 수행하지 않습니다. 따라서 트리거가 실행되면 응답하는 작업을 추가해 보겠습니다.

## <a name="send-approval-email"></a>승인 전자 메일 보내기

트리거가 생겼으니, 요청을 승인 또는 거부하는 이메일을 보내는 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다. 

1. **작업 선택** 아래의 검색 상자에 `approval`을 필터로 입력합니다. 작업 목록에서 이메일 공급자에 대한 **승인 이메일 보내기** 작업을 선택합니다. 

   이 예제에서는 다음과 같이 Office 365 Outlook 작업을 사용합니다.

   !["승인 이메일 보내기" 작업 선택](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 설명된 대로 이 작업에 대한 정보를 입력합니다. 

   ![승인 이메일 속성 보내기](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **수행할 작업** | <*your-email-address*> | 승인자의 이메일 주소입니다. 자신의 주소를 사용하여 테스트할 수 있습니다. 이 예제에서는 가상의 이메일 주소 "sophia.owen@fabrikam.com"을 사용합니다. |
   | **Subject** | `Approve member request for test-members-ML` | 설명이 포함된 이메일 제목 |
   | **사용자 옵션** | `Approve, Reject` | 승인자가 선택할 수 있는 응답 옵션입니다. 기본적으로 승인자는 응답으로 "승인" 또는 "거부"를 선택할 수 있습니다. |
   ||||

   지금은 특정 편집 상자 내부를 클릭할 때 표시되는 동적 콘텐츠 목록을 무시합니다. 이 목록을 통해 워크플로에서 입력으로 사용할 수 있는 이전 작업의 출력을 선택할 수 있습니다.

   이 작업의 속성에 대한 자세한 내용은 [Office 365 Outlook 커넥터 참조](/connectors/office365/) 또는 [Outlook.com 커넥터 참조](/connectors/outlook/)에서 확인할 수 있습니다.
 
1. 논리 앱을 저장합니다.

다음으로, 승인자가 선택한 응답을 확인하는 조건을 추가합니다.

## <a name="check-approval-response"></a>승인 응답 확인

1. **승인 이메일 보내기** 작업에서 **새 단계**를 선택합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 `condition`을 입력합니다. 작업 목록에서 **조건** 작업을 선택합니다.

   !["조건" 작업을 찾아서 선택](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. 보다 구체적인 설명이 포함되도록 조건 이름을 바꿉니다.

   1. 조건의 제목 표시줄에서 **줄임표**( **...** ) 단추 > **이름 바꾸기**를 선택합니다.

      ![조건 설명 이름 바꾸기](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. `If request approved` 설명이 포함되도록 조건 이름을 바꿉니다.

1. 승인자가 **승인**을 선택했는지 확인하는 조건을 작성합니다.

   1. 조건에서 조건의 왼쪽에 있는 **값 선택** 상자 내부를 클릭합니다.

   1. 표시되는 동적 콘텐츠 목록의 **승인 이메일 보내기** 아래에서 **SelectedOption** 속성을 선택합니다.

      ![동적 콘텐츠 목록에서 "SelectedOption"을 선택합니다.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 가운데 비교 상자에서 **같음** 연산자를 선택합니다.

   1. 조건의 오른쪽에 있는 **값 선택** 상자에 `Approve` 텍스트를 입력합니다.

      완료되면 조건이 다음 예와 같습니다.

      ![승인된 예제의 완성된 조건](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. 논리 앱을 저장합니다.

다음으로, 검토자가 요청을 승인할 때 논리 앱에서 수행하는 작업을 지정합니다. 

## <a name="add-member-to-mailchimp-list"></a>MailChimp 목록에 구성원 추가

이제 메일 그룹에 승인된 구성원을 추가하는 작업을 추가합니다.

1. 조건의 **true인 경우** 분기에서 **작업 추가**를 선택합니다.

1. **작업 선택** 아래에서 `mailchimp`를 필터로 입력하고, **목록에 구성원 추가** 작업을 선택합니다.

   !["목록에 구성원 추가" 작업 선택](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. MailChimp 계정에 액세스하라는 메시지가 표시되면 MailChimp 자격 증명으로 로그인합니다.

1. 여기에 설명된 것처럼 이 작업에 대한 정보를 입력합니다.

   !["목록에 구성원 추가"에 대한 정보 제공](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **목록 Id** | 예 | `test-members-ML` | MailChimp 메일 그룹의 이름. 이 예제에서는 "test-members-ML"을 사용합니다. |
   | **상태** | 예 | `subscribed` | 새 구성원의 구독 상태를 선택합니다. 이 예제에서는 "구독함"을 사용합니다. <p>자세한 내용은 [MailChimp API로 구독자 관리](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)를 참조하세요. |
   | **이메일 주소** | 예 | <*new-member-email-address*> | 동적 콘텐츠 목록의 **새 이메일이 도착하는 경우** 아래에서 새 구성원의 이메일 주소를 전달하는 **보내는 사람**을 선택합니다. |
   ||||

   이 작업의 속성에 대한 자세한 내용은 [MailChimp 커넥터 참조](/connectors/mailchimp/)에서 확인할 수 있습니다.

1. 논리 앱을 저장합니다.

다음으로, 새 구성원이 메일 그룹에 성공적으로 조인했는지 확인할 수 있도록 조건을 추가합니다. 이렇게 하면 논리 앱에서 이 작업의 성공 또는 실패 여부를 알려줍니다.

## <a name="check-for-success-or-failure"></a>성공 또는 실패 여부 확인

1. **true인 경우** 분기의 **목록에 구성원 추가** 작업 아래에서 **작업 추가**를 선택합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 `condition`을 입력합니다. 작업 목록에서 **조건**을 선택합니다.

1. `If add member succeeded` 설명이 포함되도록 조건 이름을 바꿉니다.

1. 승인된 구성원이 메일 그룹에 성공적으로 조인했는지 아니면 실패했는지 확인하는 조건을 작성합니다.

   1. 조건의 왼쪽에 있는 **값 선택** 상자 내부를 클릭합니다. 동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **상태** 속성을 선택합니다.

      예를 들어 이제 조건은 다음 예제와 비슷합니다.

      !["목록에 구성원 추가" 아래에서 "상태"를 선택합니다.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 가운데 비교 상자에서 **같음** 연산자를 선택합니다.

   1. 조건의 오른쪽에 있는 **값 선택** 상자에 `subscribed` 텍스트를 입력합니다.

      완료되면 조건이 다음 예와 같습니다.

      ![구독된 예제의 완성된 조건](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

다음으로, 승인된 구성원이 메일 그룹에 성공적으로 조인할 때 또는 조인에 실패할 때 보낼 이메일을 설정합니다.

## <a name="send-email-if-member-added"></a>구성원이 추가되면 이메일 보내기

1. **구성원 추가가 성공한 경우** 조건의 **true인 경우** 분기에서 **작업 추가**를 선택합니다.

   !["true인 경우" 분기에서 "작업 추가"를 선택합니다.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. **작업 선택** 아래의 검색 상자에 `outlook send email`을 필터로 입력하고, **이메일 보내기** 작업을 선택합니다.

   !["이메일 보내기" 작업 추가](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. `Send email on success` 설명이 포함되도록 작업 이름을 바꿉니다.

1. 설명된 것처럼 이 작업에 대한 정보를 제공합니다.

   ![성공 이메일에 대한 정보 제공](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 성공 이메일을 보낼 위치에 대한 이메일 주소입니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. |
   | **Subject** | 예 | <*subject-for-success-email*> | 성공 이메일의 제목입니다. 이 자습서에서는 다음 텍스트를 입력합니다. <p>`Success! Member added to "test-members-ML": ` <p>동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **이메일 주소** 속성을 선택합니다. |
   | **본문** | 예 | <*body-for-success-email*> | 성공 이메일의 본문 콘텐츠입니다. 이 자습서에서는 다음 텍스트를 입력합니다. <p>`New member has joined "test-members-ML":` <p>동적 콘텐츠 목록에서 **이메일 주소** 속성을 선택합니다. <p>다음 행에 `Member opt-in status: ` 텍스트를 입력합니다. <p> 동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **상태** 속성을 선택합니다. |
   |||||

1. 논리 앱을 저장합니다.

## <a name="send-email-if-member-not-added"></a>구성원이 추가되지 않으면 이메일 보내기

1. **구성원 추가가 성공한 경우** 조건의 **false인 경우** 분기에서 **작업 추가**를 선택합니다.

   !["false인 경우" 분기에서 "작업 추가"를 선택합니다.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. **작업 선택** 아래의 검색 상자에 `outlook send email`을 필터로 입력하고, **이메일 보내기** 작업을 선택합니다.

   !["이메일 보내기"에 대한 작업 추가](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. `Send email on failure` 설명이 포함되도록 작업 이름을 바꿉니다.

1. 여기에 설명된 것처럼 이 작업에 대한 정보를 입력합니다.

   ![실패 이메일에 대한 정보 제공](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 실패 이메일을 보낼 위치에 대한 이메일 주소입니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. |
   | **Subject** | 예 | <*subject-for-failure-email*> | 실패 이메일의 제목입니다. 이 자습서에서는 다음 텍스트를 입력합니다. <p>`Failed, member not added to "test-members-ML": ` <p>동적 콘텐츠 목록의 **목록에 구성원 추가** 아래에서 **이메일 주소** 속성을 선택합니다. |
   | **본문** | 예 | <*body-for-failure-email*> | 실패 이메일의 본문 콘텐츠입니다. 이 자습서에서는 다음 텍스트를 입력합니다. <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. 논리 앱을 저장합니다. 

다음으로, 이 예제와 비슷하게 표시되는 논리 앱을 테스트합니다.

![완료된 논리 앱 워크플로 예제](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>논리 앱 실행

1. 메일 그룹에 조인하라는 이메일 요청을 자신에게 보냅니다. 받은 편지함에 요청이 나타날 때까지 기다립니다.

1. 수동으로 논리 앱을 시작하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 

   이메일에 트리거의 제목 필터와 일치하는 제목이 있는 경우 논리 앱은 구독 요청을 승인하는 이메일을 보냅니다.

1. 승인 이메일에서 **승인**을 선택합니다.

1. 구독자의 이메일 주소가 메일 그룹에 없는 경우 논리 앱은 해당 인물의 이메일 주소를 추가하고 사용자에게 다음 예제와 비슷한 이메일을 보냅니다.

   ![예제 이메일 - 성공한 구독](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   논리 앱이 구독자를 추가할 수 없으면 이 예제와 비슷한 이메일을 받게 됩니다.

   ![예제 이메일 - 실패한 구독](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   이메일을 받지 못한 경우 이메일의 정크 폴더를 확인합니다. 이메일 정크 필터가 이러한 종류의 메일을 리디렉션할 수 있습니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. Azure, Microsoft 서비스 및 기타 SaaS 앱의 정보를 통합하는 논리 앱을 만들고 실행하셨습니다.

## <a name="clean-up-resources"></a>리소스 정리

논리 앱 샘플이 더 이상 필요하지 않으면 논리 앱 및 관련 리소스가 포함된 리소스 그룹을 삭제합니다. 

1. 주 Azure 메뉴에서 **리소스 그룹**으로 이동하여 논리 앱의 리소스 그룹을 선택합니다.

1. 리소스 그룹 메뉴에서 **개요** > **리소스 그룹 삭제**를 차례로 선택합니다. 

   !["개요" > "리소스 그룹 삭제"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 리소스 그룹 이름을 입력하여 확인하고, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 메일 그룹 요청에 대한 승인을 관리하는 논리 앱을 만들었습니다. 지금부터는 Azure Storage 및 Azure Functions 같은 Azure 서비스를 통합하여 이메일 첨부 파일을 처리하고 저장하는 논리 앱을 만드는 방법을 알아보겠습니다.

> [!div class="nextstepaction"]
> [이메일 첨부 파일 처리](../logic-apps/tutorial-process-email-attachments-workflow.md)
