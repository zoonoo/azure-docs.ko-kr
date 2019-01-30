---
title: Azure Logic Apps에서 IoT Central 커넥터를 통해 워크플로 작성 | Microsoft Docs
description: Azure Logic Apps에서 IoT Central 커넥터를 사용하여 워크플로를 트리거하고 워크플로의 디바이스를 생성, 업데이트 및 삭제합니다.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 1/3/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 971c01ec8002e73dbc6abfb66a0ede26f90a4d9c
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453210"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Azure Logic Apps에서 IoT Central 커넥터를 통해 워크플로 작성

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

Azure Logic Apps를 사용하여 클라우드 서비스와 온-프레미스 시스템 전반에서 앱과 데이터를 통합하는 자동화된 확장성 있는 워크플로를 작성합니다. Azure Logic Apps에는 많은 Azure 서비스, Microsoft 서비스 및 기타 SaaS(Software-as-a-Service) 제품에 걸쳐 많은 커넥터가 있습니다. Azure Logic Apps에서 IoT Central 커넥터를 사용하면 IoT Central에서 규칙이 트리거될 때 워크플로를 트리거할 수 있습니다. IoT Central 커넥터의 작업을 사용하여 디바이스를 만들거나, 디바이스의 속성과 설정을 업데이트하거나, 디바이스를 삭제할 수도 있습니다. 

IoT Central 커넥터는 Microsoft Flow에서 사용할 수 있습니다. Azure Logic Apps와 Microsoft Flow는 모두 디자이너 중심 통합 서비스이지만 약간 다른 그룹을 대상으로 합니다. Flow를 사용하면 모든 사무직 근로자가 필요한 비즈니스 워크플로를 작성할 수 있습니다. Logic Apps를 사용하면 IT 전문가가 데이터를 연결하는 데 필요한 통합을 작성할 수 있습니다. [여기](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)서는 Flow와 Logic Apps를 비교하고 있습니다. Microsoft Flow에서 IoT Central 커넥터를 사용하여 워크플로를 작성하는 방법은 [여기](howto-add-microsoft-flow.md)서 알아보세요. 

## <a name="prerequisites"></a>필수 조건

- 종량제 애플리케이션
- Azure 계정 및 구독을 통해 논리 앱 만들기 및 관리

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>규칙이 트리거될 때 워크플로 트리거

이 섹션에서는 규칙이 트리거될 때 메시지를 Microsoft Teams에 게시하는 방법을 보여 줍니다. 다른 커넥터를 사용하여 이벤트를 이벤트 허브에 보내거나, 새 Azure DevOps 작업 항목을 만들거나, SQL 서버에 새 행을 삽입하는 등의 작업을 수행하도록 워크플로를 구성할 수 있습니다.

1. [IoT Central에서 규칙을 만들어](howto-create-telemetry-rules.md) 시작합니다. 규칙 조건이 저장되면 새 작업으로 **Azure Logic Apps** 타일을 클릭합니다. **Azure Portal에서 만들기**를 클릭합니다. Azure Portal로 이동하여 새 논리 앱을 만듭니다. Azure 계정에 로그인해야 할 수도 있습니다.

1. 새 논리 앱을 만드는 데 필요한 정보를 입력합니다. Azure 구독을 선택하여 새 논리 앱을 프로비전할 수 있습니다. IoT Central 앱을 만든 것과 동일한 구독일 필요는 없습니다. **만들기**를 클릭합니다.

    ![Azure Portal에서 논리 앱 만들기](./media/howto-build-azure-logic-apps/createinazureportal.PNG)

1. 논리 앱이 성공적으로 만들어지면 논리 앱 디자이너로 자동으로 이동합니다. **비어 있는 논리 앱**을 클릭합니다. 

    ![빈 논리 앱을 만들기](./media/howto-build-azure-logic-apps/blanklogicapp.PNG)

1. 디자이너에서 "iot central"을 검색하고, **규칙이 실행되는 경우** 트리거를 선택합니다. IoT Central 앱에 로그인하는 계정을 사용하여 커넥터에 로그인합니다. 

    ![IoT Central 커넥터에 로그인](./media/howto-build-azure-logic-apps/addtrigger.PNG)

1. 로그인이 성공하면 필드가 표시됩니다. 드롭다운에서 **애플리케이션** 및 **규칙**을 선택합니다.

    ![애플리케이션 및 규칙 선택](./media/howto-build-azure-logic-apps/pickappandrule.PNG)

1. 새 작업을 추가합니다. **메시지 게시 팀**을 검색하고 Microsoft Teams 커넥터에서 **메시지 게시**를 선택합니다. Microsoft Teams에서 사용하는 계정을 사용하여 커넥터에 로그인합니다. 

1. 작업에서 **팀** 및 **채널**을 선택합니다. **메시지** 필드에서 각 메시지의 내용을 입력합니다. 알림에 디바이스 이름 및 타임스탬프와 같은 중요한 정보를 전달하여 IoT Central 규칙에서 *동적 콘텐츠*를 포함할 수 있습니다.
    > [!NOTE]
    > 동적 콘텐츠 창에서 "자세히 보기" 텍스트를 클릭하여 규칙을 트리거한 측정값 및 속성 값을 가져옵니다.

    ![동적 창이 열려 있는 논리 앱 편집 작업](./media/howto-build-azure-logic-apps/buildworkflow.PNG)

1. 작업 편집이 완료되면 **저장**을 클릭합니다.

1. IoT Central 앱으로 돌아가면 [작업] 영역 아래에서 이 규칙에 Azure Logic Apps 작업이 있음을 알 수 있습니다.

언제든지 Azure Portal의 Logic Apps에서 IoT Central 커넥터를 사용하여 워크플로 작성을 시작할 수 있습니다. 그런 다음, 연결할 IoT Central 앱과 규칙을 선택할 수 있으며, 동일한 방식으로 계속 작동합니다. 매번 IoT Central 규칙 페이지에서 시작할 필요가 없습니다.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>워크플로에서 디바이스 만들기, 업데이트 및 삭제

논리 앱에서 워크플로를 작성하는 경우 IoT Central 커넥터를 사용하여 작업을 추가할 수 있습니다. **디바이스 만들기**, **디바이스 업데이트** 및 **디바이스 삭제**를 찾을 수 있습니다.
> [!NOTE]
> **디바이스 업데이트** 및 **디바이스 삭제**의 경우 업데이트하거나 삭제하려는 기존 디바이스의 ID가 필요합니다. 브라우저 URL에서 IoT Central 디바이스의 ID를 확인할 수 있습니다. 이 ID는 탐색기 페이지에 나열된 디바이스 ID와 **다릅니다**.

![IoT Central 디바이스 탐색기 디바이스 ID](./media/howto-build-azure-logic-apps/iotcdeviceid.PNG)
  

## <a name="next-steps"></a>다음 단계
이제 Microsoft Flow를 사용하여 워크플로를 구축하는 방법을 배웠으므로 제안하는 다음 단계는 [디바이스를 관리](howto-manage-devices.md)하는 것입니다.
