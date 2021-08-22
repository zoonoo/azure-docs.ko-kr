---
title: VSCode용 Azure IoT Tools를 사용하여 IT Hub 메시징 관리
description: 디바이스-클라우드 메시지를 모니터링하고 클라우드를 Azure IoT Hub의 디바이스 메시지로 보내기 위해 Azure IoT Tools for Visual Studio Code를 사용하는 방법을 알아봅니다.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 8c311ead11ed41494bf1cff82809ba8f06843226
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113551221"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Azure IoT Tools for Visual Studio Code를 사용하여 디바이스와 IoT Hub 간에 메시지 보내고 받기

![엔드투엔드 다이어그램](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

이 문서에서는 디바이스-클라우드 메시지를 모니터링하고 클라우드-디바이스 메시지를 보내기 위해 Azure IoT Tools for Visual Studio Code를 사용하는 방법을 알아봅니다. 디바이스-클라우드 메시지는 디바이스에서 수집한 다음 IoT Hub로 보내는 센서 데이터일 수 있습니다. 클라우드-디바이스 메시지는 IoT Hub에서 디바이스로 보내 사용자 디바이스에 연결된 LED를 깜박이는 명령일 수 있습니다.

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)는 IoT Hub 관리 및 IoT 애플리케이션 개발을 더욱 용이하게 해주는 유용한 Visual Studio Code 확장입니다. 이 문서에서는 Azure IoT Tools for Visual Studio Code를 사용하여 디바이스와 IoT Hub 간에 메시지를 보내고 받는 방법에 중점을 둡니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>필수 구성 요소

* 활성화된 Azure 구독.

* 구독 중인 Azure IoT Hub

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)(VS Code용 Azure IoT Tools) 또는 이 URL(`vscode:extension/vsciot-vscode.azure-iot-tools`)을 복사하여 브라우저 창에 붙여넣기

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

1. **탐색기** 에서 VS Code를 보고 왼쪽 아래 모서리에서 **Azure IoT Hub 디바이스** 섹션을 확장합니다.

2. 상황에 맞는 메뉴에서 **IoT Hub 선택** 을 클릭합니다.

3. 처음으로 Azure에 로그인할 수 있게 하는 팝업 메시지가 오른쪽 아래 모서리에 표시됩니다.

4. 로그인 후 Azure 구독 목록이 표시되면 IoT Hub 및 Azure 구독을 선택합니다.

5. 잠시 후 디바이스 목록이 **Azure IoT Hub 디바이스** 탭에 표시됩니다.

   > [!Note]
   > **IoT Hub 연결 문자열 설정** 을 선택하여 설정을 완료할 수도 있습니다. 팝업 창에서 IoT 디바이스를 연결할 IoT Hub의 **iothubowner** 정책 연결 문자열을 입력합니다.

## <a name="monitor-device-to-cloud-messages"></a>디바이스-클라우드 메시지 모니터링

디바이스에서 IoT Hub로 보낸 메시지를 모니터링하려면 다음 단계를 수행합니다.

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

2. 모니터링되는 메시지가 **출력** > **Azure IoT Hub** 보기에 표시됩니다.

3. 모니터링을 중지하려면 **출력** 보기를 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 중지** 를 선택합니다.

## <a name="send-cloud-to-device-messages"></a>클라우드-디바이스 메시지 보내기

IoT Hub에서 디바이스로 메시지를 보내려면 다음 단계를 수행합니다.

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **디바이스에 C2D 메시지 보내기** 를 선택합니다.

2. 입력 상자에 메시지를 입력합니다.

3. 결과가 **OUTPUT** > **Azure IoT Hub** 보기에 표시됩니다.

## <a name="next-steps"></a>다음 단계

IoT 디바이스와 Azure IoT Hub 간에 디바이스-클라우드 메시지를 모니터링하고 클라우드-디바이스 메시지를 보내는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]