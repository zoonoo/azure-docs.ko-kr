---
title: 워크플로 오류 진단 및 해결
description: 에서 워크플로의 문제, 오류 및 오류를 해결 하 고 문제를 진단 하는 방법에 대해 알아봅니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905024"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 오류를 진단하고 해결

논리 앱은 앱에서 문제를 진단하고 디버깅하도록 도울 수 있는 정보를 생성합니다. Azure Portal을 통해 워크플로의 각 단계를 검토하여 논리 앱을 진단할 수 있습니다. 또는 런타임 디버깅을 위해 워크플로에 몇 가지 단계를 추가할 수 있습니다.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>트리거 기록 확인

각 논리 앱 실행은 트리거 시도로 시작 하므로 트리거가 발생 하지 않으면 다음 단계를 수행 합니다.

1. [트리거 기록을 확인](../logic-apps/monitor-logic-apps.md#review-trigger-history)하 여 트리거의 상태를 확인 합니다. 트리거를 시도 하는 방법에 대 한 자세한 내용을 보려면 해당 트리거 이벤트를 선택 합니다. 예를 들면 다음과 같습니다.

   ![트리거 상태 및 기록 보기](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 트리거의 입력을 확인 하 여 원하는 대로 나타나는지 확인 합니다. **입력 링크**에서 **입력** 창을 표시 하는 링크를 선택 합니다.

   트리거 입력에는 트리거에 필요한 데이터와 워크플로를 시작 하는 데 필요한 데이터가 포함 됩니다. 이러한 입력을 검토 하면 트리거 입력이 올바른지와 조건이 충족 되었는지 여부를 확인 하 여 워크플로를 계속 진행할 수 있습니다.

   예를 들어 `feedUrl` 다음 속성에는 잘못 된 RSS 피드 값이 있습니다.

   ![오류에 대 한 트리거 입력 검토](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 트리거 출력 (있는 경우)을 확인 하 여 원하는 대로 나타나는지 확인 합니다. **출력 링크**에서 **출력** 창을 표시 하는 링크를 선택 합니다.

   트리거 출력에는 트리거에서 워크플로의 다음 단계로 전달 되는 데이터가 포함 됩니다. 이러한 출력을 검토 하면 워크플로의 다음 단계로 올바른 값 또는 예상 값이 전달 되었는지 여부를 확인할 수 있습니다. 예를 들면 다음과 같습니다.

   ![오류에 대 한 트리거 출력 검토](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 인식 하지 못하는 콘텐츠가 있으면 Azure Logic Apps의 [다양 한 콘텐츠 형식](../logic-apps/logic-apps-content-type.md) 에 대해 자세히 알아보세요.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>실행 기록 확인

항목이 나 이벤트에 대해 트리거가 발생 될 때마다 Logic Apps 엔진은 각 항목 또는 이벤트에 대해 별도의 워크플로 인스턴스를 만들고 실행 합니다. 실행에 실패 하는 경우 다음 단계에 따라 워크플로의 각 단계에 대 한 상태와 각 단계의 입력 및 출력을 포함 하 여 해당 실행 중에 발생 한 상황을 검토 합니다.

1. [실행 기록을 확인](../logic-apps/monitor-logic-apps.md#review-runs-history)하 여 워크플로의 실행 상태를 확인 합니다. 실패 한 실행에 대 한 자세한 정보를 보려면 해당 상태에서 실행 되는의 모든 단계를 포함 하 여 실패 한 실행을 선택 합니다.

   ![실행 기록 보기 및 실패 한 실행 선택](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 실행의 모든 단계가 표시 된 후 첫 번째 실패 한 단계를 확장 합니다.

   ![첫 번째 실패 단계 확장](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 실패 한 단계의 입력을 확인 하 여 원하는 대로 표시 되는지 확인 합니다.

1. 특정 실행의 각 단계에 대한 세부 정보를 검토합니다. **실행 기록** 아래에서 검사하려는 실행을 선택합니다.

   ![실행 기록 검토](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![논리 앱 실행에 대한 세부 정보 보기](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 특정 단계에 대한 입력, 출력 및 오류 메시지를 검사하려면 셰이프가 확장되어 세부 정보를 표시하도록 해당 단계를 선택합니다. 예를 들어:

   ![단계 세부 정보 보기](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>런타임 디버깅 수행

디버깅에 도움이 되도록 트리거 및 실행 기록 검토와 함께 논리 앱 워크플로에 진단 단계를 추가할 수 있습니다. 예를 들어, HTTP 요청을 검사하고 정확한 해당 크기, 모양 및 형식을 확인할 수 있도록 [Webhook Tester](https://webhook.site/) 서비스를 사용하는 단계를 추가할 수 있습니다.

1. [Webhook 테스터](https://webhook.site/) 사이트로 이동 하 여 생성 된 고유한 URL을 복사 합니다.

1. 논리 앱에서 HTTP POST 작업과 테스트할 본문 콘텐츠 (예: 식 또는 다른 단계 출력)를 추가 합니다.

1. Webhook 테스터의 URL을 HTTP POST 작업에 붙여넣습니다.

1. Logic Apps 엔진에서 생성 될 때 요청이 형성 되는 방식을 검토 하려면 논리 앱을 실행 하 고 Webhook 테스터 사이트를 다시 방문 하 여 자세한 내용을 확인 하세요.

## <a name="next-steps"></a>다음 단계

* [논리 앱 모니터링](../logic-apps/monitor-logic-apps.md)
