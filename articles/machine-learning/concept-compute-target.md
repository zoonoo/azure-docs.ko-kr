---
title: 계산 대상 이란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 모델을 학습 하거나 배포할 위치를 정의 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 72cdfe2ebe79abb9649ac497a6ecddda8918b951
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322294"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning에서 컴퓨팅 대상은 무엇인가요?

*계산 대상은* 교육 스크립트를 실행 하거나 서비스 배포를 호스트 하는 지정 된 계산 리소스 또는 환경입니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 일 수 있습니다. 계산 대상을 사용 하면 나중에 코드를 변경할 필요 없이 계산 환경을 쉽게 변경할 수 있습니다.

일반적인 모델 개발 수명 주기에서 다음과 같은 작업을 할 수 있습니다.

1. 먼저 적은 양의 데이터를 개발 하 고 시험해 볼 것입니다. 이 단계에서 로컬 컴퓨터 또는 클라우드 기반 VM (가상 머신)과 같은 로컬 환경을 계산 대상으로 사용 합니다.
1. 이러한 [학습 계산 목표](#train)중 하나를 사용 하 여 대규모 데이터를 확장 하거나 분산 교육을 수행 합니다.
1. 모델이 준비 되 면 다음 [배포 계산 대상](#deploy)중 하나를 사용 하 여 웹 호스팅 환경 또는 IoT 장치에 배포 합니다.

계산 대상에 사용 하는 계산 리소스는 [작업 영역](concept-workspace.md)에 연결 됩니다. 로컬 컴퓨터 이외의 계산 리소스는 작업 영역의 사용자가 공유 합니다.

## <a name="training-compute-targets"></a><a name="train"></a> 계산 대상 학습

Azure Machine Learning에는 다양한 컴퓨팅 대상에 대한 다양한 지원이 포함되어 있습니다. 일반적인 모델 개발 수명 주기는 적은 양의 데이터를 개발 하거나 실험 하는 것으로 시작 합니다. 이 단계에서는 로컬 컴퓨터 또는 클라우드 기반 VM과 같은 로컬 환경을 사용 합니다. 대규모 데이터 집합에 대 한 학습을 확장 하거나 분산 된 학습을 수행할 때 Azure Machine Learning compute를 사용 하 여 실행을 제출할 때마다 자동으로 크기를 조정 하는 단일 또는 다중 노드 클러스터를 만듭니다. 다양 한 시나리오에 대 한 지원이 다를 수 있지만 고유한 계산 리소스를 연결할 수도 있습니다.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[계산 대상에 학습 실행을 제출](how-to-set-up-training-targets.md)하는 방법에 대해 자세히 알아보세요.

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> 유추를 위한 계산 대상

다음 계산 리소스를 사용 하 여 모델 배포를 호스트할 수 있습니다.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

유추를 수행 하는 경우 Azure Machine Learning 사용 하는 데 필요한 모델 및 관련 리소스를 호스팅하는 Docker 컨테이너를 만듭니다. 이 컨테이너는 다음 배포 시나리오 중 하나에서 사용 됩니다.

* 실시간 유추에 사용 되는 *웹 서비스* 입니다. 웹 서비스 배포는 다음 계산 대상 중 하나를 사용 합니다.

    * [로컬 컴퓨터](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning 컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (미리 보기). 함수에 대 한 배포는 Docker 컨테이너를 빌드하는 Azure Machine Learning에만 의존 합니다. 여기에서 함수를 사용 하 여 배포 됩니다. 자세한 내용은 [Azure Functions에 machine learning 모델 배포 (미리 보기)](how-to-deploy-functions.md)를 참조 하세요.

* 데이터 일괄 처리를 주기적으로 처리 하는 데 사용 되는 _일괄 처리 유추_ 끝점입니다. Batch 추론는 [계산 클러스터 Azure Machine Learning](how-to-create-attach-compute-cluster.md)를 사용 합니다.

* _IoT 장치로_ (미리 보기). IoT 장치에 배포 하는 Azure Machine Learning에만 Docker 컨테이너를 빌드하는 데 사용 됩니다. 여기에서 Azure IoT Edge를 사용 하 여 배포 됩니다. 자세한 내용은 [IoT Edge 모듈로 배포 (미리 보기)](../iot-edge/tutorial-deploy-machine-learning.md)를 참조 하세요.

[계산 대상에 모델을 배포 하는 위치 및 방법](how-to-deploy-and-where.md)에 대해 알아봅니다.

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (관리)

Azure Machine Learning에서 관리 되는 계산 리소스를 만들고 관리 합니다. 이 계산은 machine learning 워크 로드에 최적화 되어 있습니다. 계산 클러스터와 [계산 인스턴스](concept-compute-instance.md) 는 유일 하 게 관리 되는 계산 Azure Machine Learning.

다음에서 계산 인스턴스 또는 계산 클러스터 Azure Machine Learning 만들 수 있습니다.

* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md).
* Python SDK 및 CLI:
    * [계산 인스턴스입니다](how-to-create-manage-compute-instance.md).
    * [계산 클러스터](how-to-create-attach-compute-cluster.md).
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (미리 보기)
* Azure Resource Manager 템플릿 예제 템플릿은 [Azure Machine Learning 계산 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)를 참조 하세요.
* [Azure CLI에 대 한](reference-azure-machine-learning-cli.md#resource-management)machine learning 확장입니다.

이러한 계산 리소스는 생성 될 때 다른 종류의 계산 대상과 달리 작업 영역에 자동으로 포함 됩니다.


|기능  |컴퓨팅 클러스터  |컴퓨팅 인스턴스  |
|---------|---------|---------|
|단일 또는 다중 노드 클러스터     |    **&check;**       |         |
|실행을 제출할 때마다 자동으로 크기 조정     |     **&check;**      |         |
|자동 클러스터 관리 및 작업 예약     |   **&check;**        |     **&check;**      |
|CPU와 GPU 리소스에 대한 지원     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 계산 *클러스터* 는 유휴 상태일 때 0 개 노드로 자동 확장 되므로 사용 하지 않을 때에는 요금이 청구 되지 않습니다. 계산 *인스턴스* 는 always on 이며 자동 크기 조정을 하지 않습니다. 추가 비용을 방지 하기 위해 사용 하지 않는 경우 [계산 인스턴스를 중지](how-to-create-manage-compute-instance.md#manage) 해야 합니다.

### <a name="supported-vm-series-and-sizes"></a>지원되는 VM 시리즈 및 크기

Azure Machine Learning에서 관리 되는 계산 리소스에 대 한 노드 크기를 선택 하면 Azure에서 사용할 수 있는 VM 크기 선택 중에서 선택할 수 있습니다. Azure는 다양 한 워크 로드에 대해 다양 한 크기의 Linux 및 Windows를 제공 합니다. 자세히 알아보려면 [VM 유형 및 크기](../virtual-machines/sizes.md)를 참조 하세요.

VM 크기를 선택하는 데는 몇 가지 예외 및 제한 사항이 있습니다.

* 일부 VM 시리즈는 Azure Machine Learning에서 지원 되지 않습니다.
* 일부 VM 시리즈는 제한 되어 있습니다. 제한 된 시리즈를 사용 하려면 고객 지원에 문의 하 여 해당 시리즈에 대 한 할당량 증가를 요청 합니다. 지원 서비스에 문의 하는 방법에 대 한 자세한 내용은 [Azure 지원 옵션](https://azure.microsoft.com/support/options/)을 참조 하세요.

지원 되는 시리즈 및 제한 사항에 대해 자세히 알아보려면 다음 표를 참조 하세요.

| **지원 되는 VM 시리즈**  | **제한 사항** |
|------------|------------|
| D | 없음 |
| Dv2 | 없음 |  
| Dv3 | 없음|
| DSv2 | 없음 | 
| DSv3 | 없음|
| FSv2 | 없음 | 
| HBv2 | 승인이 필요 합니다. |  
| HCS | 승인이 필요 합니다. |  
| M | 승인이 필요 합니다. |
| NC | 없음 |    
| NCsv2 | 승인이 필요 합니다. |
| NCsv3 | 승인이 필요 합니다. |  
| NDs | 승인이 필요 합니다. |
| NDv2 | 승인이 필요 합니다. |
| NV | 없음 |
| NVv3 | 승인이 필요 합니다. | 


이러한 VM 시리즈를 지 원하는 Azure Machine Learning 있지만 일부 Azure 지역에서는 사용 하지 못할 수 있습니다. VM 시리즈를 사용할 수 있는지 확인 하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 참조 하세요.

### <a name="compute-isolation"></a>계산 격리

Azure Machine Learning compute는 특정 하드웨어 종류에 격리 되 고 단일 고객 전용으로 제공 되는 VM 크기를 제공 합니다. 격리 된 VM 크기는 다른 고객의 워크 로드와의 높은 수준의 격리를 필요로 하는 작업에 가장 적합 하며,이는 회의 규정 준수 및 규제 요구 사항을 포함 합니다. 격리 된 크기를 활용 하면 VM이 특정 서버 인스턴스에서 실행 되는 유일한 VM이 됩니다.

현재 격리 된 VM 제공은 다음과 같습니다.

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*RDMA 지원

격리에 대해 자세히 알아보려면 [Azure 공용 클라우드에서 격리](../security/fundamentals/isolation-choices.md)를 참조 하세요.

## <a name="unmanaged-compute"></a>관리 되지 않는 계산

관리 되지 않는 계산 대상이 Azure Machine Learning에서 관리 *되지* 않습니다. Azure Machine Learning 외부에서이 유형의 계산 대상을 만든 다음 작업 영역에 연결 합니다. 관리 되지 않는 계산 리소스를 유지 관리 하거나 machine learning 작업의 성능을 향상 시키기 위해 추가 단계가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:
* [계산 대상을 사용 하 여 모델 학습](how-to-set-up-training-targets.md)
* [계산 대상에 모델 배포](how-to-deploy-and-where.md)