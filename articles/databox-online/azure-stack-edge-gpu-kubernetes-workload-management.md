---
title: Azure Stack Edge Pro 장치에서 Kubernetes 워크 로드 관리 이해 | Microsoft Docs
description: Azure Stack Edge Pro 장치에서 Kubernetes 워크 로드를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 67110a2a2bd7f34c735edd126cfc655f45247fc2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560236"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치에서 워크 로드 관리 Kubernetes

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro 장치에서 계산 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터가 만들어지면 컨테이너 화 된 응용 프로그램을 Pod의 Kubernetes 클러스터에 배포할 수 있습니다. Kubernetes 클러스터에서 작업을 배포하는 방법에는 여러 가지가 있습니다. 

이 문서에서는 Azure Stack Edge Pro 장치에 워크 로드를 배포 하는 데 사용할 수 있는 다양 한 방법을 설명 합니다.

## <a name="workload-types"></a>작업 유형

Azure Stack Edge Pro 장치에 배포할 수 있는 두 가지 일반적인 작업 유형은 상태 비저장 응용 프로그램 또는 상태 저장 응용 프로그램입니다.

- 상태 **비저장 응용 프로그램** 은 상태를 유지 하지 않고 영구 저장소에 데이터를 저장 하지 않습니다. 모든 사용자 및 세션 데이터는 클라이언트에 유지 됩니다. 상태 비저장 응용 프로그램의 몇 가지 예로는 웹 프런트 엔드 (예: Nginx) 및 기타 웹 응용 프로그램이 있습니다.

    Kubernetes 배포를 만들어 클러스터에 상태 비저장 응용 프로그램을 배포할 수 있습니다. 

- 상태 저장 **응용 프로그램** 은 해당 상태를 저장 해야 합니다. 상태 저장 응용 프로그램은 영구적 볼륨과 같은 영구 저장소를 사용 하 여 서버 또는 다른 사용자가 사용할 데이터를 저장 합니다. 상태 저장 응용 프로그램의 예로는 [AZURE SQL Edge](../azure-sql-edge/overview.md) 및 MongoDB와 같은 데이터베이스가 있습니다.

    Kubernetes 배포를 만들어 상태 저장 응용 프로그램을 배포할 수 있습니다. 

## <a name="deployment-flow"></a>배포 흐름

Azure Stack Edge Pro 장치에 응용 프로그램을 배포 하려면 다음 단계를 수행 합니다. 
 
1. **액세스 구성**: 먼저 PowerShell runspace를 사용 하 여 사용자를 만들고, 네임 스페이스를 만들고, 해당 네임 스페이스에 대 한 사용자 액세스 권한을 부여 합니다.
2. **저장소 구성**: 다음에는 Azure Portal에서 Azure Stack Edge 리소스를 사용 하 여 배포할 상태 저장 응용 프로그램에 대해 정적 또는 동적 프로 비전을 사용 하는 영구 볼륨을 만듭니다.
3. **네트워킹 구성**: 마지막으로 서비스를 사용 하 여 외부 및 Kubernetes 클러스터 내에서 응용 프로그램을 노출 합니다.
 
## <a name="deployment-types"></a>배포 형식

작업을 배포 하는 기본 방법에는 세 가지가 있습니다. 이러한 각 배포 방법론을 사용 하면 장치의 고유한 네임 스페이스에 연결한 다음 상태 비저장 또는 상태 저장 응용 프로그램을 배포할 수 있습니다.

![Kubernetes 워크 로드 배포](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **로컬 배포**:이 배포는 `kubectl` Kubernetes를 배포 하는 데 사용할 수 있는 명령줄 액세스 도구를 통해 수행 됩니다 `yamls` . 파일을 통해 Azure Stack Edge Pro에서 Kubernetes 클러스터에 액세스 합니다 `kubeconfig` . 자세한 내용은 [kubectl를 통해 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조 하세요.

- **IoT Edge 배포**: IoT Edge를 통해 Azure IoT Hub에 연결 합니다. 네임 스페이스를 통해 Azure Stack Edge Pro 장치에서 Kubernetes 클러스터에 연결 합니다 `iotedge` . 이 네임스페이스에 배포된 IoT Edge 에이전트는 Azure에 대한 연결을 담당합니다. `IoT Edge deployment.json`Azure DevOps CI/CD를 사용 하 여 구성을 적용 합니다. 네임 스페이스 및 IoT Edge 관리는 클라우드 운영자를 통해 수행 됩니다.

- **Azure arc 사용 Kubernetes 배포**: azure Arc enabled Kubernetes는 Kubernetes 클러스터에 응용 프로그램을 배포 하는 데 사용할 수 있는 하이브리드 관리 도구입니다. 를 통해 Azure Stack Edge Pro 장치에서 Kubernetes 클러스터에 연결 `azure-arc namespace` 합니다. 이 네임 스페이스에 배포 된 에이전트는 Azure에 대 한 연결을 담당 합니다. GitOps 기반 구성 관리를 사용 하 여 배포 구성을 적용 합니다. 
    
    Azure Arc enabled Kubernetes를 사용 하면 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터를 보고 모니터링할 수 있습니다. 자세한 내용은 [Azure Arc 사용 Kubernetes?](../azure-arc/kubernetes/overview.md)을 참조 하세요.
    
    2021 년 3 월부터 Azure Arc enabled Kubernetes는 사용자에 게 일반적으로 제공 되며 표준 사용 요금이 적용 됩니다. 값 미리 보기 고객은 Azure Arc 사용 Kubernetes을 Azure Stack Edge 장치에 무료로 사용할 수 있습니다. 미리 보기 제안을 이용 하려면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)를 만듭니다.

    1. **문제점 유형** 에서 **청구** 를 선택합니다.
    2. **구독** 아래에서 구독을 선택합니다.
    3. **서비스** 에서 **내 서비스** 를 선택 하 고 **Azure Stack Edge** 를 선택 합니다.
    4. **리소스** 에서 리소스를 선택 합니다.
    5. **요약** 아래에서 문제에 대 한 설명을 입력 합니다.
    6. **문제 유형** 에서 **예기치 않은 요금** 을 선택 합니다.
    7. **문제 하위 유형** 에서 **내 무료 평가판에 대 한 요금을 이해 하도록 지원** 을 선택 합니다.


## <a name="choose-the-deployment-type"></a>배포 유형 선택

응용 프로그램을 배포 하는 동안 다음 정보를 고려 하십시오.

- **단일 또는 여러 형식**: 단일 배포 옵션이 나 다양 한 배포 옵션의 조합을 선택할 수 있습니다.
- **클라우드 및 로컬**: 응용 프로그램에 따라 IoT Edge 및 Azure Arc를 통해 kubectl 또는 클라우드 배포를 통해 로컬 배포를 선택할 수 있습니다. 
    - 로컬 배포를 선택 하면 Azure Stack Edge Pro 장치가 배포 된 네트워크로 제한 됩니다.
    - 배포할 수 있는 클라우드 에이전트가 있는 경우 클라우드 운영자를 배포 하 고 클라우드 관리를 사용 해야 합니다.
- **IoT Vs Azure Arc**: 배포를 선택 하는 것은 제품 시나리오의 의도에 따라서도 달라 집니다. IoT 또는 IoT 에코 시스템을 보다 긴밀 하 게 통합 하는 응용 프로그램 또는 컨테이너를 배포 하는 경우 IoT Edge를 선택 하 여 응용 프로그램을 배포 합니다. 기존 Kubernetes 배포가 있는 경우 Azure Arc가 선호 하는 선택입니다.


## <a name="next-steps"></a>다음 단계

Kubectl를 통해 응용 프로그램을 로컬로 배포 하려면 다음을 참조 하세요.

- [Kubectl를 통해 Azure Stack Edge Pro에 상태 비저장 응용 프로그램을 배포](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)합니다.

IoT Edge를 통해 앱을 배포 하려면 다음을 참조 하세요.

- [IoT Edge를 통해 Azure Stack Edge Pro에 샘플 모듈을 배포](azure-stack-edge-gpu-deploy-sample-module.md)합니다.

Azure Arc를 통해 앱을 배포 하려면 다음을 참조 하세요.

- [Azure Arc를 사용 하 여 응용 프로그램을 배포](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)합니다.