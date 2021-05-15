---
title: Azure Marketplace에서 Microsoft Azure Stack Edge Pro 디바이스에 GPU 모듈 배포 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스에서 GPU 사용 IoT 모듈을 배포하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568422"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Azure Marketplace에서 GPU 사용 IoT 모듈 배포

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro 디바이스의 Azure Marketplace에서 GPU(그래픽 처리 장치) 지원 IoT Edge 모듈을 배포하는 방법을 설명합니다. 

이 문서에서는 다음 방법을 설명합니다.
  - GPU 모듈을 실행하도록 Azure Stack Edge Pro를 준비합니다.
  - Azure Marketplace에서 GPU 지원 IoT 모듈을 다운로드하고 배포합니다.
  - 모듈 출력을 모니터링합니다.

## <a name="about-sample-module"></a>샘플 모듈 정보

이 문서의 GPU 샘플 모듈에는 GPU에 대한 CPU의 PyTorch 및 TensorFlow 벤치마킹 샘플 코드가 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인합니다.

- GPU 지원 1-노드 Azure Stack Edge 디바이스에 액세스할 수 있습니다. 이 디바이스는 Azure의 리소스를 사용하여 활성화됩니다. 
- 이 디바이스에서 계산을 구성했습니다. [자습서: Azure Stack Edge 디바이스에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 단계를 따릅니다.
- Windows 클라이언트에 다음과 같은 개발 리소스가 있습니다.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)   


## <a name="get-module-from-azure-marketplace"></a>Azure Marketplace에서 모듈 가져오기

1. 모든 [Azure Marketplace의 앱](https://azuremarketplace.microsoft.com/marketplace/apps)을 검색합니다.

    ![Azure Marketplace에서 앱 찾아보기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. **GPU 시작** 을 검색합니다.

    ![GPU 샘플 모듈 검색](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. **지금 받기** 를 선택합니다.

    ![샘플 모듈 가져오기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. **계속** 을 선택하여 공급자의 사용 약관 및 개인정보처리방침을 승인합니다. 

    ![샘플 모듈 2 가져오기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Azure Stack Edge Pro 디바이스를 배포하는 데 사용하는 구독을 선택합니다.

    ![구독 선택](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Azure Stack Edge Pro 디바이스를 구성할 때 만든 IoT Hub 서비스의 이름을 입력합니다. 이 IoT Hub 서비스 이름을 찾으려면 Azure Portal에서 디바이스와 연관된 Azure Stack Edge 리소스로 이동합니다. 

    1. 왼쪽 창 메뉴 옵션에서 **Edge 서비스 > IoT Edge** 로 이동합니다. 

        ![계산 구성 보기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. **속성** 으로 이동합니다. 

        1. Azure Stack Edge Pro 디바이스에서 계산을 구성할 때 생성된 IoT Hub 서비스를 적어둡니다.
        2. 계산을 구성할 때 생성된 IoT Edge 디바이스의 이름을 적어둡니다. 이 이름은 이후 단계에서 사용하게 됩니다.

        ![Edge 컴퓨팅 구성](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. **디바이스에 배포** 를 선택합니다.

11. IoT Edge 디바이스의 이름을 입력하거나 **디바이스 찾기** 를 선택하여 허브에 등록된 디바이스 중 하나를 찾습니다.

    ![디바이스 찾기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. 원하는 경우 다른 모듈 추가를 포함하여 배포 매니페스트를 구성하는 표준 프로세스를 계속하려면 **만들기** 를 선택합니다. 이미지 URI, 만들기 옵션 및 desired 속성과 같은 새 모듈에 대한 세부 정보가 미리 정의되어 있지만 변경할 수 있습니다.

    ![만들기 선택](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. 모듈이 Azure Portal의 IoT Hub에 배포되었는지 확인합니다. 디바이스를 선택하고 **모듈 설정** 을 선택하면 모듈이 **IoT Edge 모듈** 섹션에 나열됩니다.

    ![만들기 2를 선택합니다.](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>모듈 모니터링  

1. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택** 을 실행합니다.

2. 구성하려는 IoT Edge 디바이스가 포함된 구독 및 IoT Hub를 선택합니다. 이 경우 Azure Stack Edge Pro 디바이스를 배포하는 데 사용되는 구독을 선택하고 Azure Stack Edge Pro 디바이스용으로 만든 IoT Edge 디바이스를 선택합니다. 이는 이전 단계에서 Azure Portal을 통해 컴퓨팅을 구성할 때 발생합니다.

3. VS Code 탐색기에서 Azure IoT Hub 섹션을 확장합니다. **디바이스** 아래에 Azure Stack Edge Pro 디바이스에 해당하는 IoT Edge 디바이스가 표시되어야 합니다. 

    1. 해당 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음 **내장 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.
  
        ![모니터링 시작](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **디바이스 > 모듈** 로 이동하면 **GPU 모듈** 이 실행 중인 것을 확인할 수 있습니다.

    3. VS Code 터미널은 또한 IoT Hub 이벤트를 Azure Stack Edge Pro 디바이스에 대한 모니터링 출력으로 표시해야 합니다.

        ![모니터링 출력](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        GPU에서 동일한 작업 세트(모양 변환 5,000회 반복)를 실행하는 데 걸리는 시간이 CPU보다 훨씬 적다는 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모듈을 사용하도록 GPU를 구성](./azure-stack-edge-gpu-configure-gpu-modules.md)하는 방법에 대해 자세히 알아봅니다.