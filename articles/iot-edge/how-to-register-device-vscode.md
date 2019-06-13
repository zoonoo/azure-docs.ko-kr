---
title: Visual Studio Code에서 새 디바이스 등록 - Azure IoT Edge | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Hub에 새 IoT Edge 디바이스 만들기 및 연결 문자열 검색
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495307"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Visual Studio Code에서 새 Azure IoT Edge 디바이스 등록

Azure IoT Edge에서 IoT 디바이스를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 장치를 등록 하면 IoT Edge 워크 로드에 대 한 장치를 설정 하는 연결 문자열을 수신 합니다.

이 문서에서는 VS Code(Visual Studio Code)를 사용하여 새 IoT Edge 디바이스를 등록하는 방법을 보여 줍니다. VS Code에서 대부분의 작업을 수행하는 방법에는 여러 가지가 있습니다. 이 문서에서는 탐색기를 사용 하지만 단계를 실행 하 여 명령 팔레트를 사용할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 작동 하도록 이러한 작업에 대 한 Azure 계정에 로그인 하 고 IoT hub를 선택 해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

1. 탐색기의 맨 아래에 확장 합니다 **Azure IoT Hub** 섹션입니다.

   ![Azure IoT Hub 디바이스 확장 섹션](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. 클릭 된 **...**  에 **Azure IoT Hub** 섹션 헤더입니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.

1. **IoT Hub 선택**을 선택합니다.

1. Azure 계정에 로그인 하지 않은 경우에 이렇게 하려면 지시를 따릅니다.

1. Azure 구독을 선택합니다.

1. IoT Hub를 선택합니다.

## <a name="create-a-device"></a>디바이스 만들기

1. VS Code Explorer에서 **Azure IoT Hub 디바이스** 섹션을 확장합니다.

1. **Azure IoT Hub 디바이스** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.

1. **IoT Edge 디바이스 만들기**를 선택합니다.

1. 열리는 텍스트 상자에 디바이스 ID를 입력합니다.

출력 화면에 명령의 결과가 표시됩니다. 디바이스 정보가 인쇄되며, 여기에는 입력한 **deviceId**와 물리적 디바이스를 IoT Hub에 연결하는 데 사용할 수 있는 **connectionString**이 포함됩니다.

## <a name="view-all-devices"></a>모든 디바이스 보기

IoT hub에 연결 하는 모든 장치에 나열 됩니다는 **Azure IoT Hub** Visual Studio Code Explorer의 섹션입니다. IoT Edge 장치는 다른 아이콘과 팩트를 사용 하 여에 지 장치에서 구분 하는 합니다 **$edgeAgent** 및 **$edgeHub** 모듈은 각 IoT Edge 장치에 배포 합니다.

   ![IoT Hub에서 모든 IoT Edge 디바이스 보기](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. 장치 ID를 마우스 오른쪽 단추로 클릭 합니다 **Azure IoT Hub** 섹션입니다.

1. **디바이스 연결 문자열 복사**를 선택합니다.

   연결 문자열이 클립보드에 복사됩니다.

오른쪽 클릭 메뉴에서 **디바이스 정보 가져오기**를 선택하여 출력 창에서 연결 문자열을 비롯한 모든 디바이스 정보를 볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[Visual Studio Code를 사용하여 디바이스에 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.
