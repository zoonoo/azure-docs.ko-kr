---
title: 리소스 및 할당량 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 리소스에 대 한 할당량 및 할당량 향상을 요청 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 9bcf6ac9991c1ad070f823c97b5bd0460eff07c2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309080"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning 사용 하 여 리소스에 대 한 할당량 관리 및 늘리기

Azure는 제한 및 할당량을 사용 하 여 사기 문제로 인 한 예산 오버런을 방지 하 고 Azure 용량 제약 조건을 준수 합니다. 프로덕션 워크 로드를 확장할 때 이러한 제한을 고려 합니다. 이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> + [Azure Machine Learning](overview-what-is-azure-ml.md)와 관련 된 Azure 리소스에 대 한 기본 제한입니다.
> + 작업 영역 수준 할당량을 만드는 중입니다.
> + 할당량 및 한도 보기
> + 할당량을 늘리는 중입니다.
> + 개인 끝점 및 DNS 할당량.

할당량 관리와 함께 [Azure Machine Learning에 대 한 비용을 계획 하 고 관리](concept-plan-manage-cost.md)하는 방법을 배울 수 있습니다.

## <a name="special-considerations"></a>특별 고려 사항

+ 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 [Azure 지원에 문의 하 여 할당량을 늘립니다](#request-quota-increases).

+ 할당량은 Azure Machine Learning를 포함 하 여 구독에 있는 모든 서비스에서 공유 됩니다. 용량을 평가할 때 모든 서비스에서 사용량을 계산 합니다.
 
  Azure Machine Learning compute는 예외입니다. 코어 계산 할당량과 별도의 할당량이 있습니다. 

+ 기본 제한은 무료 평가판, 종 량 제 및 VM (가상 머신) 시리즈 (Dv2, F, G 등)와 같은 제품 범주 유형에 따라 달라 집니다.

## <a name="default-resource-quotas"></a>기본 리소스 할당량

이 섹션에서는 다음 리소스에 대 한 기본 및 최대 할당량 한도에 대해 알아봅니다.

+ 가상 머신
+ Azure Machine Learning 컴퓨팅
+ 파이프라인 Azure Machine Learning
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> 한도는 변경될 수 있습니다. 최신 정보는 azure [구독 및 서비스 제한, 할당량 및](../azure-resource-manager/management/azure-subscription-service-limits.md) 모든 azure에 대 한 제약 조건을 참조 하세요.

### <a name="virtual-machines"></a>가상 머신
각 Azure 구독에는 모든 서비스의 가상 머신 수에 대 한 제한이 있습니다. 가상 컴퓨터 코어에는 지역별 총 한도와 크기 별 지역별 제한이 있습니다. 두 제한 모두 개별적으로 적용 됩니다.

미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이 구독은 30 개의 A1 Vm 또는 30 D1 개의 Vm을 배포할 수 있도록 허용 하거나 총 30 개 코어를 초과 하지 않는 둘의 조합을 허용 합니다.

다음 표에 표시 된 값을 초과 하 여 가상 컴퓨터에 대 한 제한을 발생 시킬 수 없습니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
[Azure Machine Learning 계산](concept-compute-target.md#azure-machine-learning-compute-managed) 에는 코어 수와 구독에서 지역 당 허용 되는 고유 계산 리소스 수에 대 한 기본 할당량 한도가 있습니다. 이 할당량은 이전 섹션의 VM 코어 할당량과 별개입니다.

[할당량 증가를 요청](#request-quota-increases) 하 여이 섹션의 제한을 표에 표시 된 최대 제한까지 올립니다.

사용 가능한 리소스:
+ **지역별 전용 코어** 의 기본 제한은 구독 제안 유형에 따라 24 ~ 300입니다. 각 VM 제품군에 대해 구독 당 전용 코어 수를 늘릴 수 있습니다. NCv2, NCv3 또는 ND 시리즈와 같은 특수 한 VM 제품군은 기본 제로 코어를 사용 하 여 시작 합니다.

+ **지역별 낮은 우선 순위의 코어** 는 구독 제안 유형에 따라 100 ~ 3000의 기본 제한이 있습니다. 구독당 우선 순위가 낮은 코어 수는 확장 가능하며 VM 제품군별로 값이 다릅니다.

+ **지역별 클러스터** 의 기본 제한은 200입니다. 이러한 설정은 교육 클러스터와 계산 인스턴스 간에 공유 됩니다. (계산 인스턴스는 할당량을 위해 단일 노드 클러스터로 간주 됩니다.)

다음 표에서는 초과할 수 없는 추가 제한을 보여 줍니다.

| **리소스** | **최대 한도** |
| --- | --- |
| 리소스 그룹당 작업 영역 | 800 |
| 단일 Azure Machine Learning 계산 (AmlCompute) 리소스의 노드 | 100개 노드 |
| 노드당 GPU MPI 프로세스 | 1-4 |
| 노드당 GPU 작업자 | 1-4 |
| 작업 수명 | 21 일<sup>1</sup> |
| 낮은 우선 순위의 노드의 작업 수명 | 7일<sup>2</sup> |
| 노드당 매개 변수 서버 | 1 |

<sup>1</sup> 최대 수명은 실행 시작 시간과 완료 시간 사이의 기간입니다. 완료 된 실행은 무기한 지속 됩니다. 최대 수명 내에 완료 되지 않은 실행에 대 한 데이터는 액세스할 수 없습니다.
용량 제약 조건이 있을 때마다 우선 순위가 낮은 노드의 작업 <sup>2</sup> 개를 선점할 수 있습니다. 작업에서 검사점을 구현 하는 것이 좋습니다.

### <a name="azure-machine-learning-pipelines"></a>파이프라인 Azure Machine Learning
[Azure Machine Learning 파이프라인](concept-ml-pipelines.md) 의 제한은 다음과 같습니다.

| **리소스** | **제한** |
| --- | --- |
| 파이프라인의 단계 | 30,000 |
| 리소스 그룹당 작업 영역 | 800 |

### <a name="container-instances"></a>Container Instances

자세한 내용은 [Container Instances 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)을 참조 하세요.

### <a name="storage"></a>스토리지
Azure Storage은 구독 당 지역 당 저장소 계정 250 개로 제한 됩니다. 이 제한에는 표준 및 프리미엄 저장소 계정이 모두 포함 됩니다.

제한을 늘리려면 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)을 통해 요청을 수행 합니다. Azure Storage 팀은 해당 사례를 검토 하 고 지역에 대해 최대 250 개의 저장소 계정을 승인할 수 있습니다.


## <a name="workspace-level-quotas"></a>작업 영역 수준 할당량

작업 영역 수준 할당량을 사용 하 여 동일한 구독의 여러 [작업 영역](concept-workspace.md) 간에 Azure Machine Learning 계산 대상 할당을 관리 합니다.

기본적으로 모든 작업 영역은 VM 제품군에 대 한 구독 수준 할당량과 동일한 할당량을 공유 합니다. 그러나 구독의 작업 영역에서 개별 VM 패밀리의 최대 할당량을 설정할 수 있습니다. 이렇게 하면 용량을 공유 하 고 리소스 경합 문제를 방지할 수 있습니다.

1. 구독의 작업 영역으로 이동 합니다.
1. 왼쪽 창에서 **사용량 + 할당량** 을 선택 합니다.
1. 할당량을 보려면 **할당량 구성** 탭을 선택 합니다.
1. VM 제품군을 확장 합니다.
1. 해당 VM 제품군 아래에 나열 된 작업 영역에서 할당량 한도를 설정 합니다.

음수 값 또는 구독 수준 할당량 보다 높은 값을 설정할 수 없습니다.

[![Azure Machine Learning 작업 영역 수준 할당량을 보여 주는 스크린샷](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> 작업 영역 수준에서 할당량을 설정 하려면 구독 수준 권한이 있어야 합니다.

## <a name="view-your-usage-and-quotas"></a>사용량 및 할당량 보기

가상 머신, 저장소 또는 네트워크와 같은 다양 한 Azure 리소스에 대 한 할당량을 보려면 Azure Portal를 사용 합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택한 다음, **일반** 범주 아래에서 **구독** 을 선택 합니다.

2. 구독 목록에서 할당량을 찾고 있는 구독을 선택 합니다.

3. **사용량 + 할당량** 을 선택 하 여 현재 할당량 한도 및 사용량을 확인 합니다. 필터를 사용 하 여 공급자 및 위치를 선택 합니다. 

다른 Azure 할당량과는 별도로 구독에 대 한 Azure Machine Learning 계산 할당량을 관리 합니다. 

1. Azure Portal에서 **Azure Machine Learning** 작업 영역으로 이동 합니다.

2. 왼쪽 창의 **지원 + 문제 해결** 섹션에서 **사용량 + 할당량** 을 선택 하 여 현재 할당량 한도 및 사용량을 확인 합니다.

3. 구독을 선택하여 할당량 한도를 확인합니다. 관심이 있는 영역으로 필터링 합니다.

4. 구독 수준 보기와 작업 영역 수준 보기 사이를 전환할 수 있습니다.

## <a name="request-quota-increases"></a>할당량 증가 요청

제한 또는 할당량을 기본 한도 이상으로 올리려면 무료로 [온라인 고객 지원 요청을 여세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

위의 표에 표시 된 최대값을 초과 하 여 한도를 발생 시킬 수 없습니다. 최대 제한이 없는 경우에는 리소스에 대 한 제한을 조정할 수 없습니다.

할당량 증가를 요청 하는 경우 염두에 두어야 하는 서비스를 선택 합니다. 예를 들어 Azure Machine Learning, Container Instances 또는 저장소를 선택 합니다. Azure Machine Learning 계산의 경우 이전 단계에서 할당량을 볼 때 **할당량 요청** 단추를 선택할 수 있습니다.

> [!NOTE]
> [무료 평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p) 에는 제한 또는 할당량 증가가 적합 하지 않습니다. 무료 평가판 구독을 사용 하는 경우 [종 량](https://azure.microsoft.com/offers/ms-azr-0003p/) 제 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [azure 무료 평가판을 종 량](../cost-management-billing/manage/upgrade-azure-subscription.md) 제 및 [AZURE 체험 계정 FAQ](https://azure.microsoft.com/free/free-account-faq)로 업그레이드를 참조 하세요.

## <a name="private-endpoint-and-private-dns-quota-increases"></a>프라이빗 엔드포인트 및 프라이빗 DNS 할당량 증가

구독에서 만들 수 있는 개인 끝점 및 개인 DNS 영역 수에는 제한이 있습니다.

Azure Machine Learning는 (고객) 구독에 리소스를 만들지만 일부 시나리오에서는 Microsoft 소유의 구독에서 리소스를 만듭니다.

 다음 시나리오에서는 Microsoft 소유의 구독에서 할당량 한도를 요청 해야 할 수 있습니다.

* CMK (고객이 관리 하는 키)가 있는 Azure 개인 링크 사용 작업 영역
* 가상 네트워크 뒤의 작업 영역에 대한 Azure Container Registry
* Private Link를 사용하는 Azure Kubernetes Service 클러스터를 작업 영역에 연결

이러한 시나리오에 대 한 허용을 요청 하려면 다음 단계를 사용 합니다.

1. [Azure 지원 요청을 만들고](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) __기본 사항__ 섹션에서 다음 옵션을 선택 합니다.

    | 필드 | 선택 |
    | ----- | ----- |
    | 문제 유형 | **기술** |
    | 서비스 | **내 서비스**. 그런 다음 드롭다운 목록에서 __Machine Learning__ 을 선택 합니다. |
    | 문제 유형 | **작업 영역 구성 및 보안** |
    | 문제 하위 유형 | **개인 끝점 및 사설 DNS 영역 허용 요청** |

2. __세부 정보__ 섹션에서 __설명__ 필드를 사용 하 여 Azure 지역 및 사용할 시나리오를 제공 합니다. 여러 구독에 대 한 할당량 증가가 요청 해야 하는 경우이 필드에 구독 Id를 나열 합니다.

3. __만들기__ 를 선택 하 여 요청을 만듭니다.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="개인 끝점 및 개인 DNS 할당량 증가 요청의 스크린샷":::

## <a name="next-steps"></a>다음 단계

+ [Azure Machine Learning에 대 한 비용 계획 및 관리](concept-plan-manage-cost.md)
