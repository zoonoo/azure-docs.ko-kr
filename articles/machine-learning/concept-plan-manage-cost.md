---
title: 비용 계획 및 관리
titleSuffix: Azure Machine Learning
description: Azure Portal에서 비용 분석을 사용하여 Azure Machine Learning에 대한 비용을 계획하고 관리합니다. ML 모델을 빌드할 때 비용을 낮출 수 있도록 추가 비용 절감 팁을 알아보세요.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 6f4a8e4b8cbc35dca9f48bbec84e9023e82f6a84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102501638"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대한 비용 계획 및 관리

이 문서에서는 Azure Machine Learning에 대한 비용을 계획하고 관리하는 방법을 설명합니다. 먼저 리소스를 추가하기 전에 Azure 가격 책정 계산기를 사용하여 비용을 계획할 수 있습니다. 다음으로 Azure 리소스를 추가할 때 예상 비용을 검토합니다. 마지막으로 관리되는 Azure Machine Learning 컴퓨팅 클러스터로 모델을 학습하는 동안 비용 절감 팁을 사용합니다.

Azure Machine Learning 리소스 사용을 시작한 후에는 비용 관리 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 또한 예측 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수 있습니다.

Azure Machine Learning에 대한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 다른 Azure 서비스를 사용하는 경우 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다. 이 문서에서는 Azure Machine Learning에 대한 비용을 계획하고 관리하는 방법을 설명합니다. Azure Machine Learning에 대한 비용 관리에 익숙해지면 비슷한 방법을 적용하여 구독에 사용되는 모든 Azure 서비스에 대한 비용을 관리할 수 있습니다.

기계 학습 모델을 학습하는 경우 관리되는 Azure Machine Learning 컴퓨팅 클러스터를 사용하여 더 많은 비용 절감 팁을 활용합니다.

* 자동 크기 조정을 위한 학습 클러스터 구성
* 구독 및 작업 영역에 대한 할당량 설정
* 학습 실행에 대한 종료 정책 설정
* 우선 순위가 낮은 VM(가상 머신) 사용
* Azure Reserved VM Instance 사용

## <a name="prerequisites"></a>필수 구성 요소

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. 

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Azure Machine Learning을 사용하기 전에 비용 추정

Azure Machine Learning 계정에서 리소스를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 사용하여 비용을 예측합니다. 왼쪽에서 **AI + 기계 학습** 을 선택한 다음, **Azure Machine Learning** 을 선택하여 시작합니다.  

다음 스크린샷은 계산기를 사용한 비용 추정을 보여 줍니다.

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 계산기의 비용 추정":::

작업 영역에 새 리소스를 추가할 때 이 계산기로 돌아가서 동일한 리소스를 추가하여 비용 추정치를 업데이트합니다.

자세한 내용은 [Azure Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Azure Machine Learning에 대한 전체 청구 모델 이해

Azure Machine Learning은 새 리소스를 배포할 때 Azure Machine Learning과 함께 비용이 누적되는 Azure 인프라에서 실행됩니다. 추가 인프라로 비용이 누적될 수 있다는 것을 이해하는 것이 중요합니다. 배포된 리소스를 변경할 때 비용을 관리해야 합니다. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>일반적으로 Azure Machine Learning으로 누적되는 비용

Azure Machine Learning 작업 영역에 대한 리소스를 만들 때 다른 Azure 서비스에 대한 리소스도 생성됩니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기본 계정
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)(범용 v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>리소스를 삭제한 후 비용이 누적될 수 있음

Azure Portal에서 또는 Azure CLI를 사용하여 Azure Machine Learning 작업 영역을 삭제하면 다음과 같은 리소스가 계속 남아 있습니다. 이를 삭제할 때까지는 계속 비용이 누적됩니다.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

이러한 종속 리소스와 함께 작업 영역을 삭제하려면 SDK를 사용합니다.

```python
ws.delete(delete_dependent_resources=True)
```

작업 영역에 AKS(Azure Kubernetes Service)를 만들거나 작업 영역에 컴퓨팅 리소스를 연결하는 경우 [Azure Portal](https://portal.azure.com)에서 별도로 삭제해야 합니다.

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure Machine Learning에 Azure 선불 크레딧 사용

Azure 선불(이전 현금 약정 금액) 크레딧을 사용하여 Azure Machine Learning 요금을 지불할 수 있습니다. 단, Azure 선불을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.


## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 사용하면 추가 비용을 발생시키는 새 리소스를 실수로 만드는 일을 방지할 수 있습니다. 예산 생성 시 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 본인 외에 다른 사용자가 수행해야 하는 경우에 유용합니다. 예를 들어 재무 팀이 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대한 비용을 관리하고 절감하는 기타 방법

기계 학습 컴퓨팅 리소스에 대한 비용을 포함하는 데 이러한 팁을 사용합니다.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning 컴퓨팅 클러스터(AmlCompute) 사용

지속적으로 변화하는 데이터를 통해 정확한 모델을 유지하기 위해 빠르고 효율적인 모델 학습 및 재학습을 수행해야 합니다. 단, 지속적인 학습에는 비용이 발생합니다. 특히나 GPU의 딥 러닝 모델에 대해 그렇습니다. 

Azure Machine Learning 사용자는 AmlCompute라고도 하는 관리되는 Azure Machine Learning 컴퓨팅 클러스터를 사용할 수 있습니다. AmlCompute는 다양한 GPU 및 CPU 옵션을 지원합니다. AmlCompute는 Azure Machine Learning에 의해 사용자 구독을 대신하여 내부적으로 호스팅됩니다. Azure IaaS 클라우드 규모에서 동일한 엔터프라이즈급 보안, 규정 준수 및 거버넌스를 제공합니다.

이러한 컴퓨팅 풀이 Azure의 IaaS 인프라 내에 있으므로 인프라의 나머지 부분과 동일한 보안 및 규정 준수 요구 사항을 사용하여 학습을 배포, 확장 및 관리할 수 있습니다.  이러한 배포는 구독에서 발생하고 거버넌스 규칙을 준수합니다. [Azure Machine Learning 컴퓨팅](how-to-create-attach-compute-cluster.md)에 대해 자세히 알아보세요.

### <a name="configure-training-clusters-for-autoscaling"></a>자동 크기 조정을 위한 학습 클러스터 구성

워크로드 요구 사항에 따라 클러스터 크기를 자동으로 조정하면 필요한 기능만 사용하도록 비용을 줄일 수 있습니다.

AmlCompute 클러스터는 워크로드에 따라 동적으로 크기를 조정하도록 설계되었습니다. 클러스터는 구성하는 최대 노드 수까지 확장할 수 있습니다. 각 실행이 완료되면 클러스터에서 노드가 해제되고 구성된 최소 노드 수로 확장됩니다.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

또한 규모를 축소하기 전에 노드가 유휴 상태로 유지되는 시간을 구성할 수 있습니다. 기본적으로 축소 전의 유휴 시간은 120초로 설정됩니다.

+ 반복적 실험을 수행하는 경우 비용을 절약하기 위해 이 시간을 줄입니다.
+ 매우 반복적인 개발/테스트 실험을 수행하는 경우 학습 스크립트나 환경에 대한 각 변경 후 일정한 확장 및 축소에 대해 비용을 지불하지 않도록 시간을 늘려야 할 수 있습니다.

[AmlCompute SDK 클래스](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)와 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)를 사용하여 Azure Portal에서 변화하는 워크로드 요구 사항에 대해 AmlCompute 클러스터를 구성할 수 있습니다.

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>리소스에 대한 할당량 설정

AmlCompute는 [할당량(또는 제한) 구성](how-to-manage-quotas.md#azure-machine-learning-compute)과 함께 제공됩니다. 이 할당량은 VM 제품군(예: Dv2 시리즈, NCv3 시리즈)에 따라 다르며 각 구독에 대한 지역별 차이가 있습니다. 구독은 작은 기본값으로 시작되지만, 이 설정을 사용하여 구독에서 분리할 수 있는 Amlcompute 리소스의 양을 제어할 수 있습니다. 

또한 구독 내의 각 작업 영역에 대해 [VM 제품군별 작업 영역 수준 할당량](how-to-manage-quotas.md#workspace-level-quotas)을 구성합니다. 이렇게 하면 각 작업 영역에서 잠재적으로 발생할 수 있는 비용을 더 세부적으로 제어하고 특정 VM 제품군을 제한할 수 있습니다. 

작업 영역 수준에서 할당량을 설정하려면 [Azure Portal](https://portal.azure.com)에서 시작합니다.  구독에서 아무 작업 영역이나 선택하고 왼쪽 창에서 **사용량 + 할당량** 을 선택합니다. 그런 다음, **할당량 구성** 탭을 선택하여 할당량을 확인합니다. 할당량을 설정하려면 구독 범위에서 권한이 필요합니다. 이는 여러 작업 영역에 영향을 주는 설정이기 때문입니다.

### <a name="set-run-autotermination-policies"></a>자동 종료 정책 실행 설정 

경우에 따라 학습 실행을 구성하여 기간을 제한하거나 조기에 종료해야 합니다. 예를 들어 Azure Machine Learning의 기본 제공 하이퍼 매개 변수 튜닝 또는 자동화된 Machine Learning을 사용하는 경우입니다.

다음은 몇 가지 옵션입니다.
* RunConfiguration에서 `max_run_duration_seconds`라는 매개 변수를 정의하여 사용자가 선택하는 컴퓨팅(로컬 또는 원격 클라우드 컴퓨팅)에서 실행이 확장될 수 있는 최대 기간을 제어합니다.
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md#early-termination)의 경우 Bandit 정책, 중간 중지 정책 또는 잘림 선택 정책에서 조기 종료 정책을 정의합니다. 하이퍼 매개 변수 스윕을 추가로 제어하려면 `max_total_runs` 또는 `max_duration_minutes`와 같은 매개 변수를 사용합니다.
* [자동화된 Machine Learning](how-to-configure-auto-train.md#exit)의 경우 `enable_early_stopping` 플래그를 사용하여 비슷한 종료 정책을 설정합니다. 또한 `iteration_timeout_minutes` 및 `experiment_timeout_minutes`와 같은 속성을 사용하여 실행의 최대 기간 또는 전체 실험을 제어합니다.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 우선 순위가 낮은 VM 사용

Azure를 통해 가상 머신 확장 집합, Batch 및 Machine Learning 서비스에서 활용되지 않는 과도한 용량을 우선 순위가 낮은 VM으로 사용할 수 있습니다. 이러한 할당은 선점이 가능하지만, 전용 VM에 비해 저렴한 가격으로 제공됩니다. 일반적으로 Batch 워크로드에 우선 순위가 낮은 VM을 사용하는 것이 좋습니다. 다시 전송(Batch 추론의 경우) 또는 다시 시작(검사점을 사용한 딥 러닝 학습)을 통해 중단을 복구할 수 있는 경우에도 이를 사용해야 합니다.

우선 순위가 낮은 VM의 개별 할당량은 VM 제품군별 전용 할당량 값과 별개입니다. [AmlCompute 할당량에 대해 자세히](how-to-manage-quotas.md) 알아보세요.

 우선 순위가 낮은 VM은 대화형 Notebook 환경을 지원해야 하기 때문에 컴퓨팅 인스턴스에 대해 작동하지 않습니다.

### <a name="use-reserved-instances"></a>예약 인스턴스 사용

컴퓨팅 리소스에 대한 비용을 절감하는 또 다른 방법은 Azure Reserved VM Instance입니다. 이 제품을 통해 1년 또는 3년 조건으로 약정하게 됩니다. 이러한 할인 범위는 종량제 가격의 최대 72%이며 월간 Azure 청구서에 직접 적용됩니다.

Azure Machine Learning 컴퓨팅은 기본적으로 예약 인스턴스를 지원합니다. 1년 또는 3년 예약 인스턴스를 구매하는 경우 Azure Machine Learning 관리 컴퓨팅에 대한 할인이 자동으로 적용됩니다.


## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행합니다.