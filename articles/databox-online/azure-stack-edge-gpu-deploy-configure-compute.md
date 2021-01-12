---
title: Azure Stack Edge Pro GPU의 컴퓨팅 기능을 사용하여 데이터를 필터링하고 분석하는 자습서 | Microsoft Docs
description: Azure Stack Edge Pro GPU에 컴퓨팅 역할을 구성하고 이 역할을 사용하여 데이터를 변환한 후에 Azure로 보내는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c884ad6850b8f94baa7c658d685651c3241be33f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935652"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>자습서: Azure Stack Edge Pro GPU 디바이스에 컴퓨팅 구성

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 자습서에서는 Azure Stack Edge Pro 디바이스에서 컴퓨팅 역할을 구성하고 Kubernetes 클러스터를 만드는 방법에 대해 설명합니다. 

이 절차를 완료하는 데 약 20-30분이 걸릴 수 있습니다.


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * Kubernetes 엔드포인트 가져오기

 
## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge Pro 디바이스에서 컴퓨팅 역할을 설정하기 전에 다음 사항을 확인합니다.

- [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명된 대로 Azure Stack Edge Pro 디바이스를 활성화했습니다.
- [컴퓨팅 네트워크 사용](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)의 지침을 따랐는지 확인합니다.
    - 컴퓨팅에 네트워크 인터페이스를 사용하도록 설정했습니다.
    - Kubernetes 노드 IP 및 Kubernetes 외부 서비스 IP를 할당했습니다.

## <a name="configure-compute"></a>컴퓨팅 구성

Azure Stack Edge Pro에 컴퓨팅을 구성하려면 Azure Portal을 통해 IoT Hub 리소스를 만들어야 합니다.

1. Azure Stack Edge 리소스의 Azure Portal에서 **개요** 로 이동하여 **IoT Edge** 를 선택합니다.

   ![컴퓨팅 시작](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-1.png)

2. **IoT Edge 서비스 사용** 에서 **추가** 를 선택합니다.

   ![컴퓨팅 구성](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-2.png)

3. **Edge 컴퓨팅 구성** 블레이드에서 다음 정보를 입력합니다.
   
   |필드  |값  |
   |---------|---------|
   |IoT Hub     | **새로 만들기** 또는 **기존 항목** 중에서 선택합니다. <br> 표준 계층(S1)을 사용하여 IoT 리소스를 만드는 것이 기본입니다. 무료 계층 IoT 리소스를 사용하려면 IoT 리소스를 새로 만든 후 기존 리소스를 선택합니다. <br> 어떤 방법을 선택하든, IoT Hub 리소스는 Azure Stack Edge 리소스에서 사용하는 것과 동일한 구독 및 리소스 그룹을 사용합니다.     |
   |Name     |IoT Hub 리소스의 이름을 입력합니다.         |

   ![컴퓨팅 시작 2](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-3.png)

4. 설정을 마치면 **검토 + 만들기** 를 선택합니다. IoT Hub 리소스에 대한 설정을 검토하고 **만들기** 를 선택합니다.

   IoT Hub 리소스에 대한 리소스 생성은 몇 분 정도 걸립니다. 리소스가 생성된 후 **개요** 는 IoT Edge 서비스가 현재 실행 중임을 나타냅니다.

   ![컴퓨팅 시작 3](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-4.png)

5. Edge 컴퓨팅 역할이 구성되었는지 확인하려면 **속성** 을 선택합니다.

   ![컴퓨팅 시작 4](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-5.png)

   Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. IoT Edge 런타임 역시 이 IoT Edge 디바이스에서 실행됩니다. 이 시점에서 IoT Edge 디바이스에는 Linux 플랫폼만 사용할 수 있습니다.

백그라운드에서 가상 머신과 Kubernetes 클러스터가 생성되기 때문에 컴퓨팅을 구성하는 데 20~30분 정도 걸릴 수 있습니다.

Azure Portal에서 컴퓨팅을 성공적으로 구성한 후에는 Kubernetes 클러스터 및 IoT 네임스페이스(Azure Stack Edge Pro에서 제어하는 시스템 네임 스페이스)와 연결된 기본 사용자가 존재합니다.

## <a name="get-kubernetes-endpoints"></a>Kubernetes 엔드포인트 가져오기

Kubernetes 클러스터에 액세스하도록 클라이언트를 구성하려면 Kubernetes 엔드포인트가 필요합니다. Azure Stack Edge Pro 디바이스의 로컬 UI에서 Kubernetes API 엔드포인트를 가져오려면 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에서 **디바이스** 페이지로 이동합니다.
2. **디바이스 엔드포인트** 에서 **Kubernetes API 서비스** 엔드포인트를 복사합니다. 이 엔드포인트는 `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` 같은 형식의 문자열입니다. 

    ![로컬 UI의 디바이스 페이지](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. 엔드포인트 문자열을 저장합니다. kubectl을 통해 Kubernetes 클러스터에 액세스하도록 클라이언트를 구성할 때 나중에 이 엔드포인트 문자열을 사용합니다.

4. 로컬 웹 UI에 있는 동안 다음을 수행할 수 있습니다.

    - Kubernetes API로 이동하고, **고급 설정** 을 선택하고, Kubernetes에 대한 고급 구성 파일을 다운로드합니다. 

        ![로컬 UI 1의 디바이스 페이지](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Microsoft에서 키를 제공한 경우(일부 사용자에게는 키가 있을 수 있음) 이 구성 파일을 사용할 수 있습니다.

        ![로컬 UI 2의 디바이스 페이지](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - 또한 **Kubernetes 대시보드** 엔드포인트로 이동하고 `aseuser` 구성 파일을 다운로드할 수도 있습니다. 
    
        ![로컬 UI 3의 디바이스 페이지](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        이 구성 파일을 사용하여 Kubernetes 대시보드에 로그인하거나 Kubernetes 클러스터의 문제를 디버그할 수 있습니다. 자세한 내용은 [Kubernetes 대시보드 액세스](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)를 참조하세요. 


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * Kubernetes 엔드포인트 가져오기


Azure Stack Edge Pro 디바이스를 관리하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Stack Edge Pro 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)
