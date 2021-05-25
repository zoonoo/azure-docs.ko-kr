---
title: Azure 리소스를 관리하고 모니터링하는 자동화 작업 만들기
description: Azure Logic Apps에서 실행되는 워크플로를 만들어 Azure 리소스를 관리하고 비용을 모니터링하는 데 도움이 되는 자동화된 작업을 설정합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774921"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>자동화 작업을 만들어 Azure 리소스 관리 및 비용 모니터링(미리 보기)

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 리소스](../azure-resource-manager/management/overview.md#terminology)를 더 쉽게 관리할 수 있도록 리소스 종류에 따라 가용성이 달라지는 자동화 작업 템플릿을 사용하여 특정 리소스 또는 리소스 그룹에 대한 자동화된 관리 작업을 만들 수 있습니다. 예를 들어 [Azure 스토리지 계정](../storage/common/storage-account-overview.md)의 경우 해당 스토리지 계정에 대한 월별 비용을 보내는 자동화 작업을 설정할 수 있습니다. [Azure 가상 머신](https://azure.microsoft.com/services/virtual-machines/)의 경우 미리 정의된 일정에 따라 해당 가상 머신을 설정하거나 해제하는 자동화 작업을 만들 수 있습니다.

내부적으로 자동화 작업은 실제로 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 서비스에서 실행되며 동일한 [가격 책정 요율](https://azure.microsoft.com/pricing/details/logic-apps/) 및 [가격 책정 모델](../logic-apps/logic-apps-pricing.md)을 사용하여 청구되는 워크플로입니다. 작업이 만들어지면 Logic Apps 디자이너에서 작업을 열어 기본 워크플로를 보고 편집할 수 있습니다. 작업에서 하나 이상의 실행이 완료되면 각 실행의 상태, 기록, 입력 및 출력을 검토할 수 있습니다.

이 미리 보기에서 현재 사용 가능한 작업 템플릿은 다음과 같습니다.

| 리소스 종류 | 자동화 작업 템플릿 |
|---------------|---------------------------|
| Azure 리소스 그룹 | **리소스가 삭제되는 경우** |
| 모든 Azure 리소스 | **리소스에 대한 월별 비용 보내기** |
| Azure 가상 머신 | 추가 필수 구성 요소: <p>- **가상 머신 전원 끄기** <br>- **가상 머신 시작** |
| Azure Storage 계정 | 추가 필수 구성 요소: <p>- **오래된 Blob 삭제** |
| Azure Cosmos DB | 또한 <p>- **이메일을 통해 쿼리 결과 보내기** |
|||

이 문서에서는 다음 작업을 완료하는 방법을 보여 줍니다.

* 특정 Azure 리소스에 대한 [자동화 작업을 만듭니다](#create-automation-task).

* 실행 상태, 입력, 출력 및 기타 기록 정보가 포함된 [작업 기록을 검토](#review-task-history)합니다.

* 작업을 업데이트하거나 Logic Apps 디자이너에서 작업의 기본 워크플로를 사용자 지정할 수 있도록 [작업을 편집](#edit-task)합니다.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>자동화 작업은 Azure Automation과 어떻게 다른가요?

현재 리소스 수준에서만 자동화 작업을 만들고, 작업의 실행 기록을 보고, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 서비스에서 제공하는 작업의 기본 논리 앱 워크플로를 편집할 수 있습니다. 자동화 작업은 [Azure Automation](../automation/automation-intro.md)보다 더 기본적이고 가볍습니다.

이에 비해 Azure Automation은 Azure 환경 및 Azure 이외의 환경에서 일관된 관리를 지원하는 클라우드 기반 자동화 및 구성 서비스입니다. 이 서비스는 [Runbook](../automation/automation-runbook-execution.md)을 사용하여 [프로세스를 오케스트레이션하기 위한 프로세스 자동화](../automation/automation-intro.md#process-automation), [변경 추적 및 인벤토리](../automation/change-tracking/overview.md)를 통한 구성 관리, 업데이트 관리, 공유 기능 및 다른 유형의 기능으로 구성됩니다. Automation을 통해 워크로드와 리소스를 배포하고, 운영하고, 서비스를 해제하는 동안 완벽한 제어가 가능합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 관리하려는 Azure 리소스. 이 문서에서는 Azure 스토리지 계정을 예로 사용합니다.

* Office 365 계정 - Office 365 Outlook을 사용하여 이메일을 보내는 예를 수행하려는 경우

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>자동화 작업 만들기

1. [Azure Portal](https://portal.azure.com)에서 관리하려는 리소스를 찾습니다.

1. 리소스 메뉴에서 **Automation** 섹션으로 스크롤하고, **작업** 을 선택합니다.

   ![Azure Portal 및 "Automation" 섹션에서 "작업" 메뉴 항목이 선택된 스토리지 계정 리소스 메뉴를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. **작업** 창에서 **추가** 를 선택하여 작업 템플릿을 선택할 수 있습니다.

   ![도구 모음에서 "추가"가 선택된 스토리지 계정 "작업" 창을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. **작업 추가** 창의 **템플릿 선택** 아래에서 만들려는 작업에 대한 템플릿을 선택합니다. 다음 페이지가 표시되지 않으면 **다음: 인증** 을 선택합니다.

   이 예에서는 **리소스에 대한 월별 비용 보내기** 작업 템플릿을 선택하여 계속합니다.

   !["리소스에 대한 월별 비용 보내기" 및 "다음: 인증" 선택 항목을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. **인증** 아래의 **연결** 섹션에서 작업에 표시되는 모든 연결에 대해 **만들기** 를 선택하여 모든 연결에 대한 인증 자격 증명을 제공할 수 있습니다. 각 작업의 연결 유형은 작업에 따라 다릅니다.

   이 예에서는 이 작업에 필요한 연결 중 하나만 보여 줍니다.

   ![Azure Resource Manager 연결에 대해 선택한 "만들기" 옵션을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. 메시지가 표시되면 Azure 계정 자격 증명으로 로그인합니다.

   !["로그인" 선택 항목을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   성공적으로 인증된 각 연결은 다음 예와 비슷합니다.

   ![성공적으로 만들어진 연결을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. 모든 연결을 인증한 후 다음 페이지가 표시되지 않으면 **다음: 구성** 을 선택합니다.

1. **구성** 아래에서 작업 이름과 작업에 필요한 기타 정보를 제공합니다. 완료되면 **만들기** 를 선택합니다.

   > [!NOTE]
   > 작업 이름은 만든 후에 변경할 수 없으므로 [기본 워크플로를 편집](#edit-task-workflow)하는 경우에도 계속 적용되는 이름을 고려해야 합니다. 기본 워크플로에 대한 변경 내용은 작업 템플릿이 아니라 사용자가 만든 작업에만 적용됩니다.
   >
   > 예를 들어 작업 이름을 `Send monthly cost`로 지정했지만 나중에 매주 실행되도록 기본 워크플로를 편집하는 경우 작업 이름을 `Send weekly cost`로 변경할 수 없습니다.

   이메일 알림을 보내는 작업에는 이메일 주소가 필요합니다.

   ![선택한 작업에 대한 필수 정보를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   자동으로 활성화되어 실행되는 만든 작업이 이제 **자동화 작업** 목록에 표시됩니다.

   ![자동화 작업 목록을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > 작업이 즉시 표시되지 않으면 작업 목록을 새로 고치거나 잠시 기다렸다가 새로 고칩니다. 도구 모음에서 **새로 고침** 을 선택합니다.

   선택한 작업이 실행되면 다음 예와 비슷한 이메일을 받게 됩니다.

   ![작업에서 보낸 이메일 알림을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>작업 기록 검토

작업의 실행 기록을 상태, 입력, 출력 및 기타 정보와 함께 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 검토하려는 작업 기록이 있는 리소스를 찾습니다.

1. 리소스 메뉴의 **설정** 아래에서 **자동화 작업** 을 선택합니다.

1. 작업 목록에서 검토하려는 작업을 찾습니다. 해당 작업의 **실행** 열에서 **보기** 를 선택합니다.

   ![작업 및 선택한 "보기" 옵션을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   **실행 기록** 창에는 작업에 대한 모든 실행이 상태, 시작 시간, 식별자 및 실행 기간과 함께 표시됩니다.

   ![작업의 실행, 상태 및 기타 정보를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   실행에 가능한 상태는 다음과 같습니다.

   | 상태 | Description |
   |--------|-------------|
   | **취소** | 실행하는 동안 작업이 취소되었습니다. |
   | **실패** | 작업에 실패한 하나 이상의 동작이 있지만 실패를 처리하기 위한 후속 동작이 없었습니다. |
   | **실행 중** | 작업이 현재 실행되고 있습니다. |
   | **성공함** | 모든 작업에 성공했습니다. 동작이 실패한 경우에도 작업이 성공적으로 완료될 수 있지만 실패를 처리하기 위한 후속 동작이 있었습니다. |
   | **대기 중** | 작업의 이전 인스턴스가 아직 실행 중이므로 실행이 아직 시작되지 않았으며 일시 중지되었습니다. |
   |||

   자세한 내용은 [실행 기록 검토](../logic-apps/monitor-logic-apps.md#review-runs-history)를 참조하세요.

1. 실행의 각 단계에 대한 상태 및 기타 정보를 보려면 해당 실행을 선택합니다.

   **논리 앱 실행** 창이 열리고, 실행된 기본 워크플로가 표시됩니다.

   * 워크플로는 항상 [*트리거*](../connectors/apis-list.md#triggers)로 시작합니다. 이 작업의 경우 워크플로는 [**되풀이**](../connectors/connectors-native-recurrence.md) 트리거로 시작합니다.

   * 각 단계에는 상태 및 실행 기간이 표시됩니다. 기간이 0초인 단계를 실행하는 데 1초 미만이 걸렸습니다.

   ![실행, 상태 및 실행 기간의 각 단계를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. 각 단계에 대한 입력 및 출력을 검토하려면 확장되는 단계를 선택합니다.

   트리거에서 워크플로가 실행되는 경우만 지정하고 처리하기 위한 후속 동작에 대한 출력을 제공하지 않으므로 이 예에서는 출력이 없는 되풀이 트리거에 대한 입력을 보여 줍니다.

   ![확장된 트리거 및 입력을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   반대로 **이메일 보내기** 동작에는 워크플로의 이전 동작의 입력 및 출력이 있습니다.

   ![확장된 동작, 입력 및 출력을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Azure 리소스에 대한 자동화 작업 컨텍스트와는 별도로 앱, 데이터, 서비스 및 시스템을 통합할 수 있도록 사용자 고유의 자동화된 워크플로를 빌드하는 방법에 대한 자세한 내용은 [빠른 시작: Azure Logic Apps를 사용하여 첫 번째 통합 워크플로 만들기 - Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 참조하세요.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>작업 편집

작업을 변경하려면 다음 옵션을 사용할 수 있습니다.

* 연결 정보 또는 구성 정보(예: 이메일 주소)와 같은 작업 속성을 변경할 수 있도록 [작업을 "인라인"으로 편집](#edit-task-inline)합니다.

* Logic Apps 디자이너에서 [작업의 기본 워크플로를 편집](#edit-task-workflow)합니다.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>"인라인"으로 작업 편집

1. [Azure Portal](https://portal.azure.com)에서 업데이트하려는 작업이 있는 리소스를 찾습니다.

1. 리소스 메뉴의 **Automation** 아래에서 **작업** 을 선택합니다.

1. 작업 목록에서 업데이트하려는 작업을 찾습니다. 작업의 줄임표( **...** ) 메뉴를 열고, **인라인 편집** 을 선택합니다.

   ![열린 줄임표 메뉴 및 선택한 "인라인 편집" 옵션을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   기본적으로 **인증** 탭이 표시되고 기존 연결을 보여 줍니다.

1. 새 인증 자격 증명을 추가하거나 연결에 대해 다른 기존 인증 자격 증명을 선택하려면 연결의 줄임표( **...** ) 메뉴를 열고, **새 연결 추가** 를 선택하거나 사용 가능한 경우 다른 인증 자격 증명을 선택합니다.

   ![인증 탭, 기존 연결 및 선택한 줄임표 메뉴를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. 다른 작업 속성을 업데이트하려면 **다음: 구성** 을 선택합니다.

   이 예의 작업에 대해 편집할 수 있는 유일한 속성은 이메일 주소입니다.

   ![구성 탭을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. 완료되면 **저장** 을 선택합니다.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>작업의 기본 워크플로 편집

자동화 작업에 대한 기본 워크플로를 변경하는 경우 변경 내용은 작업을 만드는 템플릿이 아니라 사용자가 만든 작업 인스턴스에만 영향을 줍니다. 변경 내용이 적용되고 저장되면 원래 작업에 제공한 이름에서 해당 작업을 더 이상 정확하게 설명하지 않을 수 있으므로 다른 이름을 사용하여 작업을 다시 만들어야 할 수도 있습니다.

> [!TIP]
> 복사된 버전을 대신 편집할 수 있도록 기본 워크플로를 복제하는 것이 좋습니다. 이렇게 하면 원래 자동화 작업이 중단되거나 기존 기능을 중단하지 않고 계속 작동하고 실행되는 동안 복사본에서 변경 내용을 적용하고 테스트할 수 있습니다. 변경이 완료되고 새 버전이 성공적으로 실행되는 것에 만족하면 원래 자동화 작업을 사용하지 않도록 설정하거나 삭제하고 복제된 버전을 자동화 작업에 사용할 수 있습니다. 다음 단계에는 워크플로를 복제하는 방법에 대한 정보가 포함됩니다.

1. [Azure Portal](https://portal.azure.com)에서 업데이트하려는 작업이 있는 리소스를 찾습니다.

1. 리소스 메뉴의 **Automation** 아래에서 **작업** 을 선택합니다.

1. 작업 목록에서 업데이트하려는 작업을 찾습니다. 작업의 줄임표( **...** ) 메뉴를 열고, **Logic Apps에서 열기** 를 선택합니다.

   ![열린 줄임표 메뉴 및 선택한 "Logic Apps에서 열기" 옵션을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   작업의 기본 워크플로가 Azure Logic Apps 서비스에서 열리고, 작업에 사용할 수 있는 것과 동일한 실행 기록을 볼 수 있는 **개요** 창이 표시됩니다.

   ![개요 창이 선택된 Azure Logic Apps 보기에서 작업을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Logic Apps 디자이너의 기본 워크플로를 열려면 논리 앱의 메뉴에서 **논리 앱 디자이너** 를 선택합니다.

   ![선택한 "논리 앱 디자이너" 메뉴 옵션 및 기본 워크플로가 포함된 디자이너 화면을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   이제 워크플로 자체를 정의하는 트리거 및 동작 외에도 워크플로의 트리거 및 동작에 대한 속성을 편집할 수 있습니다. 그러나 원래 워크플로가 계속 작동하고 실행되는 동안 복사본에서 변경할 수 있도록 워크플로를 복제하는 단계를 수행하는 것이 가장 좋은 방법입니다.

1. 워크플로를 복제하고 복사된 버전을 대신 편집하려면 다음 단계를 수행합니다.

   1. 논리 앱 워크플로 메뉴에서 **개요** 를 선택합니다.

   1. 개요 창의 도구 모음에서 **복제** 를 선택합니다.

   1. 논리 앱 만들기 창의 **이름** 아래에서 복사한 논리 앱 워크플로에 대한 새 이름을 입력합니다.

      **논리 앱 상태** 를 제외하고 다른 속성은 편집할 수 없습니다. 
      
   1. **논리 앱 상태** 아래에서 변경하는 동안 복제된 워크플로가 실행되지 않도록 **사용 안 함** 을 선택합니다. 변경을 테스트할 준비가 되면 워크플로를 사용하도록 설정할 수 있습니다.

   1. Azure에서 복제된 워크플로의 프로비저닝이 완료되면 Logic Apps 디자이너에서 해당 워크플로를 찾아서 엽니다.

1. 트리거 또는 동작의 속성을 보려면 해당 트리거 또는 동작을 펼칩니다.

   예를 들어 매월이 아니라 매주 실행되도록 되풀이 트리거를 변경할 수 있습니다.

   ![사용 가능한 빈도 옵션을 표시하기 위해 빈도 목록이 열려 있는 펼쳐진 되풀이 트리거를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   되풀이 트리거에 대한 자세한 내용은 [되풀이 트리거를 사용하여 되풀이 작업 및 워크플로 만들기, 예약 및 실행](../connectors/connectors-native-recurrence.md)을 참조하세요. 사용할 수 있는 다른 트리거 및 동작에 대한 자세한 내용은 [Azure Logic Apps용 커넥터](../connectors/apis-list.md)를 참조하세요.

1. 변경 내용을 저장하려면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   ![디자이너 도구 모음 및 선택한 "저장" 명령을 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. 업데이트된 워크플로를 테스트하고 실행하려면 디자이너 도구 모음에서 **실행** 을 선택합니다.

   실행이 완료되면 디자이너에서 워크플로의 실행 세부 정보를 표시합니다.

   ![디자이너에서 워크플로의 실행 세부 정보를 보여 주는 스크린샷](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. 작업이 계속 실행되지 않도록 워크플로를 사용하지 않도록 설정하려면 [Azure Portal에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-azure-portal.md)를 참조하세요.

## <a name="provide-feedback"></a>피드백 제공

사용자의 피드백을 받고 싶습니다! 버그를 보고하거나 피드백을 제공하거나 이 미리 보기 기능에 대해 질문하려면 [Azure Logic Apps 팀에 문의하세요](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-azure-portal.md)
