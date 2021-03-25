---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99821362"
---
Azure Stack Edge Pro에 컴퓨팅을 구성하려면 Azure Portal을 통해 IoT Hub 리소스를 만들어야 합니다.

1. Azure Stack Edge 리소스의 Azure Portal에서 **개요** 로 이동하여 **IoT Edge** 를 선택합니다.

   ![컴퓨팅 시작](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. **IoT Edge 서비스 사용** 에서 **추가** 를 선택합니다.

   ![컴퓨팅 구성](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. **Edge 컴퓨팅 구성** 블레이드에서 다음 정보를 입력합니다.
   
    |필드  |값  |
    |---------|---------|
    |구독     |IoT Hub 리소스에 대한 구독을 선택합니다. Azure Stack Edge 리소스에서 사용하는 것과 동일한 구독을 사용할 수 있습니다.         |
    |Resource group     |IoT Hub 리소스에 대한 리소스 그룹을 선택합니다. Azure Stack Edge 리소스에서 사용하는 것과 동일한 리소스 그룹을 사용할 수 있습니다.         |
    |IoT Hub     | **새로 만들기** 또는 **기존 항목** 중에서 선택합니다. <br> 표준 계층(S1)을 사용하여 IoT 리소스를 만드는 것이 기본입니다. 무료 계층 IoT 리소스를 사용하려면 IoT 리소스를 새로 만든 후 기존 리소스를 선택합니다. <br> 어떤 방법을 선택하든, IoT Hub 리소스는 Azure Stack Edge 리소스에서 사용하는 것과 동일한 구독 및 리소스 그룹을 사용합니다.     |
    |속성     |기본 이름을 적용하거나 IoT Hub 리소스의 이름을 입력합니다.         |

   ![컴퓨팅 시작 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. 설정을 마치면 **검토 + 만들기** 를 선택합니다. IoT Hub 리소스에 대한 설정을 검토하고 **만들기** 를 선택합니다.

   IoT Hub 리소스에 대한 리소스 생성은 몇 분 정도 걸립니다. 리소스가 생성된 후 **개요** 는 IoT Edge 서비스가 현재 실행 중임을 나타냅니다.

   ![컴퓨팅 시작 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Edge 컴퓨팅 역할이 구성되었는지 확인하려면 **IoT Edge > 속성** 을 선택합니다.

   ![컴퓨팅 시작 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. IoT Edge 런타임 역시 이 IoT Edge 디바이스에서 실행됩니다. 이 시점에서 IoT Edge 디바이스에는 Linux 플랫폼만 사용할 수 있습니다.

백그라운드에서 가상 머신과 Kubernetes 클러스터가 생성되기 때문에 컴퓨팅을 구성하는 데 20~30분 정도 걸릴 수 있습니다.

Azure Portal에서 컴퓨팅을 성공적으로 구성한 후에는 Kubernetes 클러스터 및 IoT 네임스페이스(Azure Stack Edge Pro에서 제어하는 시스템 네임 스페이스)와 연결된 기본 사용자가 존재합니다.
