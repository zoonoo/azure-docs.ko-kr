---
title: Azure Stack Edge Pro GPU 장치에서 GPU 공유
description: Azure Stack Edge Pro GPU 장치에서 Gpu를 공유 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565021"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에서 GPU 공유

GPU (그래픽 처리 장치)는 그래픽 렌더링을 가속화 하기 위해 설계 된 특수 한 프로세서입니다. Gpu는 여러 데이터를 동시에 처리 하 여 기계 학습, 비디오 편집 및 게임 응용 프로그램에 유용 하 게 사용할 수 있습니다. 범용 계산을 위한 CPU 외에도 Azure Stack Edge Pro GPU 장치에는 하드웨어 가속 추론 같은 계산 집약적인 워크 로드를 위한 하나 또는 두 개의 Nvidia Tesla T4 Gpu가 포함 될 수 있습니다. 자세한 내용은 [Nvidia의 Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/)를 참조 하세요.


## <a name="about-gpu-sharing"></a>GPU 공유 정보

많은 machine learning 또는 기타 계산 워크 로드에 전용 GPU가 필요 하지 않을 수 있습니다. Gpu를 공유 하 고 컨테이너 화 된 또는 VM 워크 로드 간에 Gpu를 공유 하면 GPU의 성능 이점에 크게 영향을 주지 않고 GPU 사용률을 높일 수 있습니다.  

## <a name="using-gpu-with-vms"></a>Vm에서 GPU 사용

Azure Stack Edge Pro 장치에서 VM 워크 로드를 배포할 때 GPU를 공유할 수 없습니다. GPU는 하나의 VM에만 매핑될 수 있습니다. 즉, 두 개의 gpu를 장착 하는 장치에서 하나의 GPU와 두 개의 Vm이 있는 장치에 하나의 GPU VM만 있을 수 있습니다. 컨테이너 화 된 워크 로드에 대해 Kubernetes가 구성 된 장치에서 GPU Vm을 사용할 때 고려해 야 하는 다른 요소도 있습니다. 자세한 내용은 [GPU vm 및 Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes)을 참조 하세요.


## <a name="using-gpu-with-containers"></a>컨테이너에서 GPU 사용

컨테이너 화 된 워크 로드를 배포 하는 경우 하드웨어 및 소프트웨어 계층에서 여러 가지 방법으로 GPU를 공유할 수 있습니다. Azure Stack Edge Pro 장치에서 Tesla T4 GPU를 사용 하 여 소프트웨어 공유로 제한 됩니다. 장치에서 GPU의 소프트웨어 공유에 대해 다음과 같은 두 가지 방법을 사용 합니다. 

- 첫 번째 방법은 환경 변수를 사용 하 여 시간을 공유 하는 Gpu 수를 지정 하는 것입니다. 이 방법을 사용할 때는 다음 사항에 주의 하십시오.

    - 이 방법을 사용 하 여 Gpu를 하나 또는 둘 다 지정할 수 있습니다. 소수 자릿수 사용은 지정할 수 없습니다.
    - 여러 모듈을 하나의 GPU에 매핑할 수 있지만 동일한 모듈을 둘 이상의 GPU에 매핑할 수 없습니다.
    - Nvidia SMI-S 출력을 사용 하 여 메모리 사용률을 포함 한 전체 GPU 사용률을 확인할 수 있습니다.
    
    자세한 내용은 장치에서 [GPU를 사용 하는 IoT Edge 모듈을 배포](azure-stack-edge-gpu-configure-gpu-modules.md) 하는 방법을 참조 하세요.

- 두 번째 접근 방식에서는 Nvidia Gpu에서 다중 프로세스 서비스를 사용 하도록 설정 해야 합니다. MP는 단일 공유 GPU에서 여러 프로세스를 동시에 실행할 수 있도록 하는 런타임 서비스입니다. MP는 최대 사용률을 얻기 위해 GPU의 여러 프로세스에서 커널 및 memcopy 작업을 겹칠 수 있습니다. 자세한 내용은 [다중 프로세스 서비스](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)를 참조 하세요.

    이 방법을 사용할 때는 다음 사항에 주의 하십시오.
    
    - MP를 사용 하면 GPU 배포에서 더 많은 플래그를 지정할 수 있습니다.
    - MP를 통해 소수 자릿수 사용을 지정 하 여 장치에 배포 된 각 응용 프로그램의 사용량을 제한할 수 있습니다. `env` `deployment.yaml` 다음 매개 변수를 추가 하 여의 섹션에서 각 응용 프로그램에 사용할 GPU 비율을 지정할 수 있습니다. 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU 사용률
 
장치에 배포 된 컨테이너 화 된 워크 로드에서 GPU를 공유 하는 경우 nvidia smi-s (시스템 관리 인터페이스)를 사용할 수 있습니다. Nvidia-smi-s는 Nvidia GPU 장치를 관리 하 고 모니터링 하는 데 도움이 되는 명령줄 유틸리티입니다. 자세한 내용은 [Nvidia 시스템 관리 인터페이스](https://developer.nvidia.com/nvidia-system-management-interface)를 참조 하세요.

GPU 사용량을 보려면 먼저 장치의 PowerShell 인터페이스에 연결 합니다. 명령을 실행 `Get-HcsNvidiaSmi` 하 고 NVIDIA smi-s 출력을 확인 합니다. MP를 사용 하도록 설정 하 고 장치에 여러 작업을 배포 하 여 GPU 사용률이 어떻게 변경 되는 지 확인할 수도 있습니다. 자세한 내용은 [다중 프로세스 서비스 사용](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에서 Kubernetes 배포에 대 한 GPU 공유](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Azure Stack Edge Pro에서 IoT 배포에 대 한 GPU 공유](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
