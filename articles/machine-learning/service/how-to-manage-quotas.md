---
title: Azure Machine Learning 서비스에 대한 할당량을 관리 및 요청하는 방법
description: 이 방법 가이드에서는 Azure Machine Learning에 대한 리소스의 다양한 할당량에 대해 설명하고 더 많은 할당량을 요청하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997814"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Azure 리소스에 대한 할당량 관리 및 요청

다른 Azure 서비스와 마찬가지로, Azure Machine Learning 서비스와 연결된 특정 리소스에 대한 한도가 있습니다. 이러한 한도는 만들 수 있는 작업 영역의 개수부터 모델 교육 또는 추론에 사용되는 실제 기본 계산의 한도까지 다양합니다. 이 문서에서는 구독에 사용되는 다양한 Azure 리소스에 대해 미리 구성된 한도를 자세히 설명하고, 각 리소스 유형의 할당량 증가를 요청할 수 있는 간편한 링크를 제공합니다.

Azure ML를 설계 및 강화할 때 이러한 할당량을 염두에 두어야 합니다. 예를 들어 클러스터가 지정된 대상 노드 수에 도달하지 않으면 구독의 Batch AI 코어 한도에 도달한 것일 수 있습니다. Batch AI에서 프로덕션 작업을 실행하려고 계획 중인 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다. 기본 제한 이상으로 제한 또는 할당량을 높이려는 경우 비용 없이 온라인 고객 지원 요청을 개설합니다. 다음 표에 나오는 최대 한도 값 이상으로 한도를 높일 수 없습니다. 최대 한도 열이 없는 경우 리소스에 조정 가능한 한도가 없는 것입니다. 

## <a name="special-considerations"></a>특별 고려 사항

+ 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.

+ 할당량은 Batch AI를 제외하고 구독의 모든 서비스에서 공유되며, 그 중 하나는 Azure Machine Learning입니다. 용량 요구 사항을 평가할 때 모든 서비스의 할당량 사용량을 계산해야 합니다.

+ 기본 한도는 평가판, 종량제 및 Dv2, F, G 등의 시리즈와 같은 제품 범주 유형에 따라 다릅니다.

## <a name="default-resource-quotas"></a>기본 리소스 할당량

다음은 Azure 구독 내의 다양한 리소스 유형에 따라 할당량 한도를 분류한 것입니다. 

> [!Important]
> 한도는 변경될 수 있습니다. 언제든지 모든 Azure의 서비스 수준 할당량 [문서](https://docs.microsoft.com/azure/azure-subscription-service-limits/)에서 최신 정보를 확인할 수 있습니다.

### <a name="virtual-machines"></a>가상 머신 
서비스의 Azure 구독에 또는 독립 실행형으로 프로비전할 수 있는 가상 머신의 수가 제한되어 있습니다. 이 제한은 총 코어 수 및 제품군 별로 영역 수준에서 적용됩니다.

가상 머신 코어에 별도로 적용되는 지역 크기 시리즈(Dv2, F 등)당 한도뿐만 아니라 지역 총 한도가 있다는 것을 강조하는 것이 중요합니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이러한 구독은 A1 VM 30개 또는 D1 VM 30개 또는 총 코어가 30개를 초과하지 않는 두 가지의 조합(예: A1 VM 10, D1 VM 20)을 배포하도록 허용됩니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

좀 더 구체적인 최신 할당량 한도 목록은 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1)서 Azure 전체 할당량 문서를 참조하세요.

### <a name="batch-ai-clusters"></a>Batch AI 클러스터
Batch AI에서, 구독의 영역마다 허용되는 코어 수 및 클러스터 수에 대한 기본 할당량 제한이 있습니다. Batch AI 할당량은 위의 VM 코어 할당량과 별개이며 코어 한도는 현재 두 리소스 종류 간에 공유되지 않습니다.

사용 가능한 리소스:
+ 영역별 전용 코어 수의 기본 제한은 10-24입니다.  Batch AI 구독당 전용 코어 수를 늘릴 수 있습니다. 증가 옵션은 고객 지원에 문의하세요.

+ 영역당 낮은 우선 순위 코어 수의 기본 제한은 10-24입니다.  Batch AI 구독당 낮은 우선 순위 코어 수를 늘릴 수 있습니다. 증가 옵션은 고객 지원에 문의하세요.

+ 영역당 클러스터 수의 기본 제한은 20이고 최대 제한은 200입니다. 이 제한을 초과하여 늘리려면 Azure 고객 지원에 문의하세요.

좀 더 구체적인 최신 할당량 한도 목록은 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits)서 Azure 전체 할당량 문서를 참조하세요.

### <a name="container-instances"></a>Container instances

특정 기간에(시간 단위로 범위 지정) 또는 전체 구독에서 작동할 수 있는 컨테이너 인스턴스의 수에도 제한이 있습니다.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

좀 더 구체적인 최신 할당량 한도 목록은 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits)서 Azure 전체 할당량 문서를 참조하세요.

### <a name="storage"></a>Storage
영역당 및 특정 구독당 저장소 계정의 수가 제한되어 있습니다. 기본 제한은 200이고 표준 및 프리미엄 저장소 계정이 모두 포함됩니다. 지정된 지역에서 저장소 계정이 200개 이상 필요한 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)에 요청합니다. Azure Storage 팀이 비즈니스 사례를 검토하고 지정된 지역에 대해 Storage 계정을 250개까지 승인할 수 있습니다.


## <a name="find-your-quotas"></a>할당량 찾기

Virtual Machines, Storage, Network 등의 다양한 리소스에 대한 할당량을 쉽게 확인하는 방법은 Azure Portal입니다.

1. 왼쪽 창에서 **모든 서비스**를 선택한 다음, 일반 범주에서 **구독**을 선택합니다.

1. 구독 목록에서 할당량을 보려는 구독을 선택합니다.

   특히 Batch AI 할당량 보기와 관련하여 **주의할 점**이 있습니다. 앞서 언급했듯이, 이 할당량은 구독의 계산 할당량과는 별개입니다. 
   Batch AI의 경우 **모든 서비스**를 선택한 후 Batch AI를 검색하고 서비스를 엽니다.

1. **설정** 아래에서 **사용량 + 할당량**을 선택하여 현재 할당량 한도 및 사용량을 봅니다.

1. 구독을 선택하여 할당량 한도를 봅니다. 잊지 말고 보고 싶은 서비스 및 영역으로 필터링하세요.


## <a name="request-quota-increases"></a>할당량 증가 요청

기본 제한 이상으로 제한 또는 할당량을 높이려는 경우 비용 없이 [온라인 고객 지원 요청을 개설](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)합니다. 

다음 표에 나오는 최대 한도 값 이상으로 한도를 높일 수 없습니다. 최대 제한이 없는 경우 리소스에 조정 가능한 제한이 없는 것입니다. [이](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) 문서에서는 할당량 증가 프로세스를 자세히 다룹니다.

할당량 증가를 요청할 때 할당량을 늘리려는 서비스를 선택해야 하며 이러한 서비스로는 AzureML 할당량, Batch AI 할당량, Storage 할당량 등이 있습니다. 

> [!NOTE]
> [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)은 제한하거나 할당량을 증가할 수 없습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 평가판을 종량제로 업그레이드](../../billing/billing-upgrade-azure-subscription.md) 및 [평가판 구독 FAQ](https://azure.microsoft.com/free/free-account-faq)를 참조하세요.
