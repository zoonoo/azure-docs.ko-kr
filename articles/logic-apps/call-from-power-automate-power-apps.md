---
title: Power Automate 및 Power Apps에서 논리 앱 호출
description: 논리 앱을 커넥터로 내보내 Microsoft Power Automate 흐름에서 논리 앱을 호출합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 0c66f4bd7e311bdaedefe68327fc34d373fc6fec
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301657"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Power Automate 및 Power Apps에서 논리 앱 호출

Microsoft Power Automate 및 Microsoft Power Apps에서 논리 앱을 호출하려면 논리 앱을 커넥터로 내보낼 수 있습니다. Power Automate 또는 Power Apps 환경에서 논리 앱을 사용자 지정 커넥터로 노출하는 경우 흐름에서 논리 앱을 호출할 수 있습니다.

Power Automate 또는 Power Apps에서 Logic Apps로 흐름을 마이그레이션하려는 경우 [Power Automate에서 흐름 내보내기 및 Azure Logic Apps에 배포](export-from-microsoft-flow-logic-app-template.md)를 참조하세요.

> [!NOTE]
> Azure Logic Apps에서 모든 Power Automate 커넥터를 사용할 수 있는 것은 아닙니다. Azure Logic Apps에 동등한 커넥터가 있는 Power Automate 흐름만 마이그레이션할 수 있습니다. 예를 들어 단추 트리거, 승인 트리거, 알림 커넥터는 Power Automate에만 적용됩니다. 현재 Power Automate의 OpenAPI 기반 흐름은 논리 앱 템플릿으로 내보내기 및 배포가 지원되지 않습니다.
>
> * Logic Apps에 동등한 커넥터가 없는 Power Automate 커넥터를 확인하려면 [Power Automate 커넥터](/connectors/connector-reference/connector-reference-powerautomate-connectors)를 참조하세요.
>
> * Power Automate에 동등한 커넥터가 없는 Logic Apps 커넥터를 확인하려면 [Logic Apps 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Power Automate 또는 Power Apps 라이선스

* 내보낼 논리 앱(요청 트리거 포함)

* 논리 앱을 호출할 Power Automate 또는 Power Apps의 흐름

## <a name="export-your-logic-app-as-a-custom-connector"></a>논리 앱을 사용자 지정 커넥터로 내보내기

Power Automate 또는 Power Apps에서 논리 앱을 호출하려면 먼저 논리 앱을 사용자 지정 커넥터로 내보내야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal의 검색 상자에 `Logic Apps`를 입력합니다. 결과의 **서비스** 에서 **Logic Apps** 를 선택합니다.

1. 내보낼 논리 앱을 선택합니다.

1. 논리 앱의 메뉴에서 **내보내기** 를 선택합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="‘내보내기’ 단추가 선택된 메뉴를 표시하는 Azure Portal의 논리 앱 페이지 스크린샷":::

1. **내보내기** 창의 **이름** 에 논리 앱에 대한 사용자 지정 커넥터의 이름을 입력합니다. **환경** 목록에서 논리 앱을 호출할 Power Automate 또는 Power Apps 환경을 선택합니다. 완료되면 **확인** 을 선택합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="사용자 지정 커넥터 이름 및 환경의 필수 필드가 표시된 논리 앱 내보내기 창 스크린샷":::

1. 논리 앱을 성공적으로 내보냈는지 확인하려면 알림 창을 살펴봅니다.

### <a name="exporting-errors"></a>내보내기 오류

논리 앱을 사용자 지정 커넥터 및 제안된 솔루션으로 내보낼 때 발생할 수 있는 오류는 다음과 같습니다.

* **환경을 가져오지 못했습니다. 사용자 계정이 Power Automate에 대해 구성되어 있는지 확인한 후 다시 시도하세요.** : Azure 계정에 Power Automate 플랜이 있는지 확인합니다.

* **현재 논리 앱을 내보낼 수 없습니다. 내보내려면 요청 트리거가 있는 논리 앱을 선택합니다.** : 논리 앱이 [요청 트리거](./logic-apps-workflow-actions-triggers.md#request-trigger)로 시작되는지 확인합니다.

## <a name="connect-to-your-logic-app-from-power-automate"></a>Power Automate에서 논리 앱에 연결

Power Automate 흐름을 사용하여 내보낸 논리 앱에 연결하려면 다음을 수행합니다.

1. [Power Automate](https://flow.microsoft.com)에 로그인합니다.

1. **Power Automate** 홈페이지 메뉴에서 **내 흐름** 을 선택합니다.

1. **흐름** 페이지에서 논리 앱에 연결할 흐름을 선택합니다.

1. 흐름 페이지의 메뉴에서 **편집** 을 선택합니다.

1. 흐름 편집기에서 **&#43; 새 단계** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 논리 앱 커넥터의 이름을 입력합니다. 필요에 따라 사용자 환경에 사용자 지정 커넥터만 표시하려면 **사용자 지정** 탭을 선택하여 결과를 필터링합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="사용자 지정 커넥터에 대해 추가되는 새 단계 및 사용 가능한 작업이 표시된 Power Automate 흐름 편집기 스크린샷":::

1. 논리 앱 커넥터를 사용하여 수행하려는 작업을 선택합니다. 

1. 작업이 논리 앱 커넥터에 전달하는 정보를 제공합니다.

1. 변경 내용을 저장하려면 Power Automate 편집기 메뉴에서 **저장** 을 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Logic Apps 서비스에서 내보낸 논리 앱을 찾습니다.

1. 논리 앱이 Power Automate 흐름에서 예상대로 작동하는지 확인합니다.

## <a name="delete-logic-app-connector-from-power-automate"></a>Power Automate에서 논리 앱 커넥터 삭제

1. [Power Automate](https://flow.microsoft.com)에 로그인합니다.

1. **Power Automate** 홈페이지의 메뉴에서 **데이터** &gt; **사용자 지정 커넥터** 를 선택합니다.

1. 목록에서 사용자 지정 커넥터를 찾은 다음 줄임표( **...** ) 단추 &gt; **삭제** 를 선택합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="논리 앱의 사용자 지정 커넥터 관리 단추가 표시된 Power Automate ‘사용자 지정 커넥터’ 페이지 스크린샷":::

1. 삭제를 확인하려면 **확인** 을 선택합니다.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Power Apps에서 논리 앱에 연결

Power Apps 흐름을 사용하여 내보낸 논리 앱에 연결하려면 다음을 수행합니다.

1. [Power Apps](https://powerapps.microsoft.com/)에 로그인합니다.

1. **Power Apps** 홈페이지의 메뉴에서 **흐름** 을 선택합니다.

1. **흐름** 페이지에서 논리 앱에 연결할 흐름을 선택합니다.

1. 흐름 페이지의 흐름 메뉴에서 **편집** 을 선택합니다.

1. 흐름 편집기에서 **&#43; 새 단계** 단추를 선택합니다.

1. 새 단계의 **작업 선택** 아래 검색 상자에 논리 앱 커넥터의 이름을 입력합니다. 필요에 따라 사용자 환경에 사용자 지정 커넥터만 표시하려면 **사용자 지정** 탭을 기준으로 결과를 필터링합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="사용자 지정 커넥터에 대해 추가되는 새 단계 및 사용 가능한 작업이 표시된 Power Apps 흐름 편집기 스크린샷":::

1. 커넥터를 사용하여 수행하려는 작업을 선택합니다. 

1. 작업이 논리 앱 커넥터에 전달하는 정보를 구성합니다.

1. Power Apps 편집기 메뉴에서 **저장** 을 선택하여 변경 내용을 저장합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Logic Apps 서비스에서 내보낸 논리 앱을 찾습니다.

1. 논리 앱이 Power Apps 흐름에서 의도한 대로 작동하는지 확인합니다.

## <a name="delete-logic-app-connector-from-power-apps"></a>Power Apps에서 논리 앱 커넥터 삭제

1. [Power Apps](https://powerapps.microsoft.com)에 로그인합니다.

1. **Power Apps** 홈페이지의 메뉴에서 **데이터** &gt; **사용자 지정 커넥터** 를 선택합니다.

1. 목록에서 사용자 지정 커넥터를 찾은 다음 줄임표( **...** ) 단추 &gt; **삭제** 를 선택합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="논리 앱의 사용자 지정 커넥터 관리 단추가 표시된 Power Apps ‘사용자 지정 커넥터’ 페이지 스크린샷":::

1. 삭제를 확인하려면 **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)의 자세한 내용을 알아봅니다.
* [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에 대해 자세히 알아봅니다.
