---
title: 워크플로 오류 진단 및 해결
description: Azure Logic Apps에서 워크플로에서 문제, 오류 및 오류를 해결하고 진단하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905024"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 오류를 진단하고 해결

논리 앱은 앱에서 문제를 진단하고 디버깅하도록 도울 수 있는 정보를 생성합니다. Azure Portal을 통해 워크플로의 각 단계를 검토하여 논리 앱을 진단할 수 있습니다. 또는 런타임 디버깅을 위해 워크플로에 몇 가지 단계를 추가할 수 있습니다.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>트리거 기록 확인

각 논리 앱 실행은 트리거 시도로 시작되므로 트리거가 실행되지 않는 경우 다음 단계를 따르십시오.

1. [트리거 기록을 확인하여 트리거의](../logic-apps/monitor-logic-apps.md#review-trigger-history)상태를 확인합니다. 트리거 시도에 대한 자세한 정보를 보려면 트리거 이벤트를 선택합니다.

   ![트리거 상태 및 기록 보기](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 트리거의 입력을 확인하여 예상대로 나타나는지 확인합니다. **입력 링크에서** **입력** 창을 표시하는 링크를 선택합니다.

   트리거 입력에는 트리거가 워크플로를 시작하는 데 필요한 데이터가 포함됩니다. 이러한 입력을 검토하면 트리거 입력이 올바른지 여부와 워크플로를 계속할 수 있도록 조건이 충족되었는지 여부를 확인하는 데 도움이 됩니다.

   예를 들어 `feedUrl` 여기에 있는 속성에 잘못된 RSS 피드 값이 있습니다.

   ![트리거 입력 검토](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 트리거 출력(있는 경우)을 확인하여 예상대로 나타나는지 확인합니다. **출력 링크에서**출력 창을 표시하는 링크를 **선택합니다.**

   트리거 출력에는 트리거가 워크플로의 다음 단계로 전달하는 데이터가 포함됩니다. 이러한 출력을 검토하면 올바른 값또는 예상값이 워크플로의 다음 단계로 전달되는지 여부를 확인하는 데 도움이 됩니다.

   ![오류에 대한 트리거 출력 검토](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 인식하지 못하는 콘텐츠를 찾은 경우 Azure Logic Apps의 [다양한 콘텐츠 유형에](../logic-apps/logic-apps-content-type.md) 대해 자세히 알아보세요.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>실행 기록 확인

트리거가 항목 또는 이벤트에 대해 실행될 때마다 Logic Apps 엔진은 각 항목 또는 이벤트에 대해 별도의 워크플로 인스턴스를 만들고 실행합니다. 실행이 실패하면 다음 단계를 수행하여 워크플로의 각 단계의 상태와 각 단계의 입력 및 출력을 포함하여 해당 실행 중에 발생한 상황을 검토합니다.

1. [실행 기록을 확인하여 워크플로의 실행](../logic-apps/monitor-logic-apps.md#review-runs-history)상태를 확인합니다. 해당 상태의 모든 실행 단계를 포함하여 실패한 실행에 대한 자세한 정보를 보려면 실패한 실행을 선택합니다.

   ![실행 기록 보기 및 실패 한 실행 선택](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 실행의 모든 단계가 나타나면 첫 번째 실패한 단계를 확장합니다.

   ![첫 번째 실패 단계 확장](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 실패한 단계의 입력을 확인하여 예상대로 표시되는지 확인합니다.

1. 특정 실행의 각 단계에 대한 세부 정보를 검토합니다. **실행 기록** 아래에서 검사하려는 실행을 선택합니다.

   ![실행 기록 검토](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![논리 앱 실행에 대한 세부 정보 보기](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 특정 단계에 대한 입력, 출력 및 오류 메시지를 검사하려면 셰이프가 확장되어 세부 정보를 표시하도록 해당 단계를 선택합니다. 예를 들어:

   ![단계 세부 정보 보기](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>런타임 디버깅 수행

디버깅을 돕기 위해 트리거 및 기록 을 검토하는 함께 논리 앱 워크플로에 진단 단계를 추가할 수 있습니다. 예를 들어, HTTP 요청을 검사하고 정확한 해당 크기, 모양 및 형식을 확인할 수 있도록 [Webhook Tester](https://webhook.site/) 서비스를 사용하는 단계를 추가할 수 있습니다.

1. [Webhook 테스터](https://webhook.site/) 사이트로 이동하여 생성된 고유 URL을 복사합니다.

1. 논리 앱에서 HTTP POST 작업과 테스트할 본문 콘텐츠(예: 식 또는 다른 단계 출력)를 추가합니다.

1. 웹후크 테스터의 URL을 HTTP POST 작업에 붙여넣습니다.

1. Logic Apps 엔진에서 생성할 때 요청이 어떻게 형성되는지 검토하려면 논리 앱을 실행하고 Webhook 테스터 사이트를 다시 방문하여 자세한 내용을 확인하십시오.

## <a name="next-steps"></a>다음 단계

* [논리 앱 모니터링](../logic-apps/monitor-logic-apps.md)
