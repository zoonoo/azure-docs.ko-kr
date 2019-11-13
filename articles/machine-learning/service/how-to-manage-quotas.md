---
title: 리소스 & 할당량 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 리소스에 대 한 할당량 및 추가 할당량을 요청 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 11/04/2019
ms.openlocfilehash: d44fb94978c450808c8a1c0852d4c771a100857e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961712"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Azure 리소스에 대한 할당량 관리 및 요청
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 구독에 대 한 Azure 리소스에 대 한 미리 구성 된 제한 사항에 대해 자세히 설명 합니다. 또한 각 리소스 유형에 대 한 할당량 향상 기능을 요청 하는 방법에 대 한 지침도 포함 되어 있습니다. 사기로 인해 예산이 초과되는 것을 방지하고 Azure 용량 제약 조건을 준수하려면 이러한 한도를 적용해야 합니다.

다른 Azure 서비스와 마찬가지로 Azure Machine Learning와 연결 된 특정 리소스에 대 한 제한이 있습니다. 이러한 제한 범위는 작업 영역 수의 상한에서 모델 학습 또는 유추/점수 매기기에 사용 되는 실제 기본 계산에 대 한 제한으로 제한 됩니다. 

프로덕션 워크 로드에 대 한 Azure Machine Learning 리소스를 설계 하 고 확장할 때 이러한 제한을 고려 합니다. 예를 들어 클러스터가 대상 노드 수에 도달 하지 않은 경우 구독에 대 한 Azure Machine Learning 계산 코어 제한에 도달 했을 수 있습니다. 기본 제한 이상으로 제한 또는 할당량을 높이려는 경우 비용 없이 온라인 고객 지원 요청을 개설합니다. Azure 용량 제약 조건으로 인해 다음 표에 표시되는 최대 한도 값 이상으로 한도를 높일 수 없습니다. 최대 한도 열이 없는 경우 리소스에 조정 가능한 한도가 없는 것입니다.

## <a name="special-considerations"></a>특별 고려 사항

+ 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.

+ 할당량은 Azure Machine Learning를 포함 하 여 구독에 있는 모든 서비스에서 공유 됩니다. 할당량이 공유되지 않는 경우는 컴퓨팅 코어 할당량과는 다른 별도의 할당량이 적용되는 Azure Machine Learning 컴퓨팅뿐입니다. 용량 요구 사항을 평가할 때 모든 서비스의 할당량 사용량을 계산해야 합니다.

+ 기본 제한은 무료 평가판, 종 량 제 및 Dv2, F, G 등의 VM 시리즈와 같은 제품 범주 유형에 따라 달라 집니다.

## <a name="default-resource-quotas"></a>기본 리소스 할당량

다음은 Azure 구독 내의 다양한 리소스 유형에 따라 할당량 한도를 분류한 것입니다.

> [!Important]
> 한도는 변경될 수 있습니다. 언제든지 모든 Azure의 서비스 수준 할당량 [문서](https://docs.microsoft.com/azure/azure-subscription-service-limits/)에서 최신 정보를 확인할 수 있습니다.

### <a name="virtual-machines"></a>가상 머신
각 Azure 구독에 대해 서비스 또는 독립 실행형에서 사용할 수 있는 가상 컴퓨터의 수에 제한이 있습니다. 이 제한은 총 코어 수 및 제품군 별로 영역 수준에서 적용됩니다.

가상 컴퓨터 코어에는 지역별 총 제한과 지역별 크기 시리즈 (Dv2, F 등) 제한이 있습니다. 둘 다 개별적으로 적용 됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이러한 구독은 A1 VM 30개 또는 D1 VM 30개 또는 총 코어가 30개를 초과하지 않는 두 가지의 조합(예: A1 VM 10, D1 VM 20)을 배포하도록 허용됩니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

좀 더 구체적인 최신 할당량 한도 목록은 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits)서 Azure 전체 할당량 문서를 참조하세요.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
Azure Machine Learning 컴퓨팅의 경우 구독의 영역당 허용되는 코어 수 및 고유한 컴퓨팅 리소스 수의 기본 할당량 한도가 있습니다. 이 할당량은 위의 VM 코어 할당량과는 별개 이며, AmlCompute는 호스팅되는 모델에서 리소스를 배포 하는 관리 되는 서비스 이므로 두 리소스 유형 간에 코어 제한이 공유 되지 않습니다.

사용 가능한 리소스:
+ 지역별 전용 코어의 기본 제한은 EA 및 CSP 제공 유형에 대 한 높은 기본값을 가진 구독 제안 유형에 따라 24-300입니다.  구독 당 전용 코어 수는 늘릴 수 있으며 VM 제품군 마다 다릅니다. NCv2, NCv3 또는 ND 시리즈와 같은 특수화 된 특정 VM 제품군은 기본 제로 코어로 시작 합니다. 증가 옵션에 대해 자세히 설명 하는 할당량 요청을 발생 시켜 Azure 지원에 문의 하세요.

+ 지역별 낮은 우선 순위의 코어 수는 EA 및 CSP 제공 유형에 대 한 기본값이 높은 구독 제안 유형에 따라 100-3000의 기본 제한이 있습니다. 구독 당 낮은 우선 순위의 코어 수는 증가 하 고 VM 제품군에서 단일 값일 수 있습니다. 증가 옵션은 고객 지원에 문의하세요.

+ 지역별 클러스터의 기본 제한은 200입니다. 이러한 클러스터는 교육 클러스터와 계산 인스턴스 간에 공유 됩니다 .이 인스턴스는 할당량을 위해 단일 노드 클러스터로 간주 됩니다. 이 제한을 초과하여 늘리려면 Azure 고객 지원에 문의하세요.

+ 적중 한 후에는 초과 될 수 없는 * * 다른 엄격한 제한이 있습니다.

| **Resource** | **최대 한도** |
| --- | --- |
| 리소스 그룹당 최대 작업 영역 | 800 |
| 단일 Azure Machine Learning 컴퓨팅(AmlCompute) 리소스의 최대 노드 수 | 100개 노드 |
| 노드당 최대 GPU MPI 프로세스 | 1-4 |
| 노드당 최대 GPU 작업자 수 | 1-4 |
| 최대 작업 수명 | 90 일<sup>1</sup> |
| 우선 순위가 낮은 노드의 최대 작업 수명 | 7 일<sup>2</sup> |
| 노드당 최대 매개 변수 서버 수 | 1 |

<sup>1</sup> 최대 수명은 실행이 시작되는 시간과 완료되는 시간을 나타냅니다. 완료된 실행은 무기한 저장됩니다. 최대 수명 이내에 완료되지 않은 실행에 대한 데이터는 액세스할 수 없습니다.
용량 제약 조건이 있는 경우 우선 순위가 낮은 노드의 작업 <sup>2</sup> 개를 선점할 수 있습니다. 작업에서 검사점을 구현 하는 것이 좋습니다.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 파이프라인
Azure Machine Learning 파이프라인의 경우 파이프라인의 단계 수와 구독에서 지역별 게시 된 파이프라인의 일정 기반 실행 수에 대 한 할당량 한도가 있습니다.
- 파이프라인에서 허용 되는 최대 단계 수는 3만입니다.
- 일정 기반 실행 및 월별 구독 당 게시 된 파이프라인에 대 한 blob 풀의 최대 합계 수는 10만입니다.

> [!NOTE]
> 이 제한을 높이려면 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

### <a name="container-instances"></a>Container instances

특정 기간에(시간 단위로 범위 지정) 또는 전체 구독에서 작동할 수 있는 컨테이너 인스턴스의 수에도 제한이 있습니다.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

좀 더 구체적인 최신 할당량 한도 목록은 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits)서 Azure 전체 할당량 문서를 참조하세요.

### <a name="storage"></a>Storage
영역당 및 특정 구독당 스토리지 계정의 수가 제한되어 있습니다. 기본 제한은 250 이며 표준 및 Premium Storage 계정을 모두 포함 합니다. 지정 된 지역에서 250 개 이상의 저장소 계정이 필요한 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)에 요청을 만듭니다. Azure Storage 팀이 비즈니스 사례를 검토하고 지정된 지역에 대해 Storage 계정을 250개까지 승인할 수 있습니다.


## <a name="workspace-level-quota"></a>작업 영역 수준 할당량

다양 한 작업 영역 간의 Amlcompute에 대 한 리소스 할당을 보다 효율적으로 관리 하기 위해 VM 제품군에서 구독 수준 할당량을 배포 하 고 작업 영역 수준에서 구성할 수 있는 기능이 도입 되었습니다. 기본 동작은 모든 VM 제품군에 대 한 구독 수준 할당량과 동일한 할당량을 모든 작업 영역에 포함 하는 것입니다. 그러나 작업 영역 수가 늘어남에 따라 우선 순위가 다양 한 작업이 동일한 리소스를 공유 하기 시작 하면 사용자는 용량을 보다 효율적으로 공유 하 고 리소스 경합 문제를 방지 하는 방법을 원합니다. Azure Machine Learning은 사용자가 각 작업 영역에서 특정 VM 제품군에 대 한 최대 할당량을 설정할 수 있도록 하 여 관리 되는 계산 제공 솔루션을 제공 합니다. 이는 작업 영역 간에 용량을 배포 하는 것과 유사 하며, 사용자가 과도 하 게 할당 하 여 최대 사용을 추진 하도록 선택할 수도 있습니다. 

작업 영역 수준에서 할당량을 설정 하려면 구독의 작업 영역으로 이동 하 고 왼쪽 창에서 **사용량 + 할당량** 을 클릭 합니다. 그런 다음 할당량 **구성** 탭을 선택 하 여 할당량을 확인 하 고, vm 제품군을 확장 하 고, 해당 vm 제품군 아래에 나열 된 작업 영역에 대 한 할당량 제한을 설정 합니다. 음수 값 또는 구독 수준 할당량 보다 높은 값을 설정할 수 없습니다. 또한 기본적으로 모든 작업 영역에는 할당 된 할당량의 전체 사용률을 허용 하기 위해 전체 구독 할당량이 할당 됩니다.

[![Azure Machine Learning 작업 영역 수준 할당량](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Enterprise edition 기능입니다. 구독에 기본 및 Enterprise edition 작업 영역이 둘 다 있는 경우이를 사용 하 여 엔터프라이즈 작업 영역에 대 한 할당량을 설정할 수 있습니다. 기본 작업 영역에는 기본 동작인 구독 수준 할당량이 계속 포함 됩니다.
>
> 작업 영역 수준에서 할당량을 설정 하려면 구독 수준 권한이 있어야 합니다. 이는 개별 작업 영역 소유자가 편집 하거나 할당량을 늘리고 다른 작업 영역에 대해 따로 설정 된 리소스에 encroaching를 시작 하지 않도록 하는 데 적용 됩니다. 따라서 구독 관리자는 작업 영역 간에 이러한 할당량을 할당 하 고 배포 하는 데 가장 적합 합니다.



## <a name="view-your-usage-and-quotas"></a>사용량 및 할당량 보기

Virtual Machines, Storage, Network 등의 다양한 리소스에 대한 할당량을 쉽게 확인하는 방법은 Azure Portal입니다.

1. 왼쪽 창에서 **모든 서비스**를 선택한 다음, 일반 범주에서 **구독**을 선택합니다.

1. 구독 목록에서 할당량을 보려는 구독을 선택합니다.

   특히 Azure Machine Learning 컴퓨팅 할당량 보기와 관련하여 **주의할 점**이 있습니다. 앞서 언급했듯이, 이 할당량은 구독의 컴퓨팅 할당량과는 별개입니다.

1. 왼쪽 창에서 **Machine Learning 서비스** 를 선택 하 고 표시 된 목록에서 작업 영역을 선택 합니다.

1. 다음 블레이드의 **지원 + 문제 해결 섹션**에서 **사용량 + 할당량**을 선택하여 현재 할당량 한도 및 사용량을 확인합니다.

1. 구독을 선택하여 할당량 한도를 확인합니다. 관심 있는 영역으로 필터링해야 합니다.

1. 이제 구독 수준 보기와 작업 영역 수준 보기 사이를 전환할 수 있습니다.
    + **구독 뷰:** 이렇게 하면 VM 제품군에서 코어 할당량을 사용 하 여 작업 영역으로 확장 하 고 실제 클러스터 이름으로 확장할 수 있습니다. 이 보기는 특정 VM 제품군의 코어 사용에 대 한 세부 정보를 신속 하 게 파악 하 여 각 작업 영역에 대 한 기본 클러스터에서 작업 영역에의 한 분할 및 기타 작업을 추가로 확인 하는 데 적합 합니다. 이 보기의 일반 규칙은 (사용량/할당량)입니다. 여기서 사용량은 현재 확장 된 코어 수이 고, 할당량은 리소스를 확장할 수 있는 논리적 최대 코어 수입니다. 각 **작업 영역**에 대해 할당량은 위에서 설명한 대로 작업 영역 수준 할당량으로, 특정 VM 제품군에 맞게 확장할 수 있는 최대 코어 수를 나타냅니다. 이와 유사 하 게 **클러스터** 의 경우 할당량은 실제로 max_nodes 속성에 의해 정의 되는 클러스터 크기를 조정할 수 있는 최대 노드 수에 해당 하는 코어입니다.

    + **작업 영역 보기:** 이를 통해 작업 영역에서 코어 할당량을 사용 하 고 VM 제품군을 확장 하 여 실제 클러스터 이름으로 확장 하는 작업을 볼 수 있습니다. 이 보기는 특정 작업 영역에 대 한 핵심 사용에 대 한 세부 정보를 신속 하 게 확인 하 여 VM 제품군에의 한 분할을 확인 하 고 각 제품군에 대 한 기본 클러스터에서 추가 작업을 진행 하는 데 가장 적합 합니다.

## <a name="request-quota-increases"></a>할당량 증가 요청

기본 제한 이상으로 제한 또는 할당량을 높이려는 경우 비용 없이 [온라인 고객 지원 요청을 개설](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)합니다.

다음 표에 나오는 최대 한도 값 이상으로 한도를 높일 수 없습니다. 최대 제한이 없는 경우 리소스에 조정 가능한 제한이 없는 것입니다. [이](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) 문서에서는 할당량 증가 프로세스를 자세히 다룹니다.

할당량 증가를 요청할 때 할당량을 늘리려는 서비스를 선택해야 하며 이러한 서비스로는 Machine Learning Service 할당량, 컨테이너 인스턴스 또는 Storage 할당량이 있습니다. Azure Machine Learning 계산 외에도 할당량 **요청** 단추를 클릭 하 고 위의 단계에 따라 할당량을 볼 수 있습니다.

> [!NOTE]
> [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)은 제한하거나 할당량을 증가할 수 없습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 평가판을 종량제로 업그레이드](../../billing/billing-upgrade-azure-subscription.md) 및 [평가판 구독 FAQ](https://azure.microsoft.com/free/free-account-faq)를 참조하세요.
