---
title: Azure Stack Edge Pro GPU 디바이스에서 GPU 공유
description: Azure Stack Edge Pro GPU 디바이스에서 GPU를 공유하는 접근 방식을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/01/2021
ms.author: alkohli
ms.openlocfilehash: b1fd1d77a8b2e6f7da3a88bc4d7f57336d18237c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536128"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 GPU 공유

GPU(그래픽 처리 장치)는 그래픽 렌더링을 가속화하기 위해 설계된 특수 프로세서입니다. GPU는 많은 데이터 조각을 동시에 처리할 수 ​​있으므로 기계 학습, 동영상 편집 및 게임 애플리케이션에 유용합니다. 범용 컴퓨팅을 위한 CPU 외에도 Azure Stack Edge Pro GPU 디바이스에는 하드웨어 가속 추론과 같은 컴퓨팅 집약적 워크로드를 위해 하나 또는 두 개의 Nvidia Tesla T4 GPU가 포함될 수 있습니다. 자세한 내용은 [Nvidia의 Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조하세요.


## <a name="about-gpu-sharing"></a>GPU 공유 정보

많은 기계 학습 또는 기타 컴퓨팅 워크로드에는 전용 GPU가 필요하지 않을 수 있습니다. GPU를 공유할 수 있으며 컨테이너화된 워크로드 또는 VM 워크로드 간에 GPU를 공유하면 GPU의 성능 이점에 큰 영향을 주지 않고 GPU 활용도를 높일 수 있습니다.  

## <a name="using-gpu-with-vms"></a>VM에서 GPU 사용

Azure Stack Edge Pro 디바이스에서 VM 워크로드를 배포할 때 GPU를 공유할 수 없습니다. GPU는 하나의 VM에만 매핑될 수 있습니다. 즉, GPU가 한 개인 디바이스에는 GPU VM이 하나만 있고 GPU가 두 개인 디바이스에는 VM이 ​​두 개만 있을 수 있습니다. 컨테이너화된 워크로드용으로 구성된 Kubernetes가 있는 디바이스에서 GPU VM을 사용할 때 고려해야 할 다른 요소도 있습니다. 자세한 내용은 [GPU VM 및 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)를 참조하세요.


## <a name="using-gpu-with-containers"></a>컨테이너에서 GPU 사용

컨테이너화된 워크로드를 배포하는 경우 하드웨어 및 소프트웨어 계층에서 여러 가지 방법으로 GPU를 공유할 수 있습니다. Azure Stack Edge Pro 디바이스에서 Tesla T4 GPU를 사용하면 소프트웨어 공유로 제한됩니다. 디바이스에서 다음 두 가지 접근 방식을 사용하여 GPU의 소프트웨어를 공유할 수 있습니다. 

- 첫 번째 접근 방식은 환경 변수를 사용하여 시간을 공유할 수 있는 GPU 수를 지정하는 것입니다. 이 접근 방식을 사용할 때 다음 사항에 주의하세요.

    - 이 방법을 사용하여 GPU를 하나 또는 둘 다 지정할 수 있습니다. 소수 자릿수 사용은 지정할 수 없습니다.
    - 여러 모듈을 하나의 GPU에 매핑할 수 있지만 같은 모듈을 둘 이상의 GPU에 매핑할 수 없습니다.
    - Nvidia SMI 출력을 통해 메모리 사용률을 포함한 전체 GPU 사용률을 볼 수 있습니다.
    
    자세한 내용은 디바이스에서 [GPU를 사용하는 IoT Edge 모듈을 배포](azure-stack-edge-gpu-configure-gpu-modules.md)하는 방법을 참조하세요.

- 두 번째 접근 방식에서는 Nvidia GPU에서 다중 프로세스 서비스를 사용하도록 설정해야 합니다. MPS는 CUDA를 사용하는 여러 프로세스를 단일 공유 GPU에서 동시에 실행할 수 있는 런타임 서비스입니다. MPS는 최대 사용률을 얻기 위해 GPU의 여러 다른 프로세스에서 커널 및 메모리 작업을 겹칠 수 있습니다. 자세한 내용은 [다중 프로세스 서비스](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)를 참조하세요.

    이 접근 방식을 사용할 때 다음 사항에 주의하세요.
    
    - MPS를 사용하면 GPU 배포에서 더 많은 플래그를 지정할 수 있습니다.
    - MPS를 통해 부분 사용량을 지정하여 디바이스에 배포된 각 애플리케이션의 사용량을 제한할 수 있습니다. 다음 매개 변수를 추가하여 `deployment.yaml`의 `env` 섹션에서 각 앱에 사용할 GPU 백분율을 지정할 수 있습니다. 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU 사용률
 
디바이스에 배포된 컨테이너화된 워크로드에서 GPU를 공유할 때 Nvidia 시스템 관리 인터페이스(nvidia-smi)를 사용할 수 있습니다. Nvidia-smi는 Nvidia GPU 디바이스를 관리하고 모니터링하는 데 도움이 되는 명령줄 유틸리티입니다. 자세한 내용은 [Nvidia 시스템 관리 인터페이스](https://developer.nvidia.com/nvidia-system-management-interface)를 참조하세요.

GPU 사용량을 보려면 먼저 디바이스의 PowerShell 인터페이스에 연결합니다. `Get-HcsNvidiaSmi` 명령을 실행하고 Nvidia SMI 출력을 봅니다. MPS를 사용으로 설정한 다음 디바이스에 여러 워크로드를 배포하여 GPU 사용률이 변경되는 방식도 볼 수 있습니다. 자세한 내용은 [다중 프로세스 서비스 사용](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에서 Kubernetes 배포를 위해 GPU를 공유](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)합니다.
- [Azure Stack Edge Pro에서 IoT 배포를 위해 GPU를 공유](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md)합니다.
