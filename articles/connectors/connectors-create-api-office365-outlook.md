---
title: Office 365 Outlook과 통합
description: Azure Logic Apps를 사용 하 여 Office 365 Outlook에서 전자 메일, 연락처 및 일정을 관리 하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 9caf69a7f78c7872f0a5f8a2ed07bdc749a29023
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682998"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Office 365 Outlook에서 이메일, 연락처 및 일정 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Office 365 Outlook 커넥터](/connectors/office365connector/)를 사용 하 여 논리 앱을 빌드하여 회사 또는 학교 계정을 관리 하는 자동화 된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음과 같은 작업을 자동화할 수 있습니다.

* 전자 메일을 가져오고 보내고 회신 합니다.
* 일정에서 모임 일정을 예약 합니다.
* 연락처를 추가 하 고 편집 합니다.

트리거를 사용 하 여 워크플로를 시작할 수 있습니다. 예를 들어 새 전자 메일이 도착 하는 경우, 일정 항목이 업데이트 될 때 또는 Salesforce와 같은 차이점 서비스에서 이벤트가 발생 하는 경우를 들 수 있습니다. 트리거 이벤트에 응답 하는 동작 (예: 전자 메일 보내기 또는 새 일정 이벤트 만들기)을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [회사 또는 학교 계정](https://www.office.com/)으로 로그인 하는 Outlook 계정 또는 계정이 있는 경우 @outlook.com @hotmail.com [Outlook.com 커넥터](../connectors/connectors-create-api-outlook.md) 를 대신 사용 합니다. 다른 사용자 계정 (예: 서비스 계정)을 사용 하 여 Outlook에 연결 하려면 [다른 계정을 사용 하 여 연결](#connect-using-other-accounts)을 참조 하세요.

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* Outlook 계정에 액세스 하려는 논리 앱입니다. Office 365 Outlook 트리거를 사용 하 여 워크플로를 시작 하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 있어야 합니다. 워크플로에 Office 365 Outlook 작업을 추가 하려면 논리 앱에 이미 트리거가 있어야 합니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts) 는 논리 앱에서 워크플로를 시작 하는 이벤트입니다. 이 예제 논리 앱은 지정 된 간격 및 빈도에 따라 전자 메일 계정에서 업데이트 된 일정 이벤트를 확인 하는 "폴링" 트리거를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제에서는 **예정 된 이벤트가 곧 시작 될 때를** 선택 합니다.
   
   ![트리거를 선택 하 여 논리 앱 시작](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Outlook 계정에 대 한 활성 연결이 없는 경우 로그인 하 여 해당 연결을 만들라는 메시지가 표시 됩니다. 다른 사용자 계정 (예: 서비스 계정)을 사용 하 여 Outlook에 연결 하려면 [다른 계정을 사용 하 여 연결](#connect-using-other-accounts)을 참조 하세요. 그렇지 않으면 트리거의 속성에 대 한 정보를 제공 합니다.

   > [!NOTE]
   > 로그인 자격 증명을 변경 하더라도 해지 될 때까지 연결이 만료 되지 않습니다. 자세한 내용은 [Azure Active Directory에서 구성 가능한 토큰 수명](../active-directory/develop/active-directory-configurable-token-lifetimes.md)을 참조하세요.

   이 예에서는 트리거가 확인 하는 달력을 선택 합니다. 예를 들면 다음과 같습니다.

   ![트리거의 속성 구성](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 트리거에서 **빈도** 및 **간격** 값을 설정 합니다. **표준 시간대** 와 같은 사용 가능한 다른 트리거 속성을 추가 하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택 합니다.

   예를 들어 트리거가 15 분 마다 일정을 확인 하도록 하려면 **Frequency** 를 **Minute** 로 설정 하 고 **Interval** 을로 설정 `15` 합니다. 

   ![트리거의 빈도 및 간격 설정](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

이제 트리거가 발생 한 후 실행 되는 작업을 추가 합니다. 예를 들어 일정 이벤트가 15 분 내에 시작 될 때 텍스트를 전송 하는 Twilio **메시지 보내기** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업](../logic-apps/logic-apps-overview.md#logic-app-concepts) 은 논리 앱의 워크플로에서 실행 되는 작업입니다. 이 예제 논리 앱은 Office 365 Outlook에서 새 연락처를 만듭니다. 다른 트리거 또는 작업의 출력을 사용 하 여 연락처를 만들 수 있습니다. 예를 들어, **레코드를 만들 때** 논리 앱에서 Dynamics 365 트리거를 사용 한다고 가정 합니다. Office 365 Outlook **연락처 만들기** 작업을 추가 하 고 SalesForce 트리거의 출력을 사용 하 여 새 연락처를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 동작을 추가 하려면 **새 단계** 를 선택 합니다. 

   단계 사이에 작업을 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제에서는 **연락처 만들기** 를 선택 합니다.

   ![논리 앱에서 실행할 작업을 선택 합니다.](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Outlook 계정에 대 한 활성 연결이 없는 경우 로그인 하 여 해당 연결을 만들라는 메시지가 표시 됩니다. 다른 사용자 계정 (예: 서비스 계정)을 사용 하 여 Outlook에 연결 하려면 [다른 계정을 사용 하 여 연결](#connect-using-other-accounts)을 참조 하세요. 그렇지 않으면 작업의 속성에 대 한 정보를 제공 합니다.

   > [!NOTE]
   > 로그인 자격 증명을 변경 하더라도 해지 될 때까지 연결이 만료 되지 않습니다. 자세한 내용은 [Azure Active Directory에서 구성 가능한 토큰 수명](../active-directory/develop/active-directory-configurable-token-lifetimes.md)을 참조하세요.

   이 예제에서는 작업에서 새 연락처를 만드는 연락처 폴더를 선택 합니다. 예를 들면 다음과 같습니다.

   ![작업 속성 구성](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   사용 가능한 다른 작업 속성을 추가 하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택 합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>다른 계정을 사용 하 여 연결

현재 Azure에 로그인 한 계정이 아닌 다른 계정을 사용 하 여 Outlook에 연결을 시도 하는 경우 [SSO (Single Sign-On)](../active-directory/manage-apps/what-is-single-sign-on.md) 오류가 발생할 수 있습니다. 이 문제는 한 계정으로 Azure Portal에 로그인 했지만 다른 계정을 사용 하 여 연결을 만드는 경우에 발생 합니다. 논리 앱 디자이너는 Azure에 로그인 된 계정을 사용 해야 합니다. 이 문제를 해결 하려면 다음 옵션을 선택 합니다.

* 다른 계정을 논리 앱의 리소스 그룹에 대 한 **참가자로** 설정 합니다.

  1. 논리 앱의 리소스 그룹 메뉴에서 **액세스 제어 (IAM)** 를 선택 합니다. **참가자** 역할을 사용 하 여 다른 계정을 설정 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

  1. 회사 또는 학교 계정을 사용 하 여 Azure Portal에 로그인 한 경우 로그 아웃 하 고 다른 계정으로 다시 로그인 합니다. 이제 다른 계정을 사용 하 여 Outlook에 대 한 연결을 만들 수 있습니다.

* 회사 또는 학교 계정에 "다른 이름으로 보내기" 권한이 있도록 다른 계정을 설정 합니다.

   관리자 권한이 있는 경우 서비스 계정의 사서함에서 사용 권한 대신 보내기 또는 **보내기** 를 사용 하 여 회사 또는 학교 **계정으로 설정** 합니다. 자세한 내용은 [다른 사용자에 게 사서함 사용 권한 부여-관리자 도움말](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user)을 참조 하십시오. 그런 다음 회사 또는 학교 계정을 사용 하 여 연결을 만들 수 있습니다. 이제 발신자를 지정할 수 있는 트리거 또는 작업에서 서비스 계정의 전자 메일 주소를 사용할 수 있습니다.

   예를 들어 **전자 메일 보내기** 작업에는 **(다른 이름으로 보내기)의** 선택적 매개 변수가 있습니다 .이 매개 변수를 사용 하 여 작업에 추가 하 고 서비스 계정의 전자 메일 주소를 발신자로 사용할 수 있습니다. 이 매개 변수를 추가 하려면 다음 단계를 수행 합니다.

   1. **이메일 보내기** 작업에서 **매개 변수 추가** 목록을 열고 **From (Send as)** 매개 변수를 선택 합니다.

   1. 매개 변수가 작업에 표시 되 면 서비스 계정의 전자 메일 주소를 입력 합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명 된 대로이 커넥터에 대 한 기술 세부 정보 (예: 트리거, 작업 및 제한)는 [커넥터의 참조 페이지](/connectors/office365/)를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.