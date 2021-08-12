---
title: VS Code용 Azure IoT Tools를 사용하여 Azure IoT Hub 만들기 | Microsoft Docs
description: Visual Studio Code용 Azure IoT 도구를 사용하여 리소스 그룹에 Azure IoT Hub를 만드는 방법을 알아봅니다.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: e8d6bebffc46b0b158aeb975ae1e32a863b956d2
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109655879"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Visual Studio Code용 Azure IoT Tools를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

이 문서에서는 [Visual Studio Code용 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)을 사용하여 Azure IoT Hub를 만드는 방법을 보여줍니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- [Visual Studio Code](https://code.visualstudio.com/)

- Visual Studio Code에 대해 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 설치


## <a name="create-an-iot-hub-and-device-in-an-iot-project"></a>IoT Project에서 IoT 허브 및 디바이스 만들기

다음 단계에서는 Visual Studio Code로 IoT Project 안에서 IoT 허브를 만들고 해당 허브에 디바이스를 등록하는 방법을 보여 줍니다.

Azure Portal로부터 Azure IoT Hub와 디바이스를 프로비전하지 않습니다. VS Code를 통해 개발 환경을 그대로 유지하면서 작업할 수 있습니다. 이 섹션의 단계는 수행 방법을 보여 줍니다.

1. 새로 열린 프로젝트 창에서 `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: Azure 서비스 프로비전...** 을 입력하고 선택합니다. 단계별 지침에 따라 Azure IoT Hub 프로비전을 완료하고 IoT 허브 디바이스를 만듭니다.

    ![프로비저닝 명령](media/iot-hub-create-use-iot-toolkit/provision.png)

    > [!NOTE]
    > Azure에 로그인하지 않은 경우 로그인을 위한 팝업 알림을 따릅니다.

1. 사용할 구독을 선택합니다.

    ![구독 선택](media/iot-hub-create-use-iot-toolkit/select-subscription.png)

1. 이제 기존 리소스 그룹을 선택하거나 새 [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)을 만듭니다.

    ![리소스 그룹 선택](media/iot-hub-create-use-iot-toolkit/select-resource-group.png)

1. 지정한 리소스 그룹에서 프롬프트에 따라 기존 IoT 허브를 선택하거나 새 Azure IoT Hub를 만듭니다.

    ![IoT Hub 선택 단계](media/iot-hub-create-use-iot-toolkit/iot-hub-provision.png)

    ![IoT Hub 선택](media/iot-hub-create-use-iot-toolkit/select-iot-hub.png)

    ![선택한 IoT Hub](media/iot-hub-create-use-iot-toolkit/iot-hub-selected.png)

1. 출력 창에 프로비저닝된 Azure IoT Hub가 표시됩니다.

    ![프로비저닝된 IoT Hub](media/iot-hub-create-use-iot-toolkit/iot-hub-provisioned.png)

1. 프로비전된 Azure IoT Hub에서 새 IoT 허브 디바이스를 선택하거나 만듭니다.

    ![IoT 디바이스 선택 단계](media/iot-hub-create-use-iot-toolkit/iot-device-provision.png)

    ![프로비저닝된 IoT 디바이스 선택](media/iot-hub-create-use-iot-toolkit/select-iot-device.png)

1. 이제 Azure IoT Hub가 프로비저닝되고 디바이스가 생성되었습니다. 디바이스 연결 문자열도 VS Code로 저장됩니다.

    ![프로비저닝 완료](media/iot-hub-create-use-iot-toolkit/provision-done.png)



## <a name="create-an-iot-hub-without-an-iot-project"></a>IoT Project 없이 IoT 허브 만들기

다음 단계는 Visual Studio Code에서 IoT Project를 사용하지 않고 IoT 허브를 만드는 방법을 보여 줍니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

2. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다. 

   ![Azure IoT Hub 디바이스 확장](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 마우스로 가리킵니다. 

4. **IoT Hub 만들기** 를 선택택합니다.

5. 처음으로 Azure에 로그인할 수 있게 하는 팝업 메시지가 오른쪽 아래 모서리에 표시됩니다.

6. Azure 구독을 선택합니다. 

7. 리소스 그룹을 선택합니다.

8. 위치를 선택합니다.

9. 가격 책정 계층을 선택합니다.

10. IoT Hub에 대한 전역 고유 이름을 입력합니다.

11. IoT Hub가 만들어질 때까지 몇 분 동안 기다립니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code용 Azure IoT Tools를 사용하여 IoT Hub를 개발했습니다. 더 자세히 알아보려면 다음 문서를 확인하세요.

* [Visual Studio Code용 Azure IoT Tools를 사용하여 디바이스와 IoT Hub 간에 메시지 보내고 받기](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Visual Studio Code용 Azure IoT Tools를 사용한 Azure IoT Hub 디바이스 관리](iot-hub-device-management-iot-toolkit.md)

* [VS Code Wiki 페이지용 Azure IoT Hub를 참조하세요](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
