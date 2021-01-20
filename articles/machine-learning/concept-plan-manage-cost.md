---
title: 비용 계획 및 관리
titleSuffix: Azure Machine Learning
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Machine Learning에 대 한 비용을 계획 하 고 관리 합니다. ML 모델을 작성할 때 비용을 낮출 수 있도록 추가 비용 절감 팁을 알아보세요.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 5be02ff698dac02d702e47f8929c6f8ddf2adbb7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602549"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 비용 계획 및 관리

이 문서에서는 Azure Machine Learning에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 리소스를 추가 하기 전에 Azure 가격 책정 계산기를 사용 하 여 비용을 계획할 수 있습니다. 그런 다음 Azure 리소스를 추가할 때 예상 비용을 검토 합니다. 마지막으로 관리 되는 Azure Machine Learning 계산 클러스터로 모델을 학습 하는 동안 비용 절감 팁을 사용 합니다.

Azure Machine Learning 리소스 사용을 시작한 후에는 cost management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링할 수 있습니다. 또한 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별 합니다.

Azure Machine Learning에 대 한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 다른 Azure 서비스를 사용 하는 경우 타사 서비스를 포함 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대 한 요금이 청구 됩니다. 이 문서에서는 Azure Machine Learning에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. Azure Machine Learning에 대 한 비용 관리에 익숙해지면 구독에 사용 되는 모든 Azure 서비스에 대 한 비용을 관리 하는 비슷한 방법을 적용 합니다.

Machine learning 모델을 학습 하는 경우 관리 되는 Azure Machine Learning 계산 클러스터를 사용 하 여 더 많은 비용 절감 팁을 활용 합니다.

* 자동 크기 조정을 위한 학습 클러스터 구성
* 구독 및 작업 영역에 대 한 할당량 설정
* 학습 실행에 대 한 종료 정책 설정
* 낮은 우선 순위의 가상 머신 (VM) 사용
* Azure Reserved VM Instance 사용

## <a name="prerequisites"></a>사전 요구 사항

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. 

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Azure Machine Learning를 사용 하기 전에 비용 추정

Azure Machine Learning 계정에서 리소스를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 사용 하 여 비용을 예측 합니다. 왼쪽에서 **AI + Machine Learning** 를 선택한 다음 **Azure Machine Learning** 를 선택 하 여 시작 합니다.  

다음 스크린샷은 계산기를 사용 하 여 비용 예측을 보여 줍니다.

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 계산기의 예상 비용":::

작업 영역에 새 리소스를 추가할 때이 계산기로 돌아가서 동일한 리소스를 추가 하 여 비용 추정치를 업데이트 합니다.

자세한 내용은 [Azure Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 전체 청구 모델 이해

Azure Machine Learning는 새 리소스를 배포할 때 Azure Machine Learning와 함께 비용이 발생 하는 Azure 인프라에서 실행 됩니다. 추가 인프라가 비용을 계산 하는 것을 이해 하는 것이 중요 합니다. 배포 된 리소스를 변경할 때이 비용을 관리 해야 합니다. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>일반적으로 Azure Machine Learning으로 계산 되는 비용

Azure Machine Learning 작업 영역에 대 한 리소스를 만들 때 다른 Azure 서비스에 대 한 리소스도 생성 됩니다. 관련 토폴로지는 다음과 같습니다.

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기본 계정
* [Azure 블록 Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (범용 v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>리소스 삭제 후 비용이 발생할 수 있습니다.

Azure Portal 또는 Azure CLI에서 Azure Machine Learning 작업 영역을 삭제 하면 다음 리소스가 계속 남아 있습니다. 삭제할 때까지 비용이 계속 해 서 계산 됩니다.

* Azure Container Registry
* Azure 블록 Blob Storage
* Key Vault
* Application Insights

이러한 종속 리소스와 함께 작업 영역을 삭제 하려면 SDK를 사용 합니다.

```python
ws.delete(delete_dependent_resources=True)
```

작업 영역에 Azure Kubernetes 서비스 (AKS)를 만들거나 작업 영역에 계산 리소스를 연결 하는 경우 [Azure Portal](https://portal.azure.com)에서 별도로 삭제 해야 합니다.

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure Machine Learning Azure 선불 크레딧을 사용

Azure 선불 (이전에는 금액 약정 이라고 함) 크레딧을 사용 하 여 Azure Machine Learning 요금을 지불할 수 있습니다. 그러나 Azure 선불를 사용 하 여 Azure Marketplace에서 사용 하는 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.


## <a name="create-budgets"></a>예산 만들기

[예산을](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 

모니터링에 세분성이 더 필요한 경우 Azure의 특정 리소스 또는 서비스에 대 한 필터를 사용 하 여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 절감할 수 있는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산 생성 시의 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 또는 다른 사용자가 비용에 대 한 추가 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 비용을 관리 하 고 절감 하는 기타 방법

Machine learning 계산 리소스에 대 한 비용을 포함 하는 데 이러한 팁을 사용 합니다.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning 계산 클러스터 사용 (AmlCompute)

지속적으로 변화 하는 데이터를 통해 정확한 모델을 유지 하기 위해 빠르고 효율적인 모델 학습 및 재 학습을 수행 해야 합니다. 그러나 지속적인 교육은 특히 Gpu의 심층 학습 모델에 대 한 비용으로 제공 됩니다. 

Azure Machine Learning 사용자는 AmlCompute 라고도 하는 관리 되는 Azure Machine Learning 계산 클러스터를 사용할 수 있습니다. AmlCompute는 다양 한 GPU 및 CPU 옵션을 지원 합니다. AmlCompute는 Azure Machine Learning 하 여 구독을 대신 하 여 내부적으로 호스팅됩니다. Azure IaaS 클라우드 규모에서 동일한 엔터프라이즈급 보안, 규정 준수 및 거 버 넌 스를 제공 합니다.

이러한 계산 풀이 Azure의 IaaS 인프라 내에 있으므로 인프라의 나머지 부분과 동일한 보안 및 규정 준수 요구 사항을 사용 하 여 교육을 배포, 확장 및 관리할 수 있습니다.  이러한 배포는 구독에서 발생 하 고 거 버 넌 스 규칙을 준수 합니다. [Azure Machine Learning 계산](how-to-create-attach-compute-cluster.md)에 대해 자세히 알아보세요.

### <a name="configure-training-clusters-for-autoscaling"></a>자동 크기 조정을 위한 학습 클러스터 구성

워크 로드 요구 사항에 따라 클러스터 크기를 자동으로 조정 하면 필요한 기능만 사용 하도록 비용을 줄일 수 있습니다.

AmlCompute 클러스터는 워크 로드에 따라 동적으로 크기를 조정 하도록 설계 되었습니다. 클러스터는 구성 하는 최대 노드 수까지 확장할 수 있습니다. 각 실행이 완료 되 면 클러스터에서 노드가 해제 되 고 구성 된 최소 노드 수로 확장 됩니다.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

규모를 축소 하기 전에 노드가 유휴 상태로 유지 되는 시간을 구성할 수도 있습니다. 기본적으로 축소 전의 유휴 시간은 120 초로 설정 됩니다.

+ 반복적 실험을 수행 하는 경우 비용을 절약 하기 위해이 시간을 줄입니다.
+ 매우 반복적인 개발/테스트 실험을 수행 하는 경우 학습 스크립트나 환경에 대 한 각 변경 후 일정 한 확장 및 축소에 대해 비용을 지불 하지 않도록 시간을 늘려야 할 수 있습니다.

Amlcompute [SDK 클래스](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) [AMLCOMPUTE CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)와 [REST api](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)를 사용 하 여 Azure Portal에서 변화 하는 워크 로드 요구 사항에 대해 amlcompute 클러스터를 구성할 수 있습니다.

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>리소스에 대 한 할당량 설정

AmlCompute는 [할당량 (또는 제한) 구성과](how-to-manage-quotas.md#azure-machine-learning-compute)함께 제공 됩니다. 이 할당량은 VM 제품군 (예: Dv2 series, NCv3 series)에 의해 다르며 각 구독에 대 한 지역별 차이가 있습니다. 구독은 작은 기본값을 사용 하 여 시작 되지만,이 설정을 사용 하 여 구독에서 분리 수 있는 Amlcompute 리소스의 양을 제어할 수 있습니다. 

또한 구독 내의 각 작업 영역에 대해 [VM 제품군으로 작업 영역 수준 할당량](how-to-manage-quotas.md#workspace-level-quotas)을 구성 합니다. 이렇게 하면 각 작업 영역에서 잠재적으로 발생할 수 있는 비용을 보다 세부적으로 제어 하 고 특정 VM 제품군을 제한할 수 있습니다. 

작업 영역 수준에서 할당량을 설정 하려면 [Azure Portal](https://portal.azure.com)에서 시작 합니다.  구독에서 작업 영역을 선택 하 고 왼쪽 창에서 **사용량 + 할당량** 을 선택 합니다. 그런 다음 할당량 **구성** 탭을 선택 하 여 할당량을 확인 합니다. 할당량을 설정 하려면 구독 범위에서 권한이 필요 합니다 .이는 여러 작업 영역에 영향을 주는 설정 이기 때문입니다.

### <a name="set-run-autotermination-policies"></a>자동 종료 정책 실행 설정 

경우에 따라 학습 실행을 구성 하 여 기간을 제한 하거나 조기에 종료 해야 합니다. 예를 들어 Azure Machine Learning의 기본 제공 하이퍼 매개 변수 튜닝 또는 자동화 된 Machine Learning을 사용 하는 경우입니다.

다음은 몇 가지 옵션입니다.
* RunConfiguration에서 이라는 매개 변수를 정의 `max_run_duration_seconds` 하 여 사용자가 선택 하는 계산 (로컬 또는 원격 클라우드 계산)에서 실행이 확장 될 수 있는 최대 기간을 제어 합니다.
* 하이퍼 [매개 변수 조정](how-to-tune-hyperparameters.md#early-termination)의 경우 산적 정책, 중간 중지 정책 또는 잘림 선택 정책에서 조기 종료 정책을 정의 합니다. 하이퍼 매개 변수 스윕을 추가로 제어 하려면 또는와 같은 매개 변수를 사용 `max_total_runs` `max_duration_minutes` 합니다.
* [자동화 된 machine learning](how-to-configure-auto-train.md#exit)의 경우 플래그를 사용 하 여 비슷한 종료 정책을 설정 `enable_early_stopping` 합니다. 또한 및와 같은 속성 `iteration_timeout_minutes` `experiment_timeout_minutes` 을 사용 하 여 실행의 최대 기간 또는 전체 실험을 제어 합니다.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 우선 순위가 낮은 Vm 사용

Azure를 사용 하면 가상 머신 확장 집합, 배치 및 Machine Learning 서비스에서 Low-Priority Vm으로 과도 하 게 사용 되는 용량을 사용할 수 있습니다. 이러한 할당은 emptible는 하지만 전용 Vm과 비교 하 여 저렴 한 가격으로 제공 됩니다. 일반적으로 일괄 작업에 Low-Priority Vm을 사용 하는 것이 좋습니다. 다시 전송 (Batch 추론의 경우) 또는 다시 시작 (검사점을 사용한 심층 학습 교육)을 통해 중단을 복구할 수 있는 경우에도이를 사용 해야 합니다.

Low-Priority Vm에는 VM 제품군의 전용 할당량 값과 별도의 단일 할당량이 있습니다. [AmlCompute 할당량에](how-to-manage-quotas.md)대해 자세히 알아보세요.

 Low-Priority Vm은 대화형 노트북 환경을 지원 해야 하기 때문에 계산 인스턴스에 대해 작동 하지 않습니다.

### <a name="use-reserved-instances"></a>예약 인스턴스 사용

계산 리소스에 대 한 비용을 절감 하는 또 다른 방법은 Azure 예약 VM 인스턴스입니다. 이 제품을 사용 하 여 1 년 또는 3 년 조건으로 커밋합니다. 이러한 할인 범위는 종 량 제 가격의 최대 72%까지 이며 월간 Azure 청구서에 직접 적용 됩니다.

Azure Machine Learning Compute는 기본적으로 예약 된 인스턴스를 지원 합니다. 1 년 또는 3 년 예약 인스턴스를 구매 하는 경우 관리 되는 Azure Machine Learning 계산에 대 한 할인이 자동으로 적용 됩니다.


## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
- [예기치 않은 비용을 방지](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
- [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.