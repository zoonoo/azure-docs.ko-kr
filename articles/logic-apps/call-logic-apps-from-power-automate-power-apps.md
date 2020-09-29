---
title: 전원 자동화 및 파워 앱에서 논리 앱 호출
description: 논리 앱을 커넥터로 내보내 Microsoft Power에서 논리 앱을 호출 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: 99b13f96bb4676e87b96002fd183376a678d070b
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492524"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>전원 자동화 및 파워 앱에서 논리 앱 호출

Microsoft Power 자동화 및 Microsoft Power Apps에서 논리 앱을 호출 하려면 논리 앱을 커넥터로 내보낼 수 있습니다. 전원 자동화 또는 Power Apps 환경에서 논리 앱을 사용자 지정 커넥터로 노출 하는 경우 흐름에서 논리 앱을 호출할 수 있습니다.

대신 전원 자동 또는 전원에서 Logic Apps 흐름을 마이그레이션하려면 [전원 자동화에서 흐름 내보내기 및 Azure Logic Apps에 배포](export-from-microsoft-flow-logic-app-template.md)를 참조 하세요.

> [!NOTE]
> 모든 전원 자동화 커넥터를 Azure Logic Apps에서 사용할 수 있는 것은 아닙니다. Azure Logic Apps에서 동일한 커넥터가 있는 전원 자동화 흐름과 마이그레이션할 수 있습니다. 예를 들어 단추 트리거, 승인 커넥터 및 알림 커넥터는 전원 자동화에만 적용 됩니다. 현재 전원 자동화의 OpenAPI 기반 흐름은 논리 앱 템플릿으로 내보내기 및 배포에 대해 지원 되지 않습니다.
>
> * 해당 하는 Logic Apps 없는 전원 자동화 커넥터를 찾으려면 [전원 자동화 커넥터](/connectors/connector-reference/connector-reference-powerautomate-connectors)를 참조 하세요.
>
> * 전원 자동화가 해당 하는 Logic Apps 커넥터를 찾으려면 [커넥터 Logic Apps](/connectors/connector-reference/connector-reference-powerautomate-connectors)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 전원 자동화 또는 Power Apps 라이선스.

* 내보낼 요청 트리거가 있는 논리 앱입니다.

* 논리 앱을 호출 하려는 전원 자동화 또는 Power Apps의 흐름입니다.

## <a name="export-your-logic-app-as-a-custom-connector"></a>논리 앱을 사용자 지정 커넥터로 내보내기

전원 자동화 또는 Power Apps에서 논리 앱을 호출 하려면 먼저 논리 앱을 사용자 지정 커넥터로 내보내야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 검색 상자에을 입력 `Logic Apps` 합니다. 결과의 **서비스**에서 **Logic Apps**를 선택 합니다.

1. 내보내려는 논리 앱을 선택 합니다.

1. 논리 앱의 메뉴에서 **내보내기**를 선택 합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="' 내보내기 ' 단추가 선택 된 메뉴를 표시 하는 Azure Portal에서 논리 앱 페이지의 스크린샷":::

1. **내보내기** 창에서 **이름**에 대해 논리 앱에 대 한 사용자 지정 커넥터의 이름을 입력 합니다. **환경** 목록에서 논리 앱을 호출 하려는 전원 자동 또는 전원 앱 환경을 선택 합니다. 완료되면 **확인**을 선택합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="' 내보내기 ' 단추가 선택 된 메뉴를 표시 하는 Azure Portal에서 논리 앱 페이지의 스크린샷":::

1. 논리 앱이 성공적으로 내보내기 되었는지 확인 하려면 알림 창을 확인 합니다.

### <a name="exporting-errors"></a>내보내기 오류

논리 앱을 사용자 지정 커넥터 및 제안 된 솔루션으로 내보낼 때 발생할 수 있는 오류는 다음과 같습니다.

* **환경을 가져오지 못했습니다. 사용자의 계정이 전원 자동화를 사용 하도록 구성 되어 있는지 확인 한 후 다시 시도 하세요.**: Azure 계정에 전원 자동화 계획이 있는지 확인 하세요.

* **현재 논리 앱을 내보낼 수 없습니다. 내보내려면 요청 트리거가 있는 논리 앱을 선택 합니다.**: 논리 앱이 [요청 트리거로](./logic-apps-workflow-actions-triggers.md#request-trigger)시작 하는지 확인 합니다.

## <a name="connect-to-your-logic-app-from-power-automate"></a>전원 자동화에서 논리 앱에 연결

전원 자동화 흐름으로 내보낸 논리 앱에 연결 하려면:

1. [Power 자동화](https://flow.microsoft.com)에 로그인 합니다.

1. **전원 자동화** 홈 페이지 메뉴에서 **내 흐름**을 선택 합니다.

1. **흐름** 페이지에서 논리 앱에 연결 하려는 흐름을 선택 합니다.

1. 흐름 페이지의 메뉴에서 **편집**을 선택 합니다.

1. 흐름 편집기에서 **&#43; 새 단계**를 선택 합니다.

1. **작업 선택**아래의 검색 상자에 논리 앱 커넥터의 이름을 입력 합니다. 필요에 따라 사용자 환경에 사용자 지정 커넥터만 표시 하려면 **사용자 지정** 탭을 선택 하 여 결과를 필터링 합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="' 내보내기 ' 단추가 선택 된 메뉴를 표시 하는 Azure Portal에서 논리 앱 페이지의 스크린샷":::

1. 논리 앱 커넥터를 사용 하 여 수행 하려는 작업을 선택 합니다. 

1. 작업이 논리 앱 커넥터에 전달 하는 정보를 제공 합니다.

1. 변경 내용을 저장 하려면 전원 자동화 편집기 메뉴에서 **저장**을 선택 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Logic Apps 서비스에서 내보낸 논리 앱을 찾습니다.

1. 논리 앱이 전원 자동화 흐름에서 원하는 방식으로 작동 하는지 확인 합니다.

## <a name="delete-logic-app-connector-from-power-automate"></a>전원 자동화에서 논리 앱 커넥터 삭제

1. [Power 자동화](https://flow.microsoft.com)에 로그인 합니다.

1. **전원 자동화** 홈 페이지의 메뉴에서 **데이터** &gt; **사용자 지정 커넥터** 를 선택 합니다.

1. 목록에서 사용자 지정 커넥터를 찾고 줄임표 (**...**) 단추를 클릭 하 여 &gt; **삭제**합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="' 내보내기 ' 단추가 선택 된 메뉴를 표시 하는 Azure Portal에서 논리 앱 페이지의 스크린샷":::

1. 삭제를 확인 하려면 **확인**을 선택 합니다.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Power Apps에서 논리 앱에 연결

Power Apps flow를 사용 하 여 내보낸 논리 앱에 연결 하려면 다음을 수행 합니다.

1. [Power Apps](https://powerapps.microsoft.com/)에 로그인 합니다.

1. **파워 앱** 홈 페이지의 메뉴에서 **흐름** 을 선택 합니다.

1. **흐름** 페이지에서 논리 앱에 연결 하려는 흐름을 선택 합니다.

1. 흐름의 페이지에서 흐름의 메뉴에서 **편집** 을 선택 합니다.

1. 흐름 편집기에서 **&#43; 새 단계** 단추를 선택 합니다.

1. 새 단계의 **작업 선택** 에서 검색 상자에 논리 앱 커넥터의 이름을 입력 합니다. 필요에 따라 **사용자 지정** 탭을 기준으로 결과를 필터링 하 여 환경에서 사용자 지정 커넥터만 표시 합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="' 내보내기 ' 단추가 선택 된 메뉴를 표시 하는 Azure Portal에서 논리 앱 페이지의 스크린샷":::

1. 커넥터를 사용 하 여 수행 하려는 작업을 선택 합니다. 

1. 작업에서 논리 앱 커넥터에 전달 하는 정보를 구성 합니다.

1. Power Apps 편집기 메뉴에서 **저장** 을 선택 하 여 변경 내용을 저장 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Logic Apps 서비스에서 내보낸 논리 앱을 찾습니다.

1. 논리 앱이 Power Apps flow를 사용 하 여 의도 한 대로 작동 하는지 확인 합니다.

## <a name="delete-logic-app-connector-from-power-apps"></a>파워 앱에서 논리 앱 커넥터 삭제

1. [Power Apps](https://powerapps.microsoft.com)에 로그인 합니다.

1. **Power Apps** 홈 페이지의 메뉴에서 **데이터** &gt; **사용자 지정 커넥터** 를 선택 합니다.

1. 목록에서 사용자 지정 커넥터를 찾고 줄임표 (**...**) 단추를 클릭 하 여 &gt; **삭제**합니다.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="' 내보내기 ' 단추가 선택 된 메뉴를 표시 하는 Azure Portal에서 논리 앱 페이지의 스크린샷":::

1. 삭제를 확인 하려면 **확인**을 선택 합니다.
