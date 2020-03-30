---
title: Azure Advisor를 사용하여 서비스 비용 절감
description: Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259696"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Azure Advisor를 사용하여 서비스 비용 절감

Advisor는 유휴 및 사용 미달 리소스를 식별하여 전체적인 Azure 사용을 최적화하고 줄이는 데 도움을 줍니다.Advisor 대시보드의 **비용** 탭에서 비용 관련 권장 지침을 얻을 수 있습니다.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>사용량이 낮은 인스턴스의 크기를 조정하거나 종료하여 가상 머신 소비 최적화 

특정 애플리케이션 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 머신의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. Advisor 고급 평가 모델은 CPU 사용률의 최대 값 P95가 3% 미만이고 네트워크 사용률이 7일 동안 2% 미만일 때 가상 머신을 종료하는 것으로 간주합니다. 가상 머신은 현재 부하를 더 작은 SKU(동일한 SKU 제품군 내) 또는 더 작은 수의 인스턴스에 맞출 수 있을 때 적절한 크기로 간주되므로 사용자 대면 워크로드가 아닌 경우 현재 로드가 80% 이상 사용률을 초과하지 않고 사용자 대면 워크로드에서 40%를 초과하지 않습니다. 여기서, 워크로드의 유형은 워크로드의 CPU 사용률 특성을 분석하여 결정된다.

권장되는 작업은 권장되는 리소스에 따라 종료되거나 크기 가 조정됩니다. Advisor는 권장 작업(크기 조정 또는 종료)에 대한 예상 비용 절감 효과를 보여 줄 수 있습니다. 또한 권장 작업의 크기를 조정하기 위해 Advisor는 현재 및 대상 SKU 정보를 제공합니다. 

활용도가 낮은 가상 컴퓨터를 식별하는 데 더 적극적으로 사용하려면 구독별로 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>프로비저닝되지 않은 ExpressRoute 회로를 제거하여 비용 절감

Advisor는 공급자 상태가 1개월 이상 *프로비저닝되지 않음*인 ExpressRoute 회로를 식별하고, 연결 공급자로 회로를 프로비저닝할 계획이 아닌 경우 회로를 삭제할 것을 권장합니다.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>유휴 상태의 가상 네트워크 게이트웨이를 삭제하거나 다시 구성하여 비용 절감

Advisor는 90일 이상 유휴 상태인 가상 네트워크 게이트웨이를 식별합니다. 이러한 게이트웨이는 시간당 요금이 부과되므로 다시 구성하거나, 더 이상 사용하지 않으려는 경우 삭제를 고려해야 합니다. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>예약 가상 머신 인스턴스를 구매하여 종량제 비용보다 비용 절약

Advisor는 지난 30일 동안 가상 머신 사용량을 검토하고Azure 예약을 구매하여 비용을 절감할 수 있는지 확인합니다. Advisor는 잠재적으로 가장 절약할 수 있는 지역 및 크기를 보여주고, 예약 구매로 예상되는 절감을 보여줍니다. Azure 예약을 사용하여 가상 머신에 대한 기본 비용을 미리 구입할 수 있습니다. 할인은 예약과 동일한 크기 및 지역이 있는 새로운 또는 기존 VM에 자동으로 적용됩니다. [Azure Reserved VM Instances에 대한 자세한 정보](https://azure.microsoft.com/pricing/reserved-vm-instances/)

또한 어드바이저는 향후 30일 이내에 만료될 예약 인스턴스를 알려줍니다. 종량제 가격을 지불하지 않도록 새 예약 인스턴스를 구입하는 것이 좋습니다.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>비용을 절감하기 위해 관련되지 않은 공용 IP 주소 삭제

Advisor는 로드 밸런서 또는 VM과 같은 Azure 리소스에 현재 연결되지 않은 공용 IP 주소를 식별합니다. 이러한 공용 IP 주소는 명목상 요금이 부과됩니다. 사용하지 않을 경우 삭제하면 비용이 절감될 수 있습니다.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>실패한 Azure Data Factory 파이프라인 삭제

Azure Advisor는 반복적으로 실패하는 Azure Data Factory 파이프라인을 검색하고 더 이상 필요하지 않은 경우 문제를 해결하거나 실패한 파이프라인을 삭제하는 것이 좋습니다. 파이프라인이 실패하는 동안 서비스를 제공하지 않더라도 이러한 파이프라인에 대한 요금이 청구됩니다. 

## <a name="use-standard-snapshots-for-managed-disks"></a>관리 디스크에 표준 스냅숏 사용
60%의 비용을 절감하려면 부모 디스크의 스토리지 유형과 관계없이 Standard Storage에 스냅샷을 저장하는 것이 좋습니다. 이 옵션은 관리되는 디스크 스냅숏의 기본 옵션입니다. Azure Advisor는 프리미엄 저장소에 저장된 스냅숏을 식별하고 스냅샷을 프리미엄 저장소에서 표준 저장소로 마이그레이션하는 것이 좋습니다. [관리 디스크 가격 책정에 대해 자세히 알아보기](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>수명 주기 관리 활용
Azure Advisor는 Azure Blob 저장소 개체 수, 총 크기 및 트랜잭션에 대한 인텔리전스를 활용하여 하나 이상의 저장소 계정이 계층 데이터로 수명 주기 관리를 활성화하는 데 가장 적합한지 검색합니다. 응용 프로그램 호환성을 위해 Azure Blob 저장소에 데이터를 유지하면서 저장소 비용을 최적화하기 위해 데이터를 쿨 또는 보관에 자동으로 계층화하는 수명 주기 관리 규칙을 만들도록 메시지가 표시됩니다.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. 모든 페이지에서 [**어드바이저를**](https://aka.ms/azureadvisordashboard) 검색하고 선택합니다.

1. **어드바이저** 대시보드에서 **비용** 탭을 선택합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [고문 운영 우수성 권장 사항](advisor-operational-excellence-recommendations.md)
