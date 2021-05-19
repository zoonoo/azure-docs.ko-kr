---
title: 컴퓨팅 대상이란
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용하여 모델을 학습시키거나 배포하도록 컴퓨팅 리소스 또는 환경을 지정하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: f421274513cc3d76f7e8c1119cf98e4e5d36b1f9
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107950277"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning에서 컴퓨팅 대상은 무엇인가요?

*컴퓨팅 대상* 은 지정된 컴퓨팅 리소스나 환경으로, 여기서 학습 스크립트를 실행하거나 서비스 배포를 호스팅합니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 컴퓨팅 리소스일 수 있습니다. 컴퓨팅 대상을 사용하면 나중에 코드를 변경하지 않고도 컴퓨팅 환경을 간편하게 변경할 수 있습니다.

일반적인 모델 개발 수명 주기에서 다음을 수행할 수 있습니다.

1. 적은 양의 데이터에서 개발 및 실험하여 시작합니다. 이 단계에서는 로컬 컴퓨터나 클라우드 기반 VM(가상 머신)과 같은 로컬 환경을 컴퓨팅 대상으로 사용합니다.
1. 이러한 [학습 컴퓨터 대상](#train) 중 하나를 사용하여 데이터를 크게 스케일 업하거나 분산 학습을 수행합니다.
1. 모델이 준비되면 이러한 [배포 컴퓨팅 대상](#deploy) 중 하나를 사용하여 웹 호스팅 환경이나 IoT 디바이스에 배포합니다.

컴퓨팅 대상에 사용하는 컴퓨팅 리소스는 [작업 영역](concept-workspace.md)에 연결됩니다. 작업 영역의 사용자가 로컬 컴퓨터 이외의 컴퓨팅 리소스를 공유합니다.

## <a name="training-compute-targets"></a><a name="train"></a> 컴퓨팅 대상 학습

Azure Machine Learning에는 다양한 컴퓨팅 대상에 대한 다양한 지원이 포함되어 있습니다. 일반적인 모델 개발 수명 주기는 적은 양의 데이터에서 개발 또는 실험으로 시작합니다. 이 단계에서는 로컬 컴퓨터나 클라우드 기반 VM과 같은 로컬 환경을 사용합니다. 더 큰 데이터 세트에서 학습을 스케일 업하거나 분산 학습을 수행하는 경우 Azure Machine Learning 컴퓨팅을 사용하여 실행을 제출할 때마다 크기가 자동 조정되는 단일 또는 다중 노드 클러스터를 만듭니다. 고유한 컴퓨팅 리소스도 연결할 수 있지만 시나리오마다 지원이 다를 수 있습니다.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[학습 실행을 컴퓨팅 대상에 제출](how-to-set-up-training-targets.md)하는 방법에 대해 자세히 알아보세요.

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> 유추에 사용되는 컴퓨팅 대상

유추를 수행할 경우 Azure Machine Learning은 사용하는 데 필요한 모델과 관련 리소스를 호스팅하는 Docker 컨테이너를 만듭니다. 그런 다음, 이 컨테이너는 컴퓨팅 대상에 사용됩니다.

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

[모델을 컴퓨팅 대상에 배포하는 장소와 방법](how-to-deploy-and-where.md)을 알아보세요.

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 컴퓨팅(관리형)

관리형 컴퓨팅 리소스는 Azure Machine Learning에서 생성되고 관리됩니다. 이 컴퓨팅은 기계 학습 워크로드에 최적화되어 있습니다. Azure Machine Learning 컴퓨팅 클러스터와 [컴퓨팅 인스턴스](concept-compute-instance.md)는 유일한 관리형 컴퓨팅입니다.

다음에서 Azure Machine Learning 컴퓨팅 인스턴스나 컴퓨팅 클러스터를 만들 수 있습니다.

* [Azure Machine Learning 스튜디오](how-to-create-attach-compute-studio.md)
* Python SDK 및 CLI:
    * [컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)
    * [컴퓨팅 클러스터](how-to-create-attach-compute-cluster.md)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)(미리 보기)
* Azure Resource Manager 템플릿 예제 템플릿은 [Azure Machine Learning 컴퓨팅 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)를 참조하세요.
* [Azure CLI용 기계 학습 확장](reference-azure-machine-learning-cli.md#resource-management)

만들면 이러한 컴퓨팅 리소스는 다른 종류의 컴퓨팅 대상과 달리 자동으로 작업 영역에 포함됩니다.


|기능  |컴퓨팅 클러스터  |컴퓨팅 인스턴스  |
|---------|---------|---------|
|단일 또는 다중 노드 클러스터     |    **&check;**       |         |
|실행을 제출할 때마다 자동 크기 조정     |     **&check;**      |         |
|자동 클러스터 관리 및 작업 예약     |   **&check;**        |     **&check;**      |
|CPU와 GPU 리소스에 대한 지원     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 컴퓨팅 *클러스터* 가 유휴 상태이면 크기가 0 노드로 자동 조정되므로 사용하지 않을 때는 비용을 지불하지 않습니다. 컴퓨팅 *인스턴스* 는 항상 켜져 있고 크기가 자동 조정되지 않습니다. 사용하지 않을 때는 추가 비용이 발생하지 않도록 [컴퓨팅 인스턴스를 중지](how-to-create-manage-compute-instance.md#manage)해야 합니다.

### <a name="supported-vm-series-and-sizes"></a>지원되는 VM 시리즈 및 크기

Azure Machine Learning에서 관리형 컴퓨팅 리소스의 노드 크기를 선택하면 Azure에서 사용할 수 있는 일부 VM 크기 중에서 선택할 수 있습니다. Azure는 다양한 워크로드에 맞게 다양한 크기의 Linux 및 Windows를 제공합니다. 자세한 내용은 [VM 형식 및 크기](../virtual-machines/sizes.md)를 참조하세요.

VM 크기를 선택하는 데는 몇 가지 예외 및 제한 사항이 있습니다.

* Azure Machine Learning에서는 일부 VM 시리즈가 지원되지 않습니다.
* 일부 VM 시리즈는 제한됩니다. 제한된 시리즈를 사용하려면 고객 지원팀에 문의하여 시리즈 할당량 증가를 요청합니다. 고객 지원팀에 문의하는 방법은 [Azure 지원 옵션](https://azure.microsoft.com/support/options/)을 참조하세요.

지원되는 크기와 제한 사항에 대한 자세한 내용은 다음 표를 참조하세요.

| **지원되는 VM 시리즈**  | **제한 사항** | **범주** | **지원 요소** |
|------------|------------|------------|------------|
| D | 없음 | 범용 | 컴퓨팅 클러스터와 인스턴스 |
| DDSv4 | 없음 | 범용 | 컴퓨팅 클러스터와 인스턴스 |
| Dv2 | 없음 | 범용 | 컴퓨팅 클러스터와 인스턴스 |
| Dv3 | 없음| 범용 | 컴퓨팅 클러스터와 인스턴스 |
| DSv2 | 없음 | 범용 | 컴퓨팅 클러스터와 인스턴스 |
| DSv3 | 없음| 범용 | 컴퓨팅 클러스터와 인스턴스 |
| EAv4 | 없음 | 메모리 최적화 | 컴퓨팅 클러스터와 인스턴스 |
| Ev3 | 없음 | 메모리 최적화 | 컴퓨팅 클러스터와 인스턴스 |
| FSv2 | 없음 | 컴퓨팅 최적화 | 컴퓨팅 클러스터와 인스턴스 |
| H | 없음 | 고성능 컴퓨팅 | 컴퓨팅 클러스터와 인스턴스 |
| HB | 승인 필요 | 고성능 컴퓨팅 | 컴퓨팅 클러스터와 인스턴스 |
| HBv2 | 승인 필요 |  고성능 컴퓨팅 | 컴퓨팅 클러스터와 인스턴스 |
| HCS | 승인 필요 |  고성능 컴퓨팅 | 컴퓨팅 클러스터와 인스턴스 |
| M | 승인 필요 | 메모리 최적화 | 컴퓨팅 클러스터와 인스턴스 |
| NC | 없음 |  GPU | 컴퓨팅 클러스터와 인스턴스 |
| NC Promo | 없음 | GPU | 컴퓨팅 클러스터와 인스턴스 |
| NCsv2 | 승인 필요 | GPU | 컴퓨팅 클러스터와 인스턴스 |
| NCsv3 | 승인 필요 | GPU | 컴퓨팅 클러스터와 인스턴스 |  
| ND | 승인 필요 | GPU | 컴퓨팅 클러스터와 인스턴스 | 
| NDv2 | 승인 필요 | GPU | 컴퓨팅 클러스터와 인스턴스 | 
| NV | 없음 | GPU | 컴퓨팅 클러스터와 인스턴스 | 
| NVv3 | 승인 필요 | GPU | 컴퓨팅 클러스터와 인스턴스 | 


Azure Machine Learning에서 이러한 VM 시리즈를 지원하지만 모든 Azure 지역에서 사용할 수 있는 것은 아닙니다. VM 시리즈 사용 가능 여부를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 참조하세요.

> [!NOTE]
> Azure Machine Learning은 Azure Compute에서 지원하는 모든 VM 크기를 지원하지 않습니다. 사용 가능한 VM 크기를 나열하려면 다음 메서드 중 하나를 사용합니다.
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>컴퓨팅 격리

Azure Machine Learning 컴퓨팅은 특정 하드웨어 형식에서 격리되고 단일 고객 전용인 VM 크기를 제공합니다. 격리된 VM 크기는 규정 준수 및 규정 요구 사항 충족 등의 이유로 다른 고객의 워크로드로부터 높은 수준의 격리가 필요한 워크로드에 가장 적합합니다. 격리 크기를 사용하면 VM 하나만 해당 서버 인스턴스에서 실행되도록 보장됩니다.

현재 격리된 VM 제품은 다음과 같습니다.

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*RDMA 지원

격리에 대한 자세한 내용은 [Azure 퍼블릭 클라우드에서 격리](../security/fundamentals/isolation-choices.md)를 참조하세요.

## <a name="unmanaged-compute"></a>관리되지 않는 컴퓨팅

관리되지 않는 컴퓨팅 대상은 Azure Machine Learning에서 관리되지 *않습니다*. Azure Machine Learning 외부에 이 형식의 컴퓨팅 대상을 만든 다음, 작업 영역에 연결합니다. 관리되지 않는 컴퓨팅 리소스에는 기계 학습 워크로드 성능을 유지하거나 향상시키는 추가 단계가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:
* [컴퓨팅 대상을 사용하여 모델 학습](how-to-set-up-training-targets.md)
* [모델을 컴퓨팅 대상에 배포](how-to-deploy-and-where.md)
