---
title: Microsoft Azure Stack Edge Pro GPU 장치에서 GPU 모듈 실행 | Microsoft Docs
description: Azure Portal를 통해 Azure Stack Edge Pro 장치에서 GPU에 모듈을 구성 하 고 실행 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: dfae1a9b02db7e7b9577acdb47a1ba089f1609e8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439054"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치에서 GPU에 모듈 구성 및 실행

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Azure Stack Edge Pro 장치에 하나 이상의 GPU (그래픽 처리 장치)가 포함 되어 있습니다. GPU는 병렬 처리 기능을 제공하고 CPU(중앙 처리 장치)보다 이미지 렌더링 속도가 빠르기 때문에 AI 컴퓨팅에 널리 사용됩니다. Azure Stack Edge Pro 장치에 포함 된 GPU에 대 한 자세한 내용은 [Edge pro 장치 기술 사양 Azure Stack](azure-stack-edge-gpu-technical-specifications-compliance.md)을 참조 하세요.

이 문서에서는 Azure Stack Edge Pro 장치에서 GPU에 모듈을 구성 하 고 실행 하는 방법을 설명 합니다. 이 문서에서는 Nvidia T4 Gpu 용으로 작성 된 공개적으로 사용 가능한 컨테이너 모듈 **번호** 를 사용 합니다. 이 절차는 이러한 Gpu에 대해 Nvidia에서 게시 한 다른 모듈을 구성 하는 데 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Edge Pro 장치 Azure Stack GPU 사용 1 노드에 액세스할 수 있습니다. 이 디바이스는 Azure의 리소스를 사용하여 활성화됩니다.  

## <a name="configure-module-to-use-gpu"></a>GPU를 사용하도록 모듈 구성

모듈을 실행 하기 위해 Azure Stack Edge Pro 장치에서 GPU를 사용 하도록 모듈을 구성 하려면<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> 다음 단계를 수행 합니다.

1. Azure Portal에서 장치와 연결 된 리소스로 이동 합니다.

2. **개요** 에서 **IoT Edge** 를 선택 합니다.

    ![GPU 1을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. **IoT Edge 서비스 사용** 에서 **추가** 를 선택합니다.

   ![GPU 2를 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. **IoT Edge 서비스 만들기** 에서 IoT Hub 리소스에 대한 설정을 입력합니다.

   |필드   |값    |
   |--------|---------|
   |Subscription      | Azure Stack Edge 리소스에서 사용하는 구독입니다. |
   |Resource group    | Azure Stack Edge 리소스에서 사용하는 리소스 그룹입니다. |
   |IoT Hub           | **새로 만들기** 또는 **기존 항목 사용** 중에서 선택합니다. <br> 표준 계층(S1)을 사용하여 IoT 리소스를 만드는 것이 기본입니다. 무료 계층 IoT 리소스를 사용하려면 IoT 리소스를 새로 만든 후 기존 리소스를 선택합니다. <br> 어떤 방법을 선택하든, IoT Hub 리소스는 Azure Stack Edge 리소스에서 사용하는 것과 동일한 구독 및 리소스 그룹을 사용합니다.     |
   |Name              | 새 IoT Hub 리소스에 제공된 기본 이름을 사용하지 않으려면 다른 이름을 입력합니다. |

   설정을 마치면 **검토 + 만들기** 를 선택합니다. IoT Hub 리소스에 대한 설정을 검토하고 **만들기** 를 선택합니다.

   ![컴퓨팅 시작 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

   IoT Hub 리소스에 대한 리소스 생성은 몇 분 정도 걸립니다. 리소스가 생성된 후 **개요** 는 IoT Edge 서비스가 현재 실행 중임을 나타냅니다.

   ![컴퓨팅 시작 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Edge 컴퓨팅 역할이 구성되었는지 확인하려면 **속성** 을 선택합니다.

   ![컴퓨팅 시작 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

6. **속성** 에서 **IoT Edge 장치** 에 대 한 링크를 선택 합니다.

   ![GPU 6을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

   오른쪽 창에 Azure Stack Edge Pro 장치와 연결 된 IoT Edge 장치가 표시 됩니다. 이 장치는 IoT Hub 리소스를 만들 때 만든 IoT Edge 장치에 해당 합니다.
 
7. 이 IoT Edge 장치를 선택 합니다.

   ![GPU 7을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

8. **모듈 설정** 을 선택합니다.

   ![GPU 8을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

9. **+ 추가** 를 선택 하 고 **+ IoT Edge 모듈** 을 선택 합니다. 

    ![GPU 9를 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

10. **IoT Edge 모듈 추가** 탭에서 다음을 수행 합니다.

    1. **이미지 URI** 를 제공 합니다. 여기에서 공개적으로 제공 되는 Nvidia 모듈 **번호** 를 사용 합니다. 
    
    2. **다시 시작 정책** 을 **항상** 으로 설정 합니다.
    
    3. **원하는 상태** 를 **실행 중** 으로 설정 합니다.
    
    ![GPU 10을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

11. **환경 변수** 탭에서 변수의 이름과 해당 값을 제공 합니다. 

    1. 현재 모듈이이 장치에서 하나의 GPU를 사용 하도록 하려면 NVIDIA_VISIBLE_DEVICES을 사용 합니다. 

    2. 값을 0 또는 1로 설정 합니다. 값이 0 또는 1 이면이 모듈의 장치에서 하나 이상의 GPU를 사용 합니다. 값을 0, 1로 설정 하면이 모듈에서 장치의 Gpu를 모두 사용 하는 것을 의미 합니다.

       ![GPU 11을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

       Nvidia GPU에서 사용할 수 있는 환경 변수에 대 한 자세한 내용은 [nvidia container runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)로 이동 합니다.

    > [!NOTE]
    > GPU는 하나의 모듈에만 매핑될 수 있습니다. 그러나 모듈은 Gpu를 모두 사용 하거나 사용 하지 않을 수 있습니다.

12. 모듈의 이름을 입력합니다. 이제 컨테이너 만들기 옵션을 제공 하 고 모듈 쌍 설정을 수정 하도록 선택할 수 있습니다. 완료 되 면 **추가** 를 선택 합니다. 

    ![GPU 12를 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

13. 모듈이 실행 중인지 확인 하 고 **검토 + 만들기** 를 선택 합니다.

    ![GPU 13을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

14. **검토 + 만들기** 탭에서 선택한 배포 옵션이 표시 됩니다. 옵션을 검토 하 고 **만들기** 를 선택 합니다.
    
    ![GPU 14를 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

15. 모듈의 **런타임 상태** 를 기록 합니다.
    
    ![GPU 15를 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    모듈을 배포 하는 데 몇 분 정도 걸립니다. **새로 고침** 을 선택 하면 **런타임 상태** 업데이트 **실행 중** 이 표시 됩니다.

    ![GPU 16을 사용 하도록 모듈 구성](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>다음 단계

- [NVIDIA GPU에서 사용할 수 있는 환경 변수에](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)대해 자세히 알아보세요.
