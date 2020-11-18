---
title: 컴퓨팅 클러스터 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역에서 계산 클러스터를 만드는 방법에 대해 알아봅니다. 계산 클러스터를 학습 또는 유추를 위한 계산 대상으로 사용 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 0bbf70016dc9b93120b3158e8954c336095ea211
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832690"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning 컴퓨팅 클러스터 만들기

Azure Machine Learning 작업 영역에서 [계산 클러스터](concept-compute-target.md#azure-machine-learning-compute-managed) 를 만들고 관리 하는 방법에 대해 알아봅니다.

Azure Machine Learning 계산 클러스터를 사용 하 여 클라우드의 CPU 또는 GPU 계산 노드 클러스터에서 학습 또는 일괄 처리 유추 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](../virtual-machines/sizes-gpu.md)를 참조하세요. 

이 문서에서는 다음 방법을 알아봅니다.

* 컴퓨팅 클러스터 만들기
* 계산 클러스터 비용 낮추기
* 클러스터에 대 한 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 설정

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

* Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

## <a name="what-is-a-compute-cluster"></a>계산 클러스터용 이란?

Azure Machine Learning 계산 클러스터는 단일 또는 다중 노드 계산을 쉽게 만들 수 있는 관리 되는 계산 인프라입니다. 작업 영역 지역 내에서 컴퓨팅은 작업 영역에서 다른 사용자와 공유할 수 있는 리소스로 만들어집니다. 작업이 제출될 때 컴퓨팅이 자동으로 확장되어 Azure Virtual Network에 배치될 수 있습니다. 컴퓨팅은 컨테이너화된 환경에서 실행되며 모델 종속성을 [Docker 컨테이너](https://www.docker.com/why-docker)로 패키지합니다.

계산 클러스터는 기업에서 SSH 포트를 열지 않아도 [가상 네트워크 환경](how-to-secure-training-vnet.md)에서 작업을 안전 하 게 실행할 수 있습니다. 작업은 컨테이너 화 된 환경에서 실행 되며 모델 종속성을 Docker 컨테이너에 패키지 합니다. 

## <a name="limitations"></a>제한 사항

* 작업 영역에서 **동일한 계산에 대 한 여러 개의 동시 첨부 파일을 만들지 마십시오** . 예를 들어 두 개의 다른 이름을 사용 하 여 하나의 계산 클러스터를 작업 영역에 연결 합니다. 새 첨부 파일은 이전의 기존 첨부 파일을 중단 합니다.

    예를 들어 클러스터 구성 설정을 변경 하기 위해 계산 대상을 다시 연결 하려면 먼저 기존 첨부 파일을 제거 해야 합니다.

* 이 문서에 나열 된 일부 시나리오는 __미리 보기로__ 표시 되어 있습니다. 미리 보기 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

* Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수와 같은 기본적인 제한이 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조하세요.

* Azure를 사용 하면 리소스에 대 한 _잠금을_ 설정 하 여 삭제 하거나 읽기 전용으로 설정할 수 있습니다. __작업 영역을 포함 하는 리소스 그룹에 리소스 잠금을 적용 하지 마십시오__. 작업 영역을 포함 하는 리소스 그룹에 잠금을 적용 하면 Azure ML 계산 클러스터에 대 한 크기 조정 작업을 할 수 없습니다. 리소스 잠금에 대 한 자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

> [!TIP]
> 필요한 코어 수만큼 할당량이 있는 경우 일반적으로 클러스터를 최대 100노드까지 스케일 업할 수 있습니다. 예를 들어 기본적으로 클러스터 노드 간에는 노드 간 통신을 사용하도록 설정하여 MPI 작업을 지원합니다. 그러나 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 수천 개의 클러스터의 크기를 조정 하 고, 구독 또는 작업 영역을 나열 하도록 요청 하거나, 노드 간 통신을 사용 하지 않도록 설정할 수 있습니다. 


## <a name="create"></a>만들기

**예상 시간**: 약 5 분.

Azure Machine Learning 컴퓨팅은 실행 전반에서 다시 사용할 수 있습니다. 컴퓨팅은 작업 영역에 있는 다른 사용자와 공유할 수 있으며, 실행 간에 유지되고 제출된 실행 수와 클러스터에 설정된 max_nodes 수에 따라 노드를 자동으로 확장 또는 축소합니다. min_nodes 설정은 사용 가능한 최소 노드 수를 제어합니다.

VM 제품군 할당량 당 지역별 전용 코어 및 계산 클러스터 만들기에 적용 되는 총 지역 할당량은 통합 되 고 Azure Machine Learning 교육 계산 인스턴스 할당량을 사용 하 여 공유 됩니다. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

사용되지 않는 경우 컴퓨팅은 0개 노드로 자동 축소됩니다.   필요에 따라 작업을 실행하기 위해 전용 VM이 만들어집니다.
    
# <a name="python"></a>[Python](#tab/python)

Python에서 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들려면 **vm_size** 및 **max_nodes** 속성을 지정합니다. 그런 다음, Azure Machine Learning은 다른 속성에 스마트 기본값을 사용합니다. 
    
* **vm_size**: Azure Machine Learning 컴퓨팅에서 만든 노드의 VM 제품군입니다.
* **max_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행할 때 자동으로 확장할 최대 노드 수입니다.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Azure Machine Learning 컴퓨팅을 만들 때 여러 고급 속성을 구성할 수도 있습니다. 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.  자세한 내용은 [AmlCompute 클래스](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py)를 참조하세요.


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

자세한 내용은 [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true)를 참조하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

스튜디오에서 계산 클러스터를 만드는 방법에 대 한 자세한 내용은 [Azure Machine Learning studio에서 계산 대상 만들기](how-to-create-attach-compute-studio.md#amlcompute)를 참조 하세요.

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 계산 클러스터 비용 낮추기

[우선 순위가 낮은 vm](concept-plan-manage-cost.md#low-pri-vm) 을 사용 하 여 작업의 일부 또는 전체를 실행 하도록 선택할 수도 있습니다. 이러한 VM은 가용성이 보장되지 않고 사용 중에 선점될 수 있습니다. 선점된 작업은 계속되지 않고 다시 시작됩니다. 

이러한 방법 중 하나를 사용 하 여 우선 순위가 낮은 VM을 지정 합니다.
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음을 설정 합니다 `vm-priority` .
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

스튜디오에서 VM을 만들 때 **낮은 우선 순위** 를 선택 합니다.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 관리 id 설정

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* 프로 비전 구성에서 관리 되는 id 구성:  

    * 시스템 할당 관리 id:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * 사용자가 할당한 관리형 ID:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* 기존 계산 클러스터에 관리 되는 id 추가 
    
    * 시스템이 할당한 관리형 ID:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * 사용자가 할당한 관리형 ID:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 관리 id를 사용 하 여 관리 되는 계산 클러스터 새로 만들기

  * 사용자 할당 관리 ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * 시스템 할당 관리 ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* 기존 클러스터에 관리 되는 id를 추가 합니다.

    * 사용자 할당 관리 ID
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * 시스템 할당 관리 ID

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

[Studio에서 관리 Id 설정](how-to-create-attach-compute-studio.md#managed-identity)을 참조 하세요.

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>관리 id 사용

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>다음 단계

계산 클러스터를 사용 하 여 다음을 수행 합니다.

* [학습 실행 제출](how-to-set-up-training-targets.md) 
* [일괄 처리 유추를 실행](./tutorial-pipeline-batch-scoring-classification.md)합니다.
