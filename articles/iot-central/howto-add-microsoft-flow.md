---
title: Microsoft Flow에서 IoT Central 커넥터를 사용하여 워크플로 구축 | Microsoft Docs
description: Microsoft Flow에서 IoT Central 커넥터를 사용하여 워크플로를 트리거하고 워크플로에서 디바이스를 만들고, 업데이트하고, 삭제합니다.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: c06939ccb920954c08ef02f38e6d12471ac267db
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959293"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Microsoft Flow에서 IoT Central 커넥터를 사용하여 워크플로 구축

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

Microsoft Flow를 사용하여 비즈니스 사용자가 의존하는 많은 애플리케이션 및 서비스 전반에서 워크플로를 자동화합니다. Microsoft Flow에서 IoT Central 커넥터를 사용하면 IoT Central에서 규칙이 트리거될 때 워크플로를 트리거할 수 있습니다. IoT Central 또는 다른 응용 프로그램에 의해 트리거되는 워크플로에서 IoT Central 커넥터의 작업을 사용하여 디바이스를 만들고, 디바이스의 속성 및 설정을 업데이트하거나 디바이스를 삭제할 수 있습니다. IoT Central을 모바일 알림 및 Microsoft Teams와 같은 다른 서비스에 연결하는 [이러한 Microsoft Flow 템플릿](https://aka.ms/iotcentralflowtemplates)을 확인하세요.

## <a name="prerequisites"></a>필수 조건

- 종량제 애플리케이션
- Microsoft 개인 또는 회사나 학교 계정을 사용하여 Flow에 로그인합니다([Microsoft Flow에 대한 자세한 정보](https://aka.ms/microsoftflowplans)).

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>규칙이 트리거될 때 워크플로 트리거

이 섹션에서는 IoT Central에서 규칙이 트리거될 때 Flow 모바일 앱에서 모바일 알림을 트리거하는 방법을 설명합니다.

1. [IoT Central에서 규칙을 만들어](howto-create-telemetry-rules.md) 시작합니다. 규칙 조건을 저장한 후 새 작업으로 **Microsoft Flow 작업**을 클릭합니다. Microsoft Flow로 이동할 수 있는 새 탭 또는 창이 브라우저에 열립니다.

    ![새 Microsoft Flow 작업 만들기](media/howto-add-microsoft-flow/createflowaction.PNG)

1. Microsoft Flow에 로그인합니다. IoT Central에서 사용하는 것과 동일한 계정일 필요가 없습니다. 사용자 지정 작업에 연결하는 IoT Central 커넥터를 보여주는 개요 페이지로 연결됩니다.

1. IoT Central 커넥터에 로그인한 다음 **계속**을 클릭합니다. 워크플로를 구축하는 Microsoft Flow 디자이너로 이동됩니다. 워크플로에는 애플리케이션 및 규칙이 이미 채워져 있는 IoT Central 트리거가 있습니다.

1. **+ 새 단계** 및 **작업 추가**를 선택합니다. 이 시점에서 워크플로에 원하는 모든 작업을 추가할 수 있습니다. 예를 들어 모바일 알림을 전송해 보겠습니다. **알림**을 검색하고, **알림 - 모바일 알림 보내기**를 선택합니다.

1. 작업에서 알림에서 알리려는 내용으로 텍스트 필드를 채웁니다. 알림에 디바이스 이름 및 타임스탬프와 같은 중요한 정보를 전달하여 IoT Central 규칙에서 *동적 콘텐츠*를 포함할 수 있습니다.

    > [!NOTE]
    > 동적 콘텐츠 창에서 "자세히 보기" 텍스트를 클릭하여 규칙을 트리거한 측정값 및 속성 값을 가져옵니다.

    ![동적 창을 열어 흐름 편집 작업](./media/howto-add-microsoft-flow/flowdynamicpane.PNG)

1. 작업 편집이 완료되면 **저장**을 클릭합니다. 워크플로의 개요 페이지로 이동됩니다. 여기에서 실행 기록을 보고 다른 동료와 공유할 수 있습니다.

    > [!NOTE]
    > IoT Central 앱의 다른 사용자가 이 규칙을 편집하도록 하려는 경우 Microsoft Flow에서 공유해야 합니다. 워크플로에서 소유자로 해당 Microsoft Flow 계정을 추가합니다.

1. IoT Central 앱으로 다시 이동하면 이 규칙의 작업 영역 아래에 Microsoft Flow 작업이 표시됩니다.

Microsoft Flow에서 IoT Central 커넥터를 사용하여 항상 워크플로 구축을 시작할 수 있습니다. 그런 다음, 연결할 IoT Central 앱 및 규칙을 선택할 수 있습니다.

## <a name="create-a-device-in-a-workflow"></a>워크플로에서 디바이스 만들기

이 섹션에서는 Microsoft Flow 모바일 앱을 사용하여 모바일 디바이스에서 단추 하나만 눌러 IoT Central에 새 디바이스를 만드는 방법을 보여줍니다. Flow에서 이 작업을 사용하여 디바이스가 다른 곳에서 추가될 때 새 디바이스를 만들어 Dynamics와 같은 ERP 시스템을 IoT Central과 통합할 수 있습니다.

1. Microsoft Flow에서 빈 워크플로를 만들어 시작합니다.

1. 트리거로 **모바일용 흐름 단추**를 검색합니다.

1. 이 트리거에서 **디바이스 이름**이라는 텍스트 입력을 추가합니다. 설명 텍스트를 **새 디바이스의 디바이스 이름 입력**으로 변경합니다.

1. 새 작업을 추가합니다. **Azure IoT Central - 장치 만들기** 작업을 검색합니다.

1. 응용 프로그램을 선택하고, 드롭다운 목록에서 디바이스를 만들 디바이스 템플릿을 선택합니다. 디바이스의 모든 속성 및 설정을 표시하는 작업 확장이 표시됩니다.

1. 디바이스 이름 필드를 선택합니다. 동적 콘텐츠 창에서 **디바이스 이름**을 선택합니다. 이 값은 모바일 앱을 통해 사용자가 입력하는 입력에서 전달되며, IoT Central에서 새 디바이스의 이름이 됩니다. 이 예제에서 유일한 필수 필드는 빨간색 별표로 표시되는 디바이스 이름입니다. 다른 디바이스 템플릿에는 새 디바이스를 만들기 위해 채워야 하는 여러 개의 필수 필드가 있을 수 있습니다.

    ![흐름 만들기 디바이스 작업 동적 창](./media/howto-add-microsoft-flow/flowcreatedevice.PNG)
1. (선택 사항) 새 디바이스를 만드는 데 적합한 대로 다른 필드를 채웁니다.

1. 마지막으로 워크플로를 저장합니다.

1. Microsoft Flow 모바일 앱에서 워크플로를 시도합니다. 앱에서 **단추** 탭으로 이동합니다. 단추 -> 새 장치 워크플로 만들기가 표시됩니다. 새 디바이스의 이름을 입력하고, IoT Central에서 나타나는 것을 시청하세요!

    ![흐름 만들기 디바이스 모바일 스크린샷](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>워크플로에서 디바이스 업데이트

이 섹션에서는 Microsoft Flow 모바일 앱을 사용하여 모바일 디바이스에서 단추 하나만 눌러 IoT Central에서 디바이스 설정 및 속성을 업데이트하는 방법을 보여줍니다.

1. Microsoft Flow에서 빈 워크플로를 만들어 시작합니다.

1. 트리거로 **모바일용 흐름 단추**를 검색합니다.

1. 이 트리거에서 변경하려는 설정 또는 속성에 해당하는 **위치** 텍스트 입력과 같은 입력을 추가합니다. 설명 텍스트를 변경합니다.

1. 새 작업을 추가합니다. **Azure IoT Central - 장치 업데이트** 작업을 검색합니다.

1. 드롭다운에서 애플리케이션을 선택합니다. 이제 업데이트하려는 기존 디바이스의 ID가 필요합니다. 브라우저 URL에서 IoT Central 디바이스의 ID를 확인할 수 있습니다.

    ![IoT Central 디바이스 탐색기 디바이스 ID](./media/howto-add-microsoft-flow/iotcdeviceid.PNG)

1. 디바이스 이름을 업데이트할 수 있습니다. 디바이스의 속성 및 설정 중 하나를 업데이트하려면 **디바이스 템플릿** 드롭다운에서 업데이트하려는 디바이스의 디바이스 템플릿을 선택해야 합니다. 작업 타일은 업데이트할 수 있는 모든 속성 및 설정을 표시하도록 확장됩니다.

    ![흐름 업데이트 디바이스 워크플로](./media/howto-add-microsoft-flow/flowupdatedevice.PNG)

1. 업데이트하려는 각 속성 및 설정을 선택합니다. 동적 콘텐츠 창에서 트리거의 해당 입력을 선택합니다. 이 예제에서 위치 값은 디바이스의 위치 속성을 업데이트하도록 아래로 전파됩니다.

1. 마지막으로 워크플로를 저장합니다.

1. Microsoft Flow 모바일 앱에서 워크플로를 시도합니다. 앱에서 **단추** 탭으로 이동합니다. 단추 -> 장치 워크플로 업데이트가 표시됩니다. 입력을 입력하고, IoT Central에서 업데이트되는 디바이스를 확인하세요.

## <a name="delete-a-device-in-a-workflow"></a>워크플로에서 디바이스 삭제

**Azure IoT Central - 장치 삭제** 작업을 사용하여 해당 장치 ID로 장치를 삭제할 수 있습니다. Microsoft Flow 모바일 앱에서 단추 하나만 눌러 디바이스를 삭제하는 예제 워크플로는 다음과 같습니다.

   ![흐름 삭제 디바이스 워크플로](./media/howto-add-microsoft-flow/flowdeletedevice.PNG)
    
## <a name="troubleshooting"></a>문제 해결

Azure IoT Central 커넥터에 연결하는 데 문제가 있는 경우 다음과 같은 일부 팁이 도움이 됩니다.

1. Microsoft 개인 계정(예: @hotmail.com, @live.com, @outlook.com 도메인)은 현재 지원되지 않습니다. AAD 회사 또는 학교 계정을 사용해야 합니다.

2. Microsoft Flow에서 IoT Central 커넥터를 사용하려면 IoT Central 응용 프로그램에 한 번 이상 로그인한 적이 있어야 합니다. 그렇지 않으면 응용 프로그램이 응용 프로그램 드롭다운에 표시되지 않습니다.

3. AAD 계정을 사용하는 동안 오류를 수신하는 경우 Windows PowerShell을 열고 관리자 권한으로 다음 commandlet을 실행합니다.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>다음 단계
이제 Microsoft Flow를 사용하여 워크플로를 구축하는 방법을 배웠으므로 제안하는 다음 단계는 [디바이스를 관리](howto-manage-devices.md)하는 것입니다.

