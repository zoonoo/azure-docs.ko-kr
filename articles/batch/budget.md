---
title: 비용 분석 및 예산 - Azure 일괄 처리
description: 비용 분석을 받고 Batch 워크로드를 실행하는 데 사용되는 기본 컴퓨팅 리소스 및 소프트웨어 라이선스에 대한 예산을 설정하는 방법을 알아봅니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022718"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure 일괄 처리에 대한 비용 분석 및 예산

Azure Batch 자체에 대한 요금은 없으며 일괄 처리 워크로드를 실행하는 데 사용되는 기본 계산 리소스 및 소프트웨어 라이센스만 사용할 수 있습니다. 높은 수준에서 풀의 가상 시스템(VM), VM에서의 데이터 전송 또는 클라우드에 저장된 모든 입력 또는 출력 데이터로 비용이 발생합니다. Batch의 몇 가지 주요 구성 요소를 살펴보고 비용의 시작 부분, 풀 또는 계정에 대한 예산을 설정하는 방법 및 Batch 워크로드를 보다 비용 효율적으로 만드는 몇 가지 기술을 살펴보겠습니다.

## <a name="batch-resources"></a>Batch 리소스

가상 컴퓨터는 일괄 처리에 사용되는 가장 중요한 리소스입니다. 일괄 처리에 VM을 사용하는 비용은 유형, 수량 및 사용 기간에 따라 계산됩니다. VM 청구 옵션에는 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [예약(사전](../cost-management-billing/reservations/save-compute-costs-reservations.md) 결제)이 포함됩니다. 두 결제 옵션 모두 계산 워크로드에 따라 다른 이점이 있으며 두 결제 모델 모두 청구서에 다르게 영향을 미칩니다.

응용 프로그램이 응용 프로그램 패키지를 사용하여 일괄 처리 노드(VM)에 배포되는 경우 응용 프로그램 [패키지가](batch-application-packages.md)사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다. 또한 리소스 파일 및 기타 로그 데이터와 같은 입력 또는 출력 파일의 저장에 대한 요금이 청구됩니다. 일반적으로 Batch와 연결된 저장소 데이터의 비용은 계산 리소스 비용보다 훨씬 낮습니다. **VirtualMachineConfiguration에서** 만든 풀의 각 VM에는 Azure 관리 디스크를 사용하는 연결된 OS 디스크가 있습니다. Azure 관리 디스크에는 추가 비용이 있으며 다른 디스크 성능 계층도 서로 다른 비용을 가지게 됩니다.

일괄 처리 풀은 네트워킹 리소스를 사용합니다. 특히 가상 **머신 구성** 풀의 경우 정적 IP 주소가 필요한 표준 로드 밸로워서가 사용됩니다. Batch에서 사용하는 로드 밸로드미는 **사용자 구독** 계정에 표시되지만 **일괄 처리 서비스** 계정에는 표시되지 않습니다. 표준 로드 밸로드솔더는 Batch 풀 VM과 전달된 모든 데이터에 대해 요금이 부과됩니다. 풀 노드(예: 작업/노드 파일 받기), 작업 응용 프로그램 패키지, 리소스/출력 파일 및 컨테이너 이미지에서 데이터를 검색하는 일괄 처리 API를 선택합니다.

### <a name="additional-services"></a>서비스

VM 및 저장소를 포함하지 않는 서비스는 Batch 계정의 비용을 고려할 수 있습니다.

Batch와 함께 일반적으로 사용되는 다른 서비스에는 다음이 포함될 수 있습니다.

- 애플리케이션 정보
- Data Factory
- Azure Monitor
- Virtual Network
- 그래픽 응용 프로그램이 있는 VM

Batch 솔루션에서 사용하는 서비스에 따라 추가 요금이 부과될 수 있습니다. 각 추가 서비스의 비용을 확인하려면 [가격 계산기를](https://azure.microsoft.com/pricing/calculator/) 참조하십시오.

## <a name="cost-analysis-and-budget-for-a-pool"></a>풀에 대한 비용 분석 및 예산

Azure 포털을 통해 Batch 풀 또는 Batch 계정에 대한 예산 및 지출 경고를 만들 수 있습니다. 예산 및 경고는 초과 지출의 위험을 이해 관계자에게 알리는 데 유용합니다. 지출 경고가 지연되고 예산을 약간 초과할 수 있습니다. 이 예제에서는 개별 Batch 풀의 비용 분석을 봅니다.

1. Azure 포털에서 왼쪽 탐색 모음에서 **비용 관리 + 청구를** 선택합니다.
1. **내 구독** 섹션에서 구독 선택
1. 왼쪽 탐색 모음의 **비용 관리** 섹션에서 비용 **분석으로** 이동하여 다음과 같은 뷰를 표시합니다.
1. **필터 추가를**선택합니다. 첫 번째 드롭다운에서 **리소스** ![선택 리소스 필터](./media/batch-budget/resource-filter.png)
1. 두 번째 드롭다운에서 일괄 처리 풀을 선택합니다. 풀을 선택하면 비용 분석이 다음 분석과 유사하게 표시됩니다.
    ![풀의 비용 분석](./media/batch-budget/pool-cost-analysis.png)

결과 비용 분석은 풀 의 비용과 이 비용에 기여하는 리소스를 보여줍니다. 이 예제에서는 풀에 사용되는 VM이 가장 비용이 많이 드는 리소스입니다.

풀에 대한 예산을 만들려면 **예산 없음을**선택한 다음 **새 예산 만들기>** 를 선택합니다. 이제 창을 사용하여 풀에 맞게 특별히 예산을 구성합니다.

예산 구성에 대한 자세한 내용은 [Azure 예산 만들기 및 관리를](../cost-management-billing/costs/tutorial-acm-create-budgets.md)참조하십시오.

> [!NOTE]
> Azure Batch는 Azure Cloud Services 및 Azure Virtual Machines 기술을 기반으로 빌드됩니다. **Cloud Services 구성**을 선택하는 경우 Cloud Services 가격 구조에 따라 요금이 청구됩니다. **Virtual Machine 구성**을 선택하는 경우 Virtual Machines 가격 구조에 따라 요금이 청구됩니다. 이 페이지의 예제에서는 **가상 컴퓨터 구성을**사용합니다.

## <a name="minimize-cost"></a>비용 최소화

여러 VM 및 Azure 서비스를 장기간 사용하면 비용이 많이 들 수 있습니다. 다행히도 지출을 줄이는 데 도움이 되는 서비스와 워크로드의 효율성을 극대화하기 위한 전략이 있습니다.

### <a name="low-priority-virtual-machines"></a>우선 순위가 낮은 가상 시스템

우선 순위가 낮은 VM은 Azure에서 잉여 컴퓨팅 용량을 활용하여 일괄 처리 워크로드 비용을 줄입니다. 풀에서 우선 순위가 낮은 VM을 지정할 때 Batch는 이 잉여를 사용하여 워크로드를 실행합니다. 전용 VM 대신 우선 순위가 낮은 VM을 사용하면 상당한 비용 절감이 가능합니다.

일괄 처리가 있는 [우선 순위가 낮은 VM 사용에서](batch-low-pri-vms.md)워크로드에 대해 우선 순위가 낮은 VM을 설정하는 방법에 대해 자세히 알아봅니다.

### <a name="virtual-machine-os-disk-type"></a>가상 머신 OS 디스크 유형

여러 [VM OS 디스크 유형이](../virtual-machines/windows/disks-types.md)있습니다. 대부분의 VM 계열에는 프리미엄 및 표준 스토리지를 모두 지원하는 크기가 있습니다. 풀에 대해 's' VM 크기를 선택하면 Batch는 프리미엄 SSD OS 디스크를 구성합니다. '비'VM 크기를 선택하면 저렴하고 표준 HDD 디스크 유형이 사용됩니다. 예를 들어 프리미엄 SSD OS 디스크가 사용되고 `Standard_D2s_v3` 표준 HDD OS `Standard_D2_v3`디스크가 에 사용됩니다.

프리미엄 SSD OS 디스크는 더 비싸지만 성능이 높으며 프리미엄 디스크가 있는 VM은 표준 HDD OS 디스크가 있는 VM보다 약간 빠르게 시작할 수 있습니다. Batch에서는 응용 프로그램 및 작업 파일이 VM 임시 SSD 디스크에 있기 때문에 OS 디스크가 많이 사용되지 않는 경우가 많습니다. 따라서 대부분의 경우 's' VM 크기를 지정할 때 프로비저닝되는 프리미엄 SSD에 대한 증가된 비용을 지불할 필요가 없습니다.

### <a name="reserved-virtual-machine-instances"></a>예약된 가상 시스템 인스턴스

오랫동안 일괄 처리를 사용하려는 경우 워크로드에 [Azure 예약을](../cost-management-billing/reservations/save-compute-costs-reservations.md) 사용하여 VM 비용을 절감할 수 있습니다. 예약 률은 종량제 요금보다 상당히 낮습니다. 예약 없이 사용되는 가상 시스템 인스턴스는 종량제 요금으로 청구됩니다. 예약을 구매하는 경우 예약 할인이 적용되며 종량제 요금이 더 이상 부과되지 않습니다.

### <a name="automatic-scaling"></a>자동 크기 조정

[자동 크기 조정은](batch-automatic-scaling.md) 현재 작업의 요구에 따라 Batch 풀의 VM 수를 동적으로 확장합니다. 작업의 수명을 기준으로 풀을 확장하면 자동 크기 조정을 통해 VM을 확장하고 수행할 작업이 있는 경우에만 사용할 수 있습니다. 작업이 완료되거나 작업이 없는 경우 VM은 자동으로 축소되어 계산 리소스를 저장합니다. 크기 조정을 사용하면 필요한 리소스만 사용하여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

자동 크기 조정에 대한 자세한 내용은 [Azure Batch 풀에서 계산 노드 자동 조정을](batch-automatic-scaling.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 [빌드하고](batch-apis-tools.md) 모니터링하는 데 사용할 수 있는 Batch API 및 도구에 대해 자세히 알아보십시오.  

- 일괄 [처리를 사용하면 우선 순위가 낮은 VM에](batch-low-pri-vms.md)대해 알아봅니다.
