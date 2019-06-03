---
title: Azure Advisor를 사용하여 서비스 비용 절감 | Microsoft Docs
description: Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: fef7b82e6969de16d1815250d2373c99021b0e86
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254716"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Azure Advisor를 사용하여 서비스 비용 절감

Advisor는 유휴 및 사용 미달 리소스를 식별하여 전체적인 Azure 사용을 최적화하고 줄이는 데 도움을 줍니다. Advisor 대시보드의 **비용** 탭에서 비용 관련 권장 지침을 얻을 수 있습니다.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>사용량이 낮은 인스턴스의 크기를 조정하거나 종료하여 가상 머신 소비 최적화 

특정 애플리케이션 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 머신의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. Advisor는 7 일 동안 가상 머신 사용량을 모니터링 하 고 사용률이 낮은 가상 컴퓨터를 식별 합니다. 가상 컴퓨터 사용률이 낮은 경우 해당 CPU 사용률이 5% 이하인 것으로 간주 됩니다 및 해당 네트워크 사용률은 2% 보다 작으면 또는 더 작은 가상 머신 크기에서 현재 워크 로드를 수용할 수 있는지 표시 합니다.

Advisor에는 가상 머신을 계속 실행할 때의 예상 비용이 표시되므로 해당 가상 머신을 종료하거나 크기를 조정하도록 선택할 수 있습니다.

사용량이 낮은 가상 머신을 더 적극적으로 식별하고 싶은 경 구독당 평균 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>프로비저닝되지 않은 ExpressRoute 회로를 제거하여 비용 절감

Advisor는 공급자 상태가 1개월 이상 *프로비저닝되지 않음*인 ExpressRoute 회로를 식별하고, 연결 공급자로 회로를 프로비저닝할 계획이 아닌 경우 회로를 삭제할 것을 권장합니다.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>유휴 상태의 가상 네트워크 게이트웨이를 삭제하거나 다시 구성하여 비용 절감

Advisor는 90 일 넘게 유휴 상태를 유지한 가상 네트워크 게이트를 식별합니다. 이러한 게이트웨이는 시간당 요금이 부과되므로 다시 구성하거나, 더 이상 사용하지 않으려는 경우 삭제를 고려해야 합니다. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>예약 가상 머신 인스턴스를 구매하여 종량제 비용보다 비용 절약

Advisor는 지난 30일 동안 가상 머신 사용량을 검토하고Azure 예약을 구매하여 비용을 절감할 수 있는지 확인합니다. Advisor는 잠재적으로 가장 절약할 수 있는 지역 및 크기를 보여주고, 예약 구매로 예상되는 절감을 보여줍니다. Azure 예약을 사용하여 가상 머신에 대한 기본 비용을 미리 구입할 수 있습니다. 할인은 예약과 동일한 크기 및 지역이 있는 새로운 또는 기존 VM에 자동으로 적용됩니다. [Azure Reserved VM Instances에 대한 자세한 정보](https://azure.microsoft.com/pricing/reserved-vm-instances/)

관리자도 알려 인스턴스의 예약 해야 하는 30 일 이내에 만료 됩니다. 종 량 제 가격을 지불 하지 않고 새 예약된 인스턴스 구입 하는 것이 좋습니다.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>비용을 절약 하려면 연결 되지 않은 공용 IP 주소를 삭제 합니다.

Advisor는 부하 분산 장치 또는 Vm과 같은 Azure 리소스에 현재 연결 되어 있지 않은 공용 IP 주소를 식별 합니다. 이러한 공용 IP 주소는 명목 요금이 부과 함께 제공 합니다. 비용 절감 사용 하지 않으려는 경우 삭제 될 수 있습니다.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>실패 하는 Azure Data Factory 파이프라인을 삭제 합니다.

Azure Advisor는 반복적으로 실패 하 고 문제를 해결 하거나 더 이상 필요 없는 경우 실패 한 파이프라인을 삭제 하는 것이 좋습니다는 Azure Data Factory 파이프라인을 검색 합니다. 하지만 하지 서비스를 제공 하면 실패 하는 동안 하는 경우에 이러한 파이프라인에 대 한 청구 됩니다. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Managed Disks에 대 한 표준 스냅숏을 사용 하 여
60%의 비용을 절약 하기 위해 스냅숏이 부모 디스크의 저장소 유형에 관계 없이 표준 저장소에 저장 하는 것이 좋습니다. Managed Disks 스냅숏에 대 한 기본 옵션입니다. Azure Advisor는 Premium Storage 및 Premium에서 Standard Storage에 스냅숏을 권장 되는 저장 된 스냅숏을 식별 합니다. [관리 디스크 가격 책정에 대해 자세히 알아보기](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **비용** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-cost-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-cost-recommendations.md)
* [Advisor 보안 권장 사항](advisor-cost-recommendations.md)
