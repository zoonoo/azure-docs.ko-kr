---
title: Azure 리소스를 관리 및 모니터링 하는 자동화 작업 만들기
description: Azure Logic Apps에서 실행 되는 워크플로를 만들어 Azure 리소스를 관리 하 고 비용을 모니터링 하는 데 도움이 되는 자동화 된 작업을 설정 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1826b17a971b49fdfe8d5df02d71eb682b15db6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269729"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Automation 작업 (미리 보기)을 만들어 Azure 리소스를 관리 하 고 비용을 모니터링 합니다.

> [!IMPORTANT]
> 이 기능은 공개 미리 보기로 제공 되며 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 리소스](../azure-resource-manager/management/overview.md#terminology) 를 보다 쉽게 관리할 수 있도록 리소스 종류에 따라 가용성에 따라 달라 지는 자동화 작업 템플릿을 사용 하 여 특정 리소스 또는 리소스 그룹에 대 한 자동화 된 관리 작업을 만들 수 있습니다. 예를 들어 [Azure storage 계정의](../storage/common/storage-account-overview.md)경우 해당 저장소 계정에 대 한 월별 비용을 전송 하는 자동화 작업을 설정할 수 있습니다. [Azure 가상 컴퓨터](https://azure.microsoft.com/services/virtual-machines/)의 경우 미리 정의 된 일정에 따라 가상 컴퓨터를 켜고 끄는 자동화 작업을 만들 수 있습니다.

백그라운드에서 자동화 작업은 실제로 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 서비스에서 실행 되 고 동일한 [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) 및 [가격 책정 모델](../logic-apps/logic-apps-pricing.md)을 사용 하 여 청구 되는 워크플로입니다. 작업을 만든 후에는 논리 앱 디자이너에서 작업을 열어 기본 워크플로를 보고 편집할 수 있습니다. 작업에서 하나 이상의 실행을 완료 한 후에는 각 실행에 대 한 상태, 기록, 입력 및 출력을 검토할 수 있습니다.

이 미리 보기에서 현재 사용할 수 있는 작업 템플릿은 다음과 같습니다.

| 리소스 유형 | 자동화 작업 템플릿 |
|---------------|---------------------------|
| Azure 리소스 그룹 | **리소스가 삭제 되는 경우** |
| 모든 Azure 리소스 | **리소스에 대 한 월별 비용 전송** |
| Azure 가상 머신 | 추가 필수 구성 요소: <p>- **가상 컴퓨터 전원 끄기** <br>- **가상 컴퓨터 시작** |
| Azure Storage 계정 | 추가 필수 구성 요소: <p>- **이전 blob 삭제** |
| Azure Cosmos DB | 또한 <p>- **전자 메일을 통해 쿼리 결과 보내기** |
|||

이 문서에서는 다음 작업을 완료 하는 방법을 보여 줍니다.

* 특정 Azure 리소스에 대 한 [자동화 작업을 만듭니다](#create-automation-task) .

* 실행 상태, 입력, 출력 및 기타 기록 정보를 포함 하는 [작업 기록을 검토](#review-task-history)합니다.

* 작업을 업데이트 하거나 논리 앱 디자이너에서 작업의 기본 워크플로를 사용자 지정할 수 있도록 [작업을 편집](#edit-task) 합니다.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>자동화 작업은 Azure Automation와 어떻게 다릅니까?

현재는 리소스 수준 에서만 자동화 작업을 만들고, 작업 실행 기록을 확인 하 고, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 서비스에서 제공 하는 작업의 기본 논리 앱 워크플로를 편집할 수 있습니다. 자동화 작업은 [Azure Automation](../automation/automation-intro.md)보다 간단 하 고 간단 합니다.

비교 하 여 Azure Automation은 Azure 및 비 Azure 환경에서 일관 된 관리를 지 원하는 클라우드 기반 자동화 및 구성 서비스입니다. 이 서비스는 [runbook](../automation/automation-runbook-execution.md), [변경 내용 추적 및 인벤토리](../automation/change-tracking.md)를 통한 구성 관리, 업데이트 관리, 공유 기능 및 다른 유형의 기능을 사용 하 여 [오케스트레이션 프로세스에 대 한 프로세스 자동화](../automation/automation-intro.md#process-automation) 로 구성 됩니다. Automation을 통해 워크로드와 리소스를 배포하고, 운영하고, 서비스를 해제하는 동안 완벽한 제어가 가능합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 관리 하려는 Azure 리소스입니다. 이 문서에서는 Azure storage 계정을 예제로 사용 합니다.

* Office 365 Outlook을 사용 하 여 전자 메일을 보내는 예제와 함께 작업을 수행 하려는 경우 Office 365 계정

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>자동화 작업 만들기

1. [Azure Portal](https://portal.azure.com)에서 관리 하려는 리소스를 찾습니다.

1. 리소스 메뉴에서 **자동화** 섹션으로 스크롤하고 **작업** 을 선택 합니다.

   !["Automation" 섹션에서 "작업" 메뉴 항목을 선택 하는 Azure Portal 및 저장소 계정 리소스 메뉴를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. **작업 창에서** **추가** 를 선택 하 여 작업 템플릿을 선택할 수 있습니다.

   ![도구 모음에서 "추가"가 선택 된 저장소 계정 "작업" 창을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. **작업 추가** 창의 **템플릿 선택**에서 만들려는 작업의 템플릿을 선택 하 고 **다음: 인증**을 선택 합니다.

   이 예에서는 계속 해 서 **리소스에 대 한 월별 비용 보내기** 작업 템플릿을 선택 합니다.

   !["리소스에 대 한 월별 비용 보내기" 및 "다음: 인증" 선택 항목을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. **인증**의 연결 섹션에서 각 연결에 대해 **만들기** 를 선택 하 여 해당 연결에 대 한 인증 자격 증명을 제공할 수 **있도록 합니다.** 각 태스크의 연결 유형은 태스크에 따라 다릅니다.

   ![Azure Resource Manager 연결에 대해 선택한 "만들기" 옵션을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. 메시지가 표시 되 면 Azure 계정 자격 증명을 사용 하 여 로그인 합니다.

   !["로그인" 선택을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   성공적으로 인증 된 각 연결은 다음 예와 유사 합니다.

   ![성공적으로 생성 된 연결을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. 필요한 모든 연결을 인증 한 후 **다음: 구성**을 선택 합니다.

1. **구성**아래에서 태스크 이름 및 작업에 필요한 기타 정보를 제공 합니다. 완료되면 **만들기**를 선택합니다.

   > [!NOTE]
   > 작업 이름을 만든 후에는 변경할 수 없으므로 [기본 워크플로를 편집](#edit-task-workflow)하는 경우에도 여전히 적용 되는 이름을 고려해 야 합니다. 기본 워크플로를 변경한 내용은 작업 템플릿이 아니라 사용자가 만든 작업에만 적용 됩니다.
   >
   > 예를 들어 작업의 이름을 지정할 때 `Send monthly cost` 나중에 매주 실행 되도록 기본 워크플로를 편집 하는 경우 작업의 이름을로 변경할 수 없습니다 `Send weekly cost` .

   전자 메일 알림을 보내는 태스크에는 전자 메일 주소가 필요 합니다.

   ![선택한 작업에 대 한 필수 정보를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   자동으로 라이브 및 실행 되는 만든 태스크가 이제 **Automation 작업** 목록에 표시 됩니다.

   ![자동화 작업 목록을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > 작업이 즉시 표시 되지 않으면 작업 목록을 새로 고치거 나 새로 고침을 수행 하기 전에 잠시 기다려야 합니다. 도구 모음에서 **새로 고침**을 선택 합니다.

   선택한 작업을 실행 하면 다음 예제와 같은 전자 메일을 받게 됩니다.

   ![작업에서 보낸 전자 메일 알림을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>작업 기록 검토

상태, 입력, 출력 및 기타 정보와 함께 작업의 실행 기록을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 검토 하려는 작업 기록이 있는 리소스를 찾습니다.

1. 리소스 메뉴의 **설정**에서 **자동화 작업**을 선택 합니다.

1. 작업 목록에서 검토 하려는 작업을 찾습니다. 해당 작업의 **실행** 열에서 **보기**를 선택 합니다.

   ![작업 및 선택한 "보기" 옵션을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   **실행 기록** 창에는 해당 상태, 시작 시간, 식별자 및 실행 기간과 함께 작업에 대 한 모든 실행이 표시 됩니다.

   ![작업 실행, 상태 및 기타 정보를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   실행에 대 한 가능한 상태는 다음과 같습니다.

   | 상태 | 설명 |
   |--------|-------------|
   | **취소** | 작업을 실행 하는 동안 작업이 취소 되었습니다. |
   | **실패** | 작업에 실패 한 작업이 하나 이상 있지만 실패를 처리 하기 위해 후속 작업이 없었습니다. |
   | **실행 중** | 태스크가 현재 실행 되 고 있습니다. |
   | **성공함** | 모든 작업에 성공했습니다. 작업에 실패 한 경우에도 작업을 성공적으로 완료할 수 있지만 실패를 처리 하기 위해 후속 작업이 있었습니다. |
   | **대기 중** | 작업의 이전 인스턴스가 여전히 실행 중 이므로 실행이 아직 시작 되지 않았고 일시 중지 되었습니다. |
   |||

   자세한 내용은 [검토 실행 기록](../logic-apps/monitor-logic-apps.md#review-runs-history) 을 참조 하세요.

1. 실행의 각 단계에 대 한 상태 및 기타 정보를 보려면 해당 실행을 선택 합니다.

   **논리 앱 실행** 창이 열리고 실행 된 기본 워크플로가 표시 됩니다.

   * 워크플로는 항상 [*트리거로*](../connectors/apis-list.md#triggers-actions)시작 합니다. 이 태스크의 경우 워크플로는 [ **되풀이** 트리거로](../connectors/connectors-native-recurrence.md)시작 합니다.

   * 각 단계에는 상태 및 실행 기간이 표시 됩니다. 0 초의 기간이 있는 단계는 실행 하는 데 1 초 미만으로 소요 됩니다.

   ![실행, 상태 및 실행 기간의 각 단계를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. 각 단계에 대 한 입력 및 출력을 검토 하려면 확장 하는 단계를 선택 합니다.

   이 예에서는 출력이 없는 되풀이 트리거의 입력을 보여 줍니다. 트리거는 워크플로가 실행 되는 경우에만 지정 하 고 이후 처리할 작업에 대해 출력을 제공 하지 않기 때문입니다.

   ![확장 된 트리거와 입력을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   이와 대조적으로, **전자 메일 보내기** 작업은 워크플로 및 출력의 이전 작업에서 입력을 포함 합니다.

   ![확장 된 작업, 입력 및 출력을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Azure 리소스에 대 한 자동화 작업의 컨텍스트와 별도로 앱, 데이터, 서비스 및 시스템을 통합할 수 있도록 자동화 된 워크플로를 직접 빌드하는 방법에 대 한 자세한 내용은 [빠른 시작: Azure Logic Apps-Azure Portal를 사용 하 여 첫 번째 통합 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>작업 편집

작업을 변경 하려면 다음 옵션을 사용할 수 있습니다.

* 연결 정보 또는 구성 정보 (예: 전자 메일 주소)와 같은 작업의 속성을 변경할 수 있도록 ["인라인" 태스크를 편집](#edit-task-inline) 합니다.

* 논리 앱 디자이너에서 [작업의 기본 워크플로를 편집](#edit-task-workflow) 합니다.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>인라인으로 작업 편집

1. [Azure Portal](https://portal.azure.com)에서 업데이트 하려는 태스크가 있는 리소스를 찾습니다.

1. 리소스의 메뉴에 있는 **Automation**에서 **작업**을 선택 합니다.

1. 작업 목록에서 업데이트 하려는 작업을 찾습니다. 작업의 줄임표 (**...**) 메뉴를 열고 **인라인 편집**을 선택 합니다.

   ![열린 줄임표 메뉴와 선택한 옵션인 "인라인 편집"을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   기본적으로 **인증** 탭이 나타나고 기존 연결이 표시 됩니다.

1. 새 인증 자격 증명을 추가 하거나 연결에 대해 다른 기존 인증 자격 증명을 선택 하려면 연결의 줄임표 (**...**) 메뉴를 열고 **새 연결 추가** 또는 사용 가능한 경우 다른 인증 자격 증명을 선택 합니다.

   ![인증 탭, 기존 연결 및 선택한 줄임표 메뉴를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. 다른 태스크 속성을 업데이트 하려면 **다음: 구성**을 선택 합니다.

   이 예제의 작업의 경우 편집에 사용할 수 있는 유일한 속성은 전자 메일 주소입니다.

   ![구성 탭을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. 완료되면 **저장**을 선택합니다.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>작업의 기본 워크플로 편집

Automation 작업에 대 한 기본 워크플로를 변경 하면 변경 내용이 작업을 만드는 템플릿이 아니라 사용자가 만든 작업 인스턴스에만 영향을 줍니다. 변경 내용을 적용 하 고 저장 한 후에는 원래 작업에 대해 제공한 이름이 더 이상 작업을 정확 하 게 설명 하지 않을 수 있으므로 다른 이름으로 작업을 다시 만들어야 할 수도 있습니다.

> [!TIP]
> 대신 복사 된 버전을 편집할 수 있도록 기본 워크플로를 복제 하는 것이 가장 좋습니다. 이렇게 하면 원래 자동화 작업이 계속 작동 하 고 기존 기능을 중단 하지 않고 계속 실행 되는 동안 복사본에 대 한 변경 내용을 만들고 테스트할 수 있습니다. 변경을 완료 하 고 새 버전이 성공적으로 실행 되는 것이 만족 되 면 원래 자동화 작업을 사용 하지 않도록 설정 하거나 삭제 하 고 자동화 작업에 복제 된 버전을 사용할 수 있습니다. 다음 단계는 워크플로를 복제 하는 방법에 대 한 정보를 포함 합니다.

1. [Azure Portal](https://portal.azure.com)에서 업데이트 하려는 태스크가 있는 리소스를 찾습니다.

1. 리소스의 메뉴에 있는 **Automation**에서 **작업**을 선택 합니다.

1. 작업 목록에서 업데이트 하려는 작업을 찾습니다. 작업의 줄임표 (**...**) 메뉴를 열고 **Logic Apps에서 열기**를 선택 합니다.

   ![열린 줄임표 메뉴와 선택한 옵션인 "Logic Apps에서 열기"를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   작업의 기본 워크플로가 Azure Logic Apps 서비스에서 열리고 작업에 사용할 수 있는 것과 동일한 실행 기록을 볼 수 있는 **개요** 창이 표시 됩니다.

   ![개요 창이 선택 된 Azure Logic Apps 보기에서 작업을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. 논리 앱 디자이너에서 기본 워크플로를 열려면 논리 앱의 메뉴에서 **논리 앱 디자이너**를 선택 합니다.

   ![기본 워크플로를 사용 하 여 "논리 앱 디자이너" 메뉴 옵션을 선택 하 고 디자이너 화면을 표시 하는 스크린샷](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   이제 워크플로의 트리거 및 작업에 대 한 속성을 편집 하 고 워크플로 자체를 정의 하는 트리거 및 작업을 편집할 수 있습니다. 그러나 가장 좋은 방법은 원본 워크플로가 계속 작동 하 고 실행 되는 동안 복사본을 변경할 수 있도록 워크플로를 복제 하는 단계를 수행 하는 것입니다.

1. 워크플로를 복제 하 고 복사 된 버전을 대신 편집 하려면 다음 단계를 수행 합니다.

   1. 논리 앱 워크플로 메뉴에서 **개요**를 선택 합니다.

   1. 개요 창의 도구 모음에서 **복제**를 선택 합니다.

   1. 논리 앱 생성 창의 **이름**에 복사 된 논리 앱 워크플로에 대 한 새 이름을 입력 합니다.

      **논리 앱 상태**를 제외 하 고 다른 속성은 편집할 수 없습니다. 
      
   1. **논리 앱 상태**에서 변경 작업을 수행 하는 동안 복제 된 워크플로가 실행 되지 않도록 **사용 안 함** 을 선택 합니다. 변경을 테스트할 준비가 되 면 워크플로를 사용 하도록 설정할 수 있습니다.

   1. Azure가 복제 된 워크플로의 프로 비전을 완료 한 후 논리 앱 디자이너에서 해당 워크플로를 찾아 엽니다.

1. 트리거 또는 동작에 대 한 속성을 보려면 해당 트리거 또는 동작을 확장 합니다.

   예를 들어 되풀이 트리거를 매월이 아닌 매주 실행 되도록 변경할 수 있습니다.

   ![사용 가능한 빈도 옵션을 표시 하기 위해 빈도 목록이 열려 있는 확장 된 되풀이 트리거를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   되풀이 트리거에 대 한 자세한 내용은 되풀이 트리거를 [사용 하 여 되풀이 작업 및 워크플로 만들기, 예약 및 실행](../connectors/connectors-native-recurrence.md)을 참조 하세요. 사용할 수 있는 다른 트리거 및 작업에 대 한 자세한 내용은 [Azure Logic Apps 커넥터](../connectors/apis-list.md)를 참조 하십시오.

1. 변경 내용을 저장 하려면 디자이너 도구 모음에서 **저장**을 선택 합니다.

   ![디자이너 도구 모음과 선택한 "저장" 명령을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. 업데이트 된 워크플로를 테스트 하 고 실행 하려면 디자이너 도구 모음에서 **실행**을 선택 합니다.

   실행이 완료 되 면 디자이너에서 워크플로의 실행 세부 정보를 표시 합니다.

   ![디자이너에서 워크플로의 실행 세부 정보를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. 태스크가 계속 실행 되지 않도록 워크플로를 사용 하지 않도록 설정 하려면 [Azure Portal에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-azure-portal.md)를 참조 하세요.

## <a name="provide-feedback"></a>피드백 제공

사용자 의견을 듣고 싶습니다. 버그를 보고 하거나, 사용자 의견을 제공 하거나,이 미리 보기 기능에 대 한 질문을 하려면 [Azure Logic Apps 팀에 문의 하세요](mailto:logicapps@microsoft.com).

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-azure-portal.md)
