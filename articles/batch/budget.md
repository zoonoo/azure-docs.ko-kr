---
title: Azure Batch에 대 한 비용 분석 및 예산 설정
description: Batch 워크 로드를 실행 하는 데 사용 되는 기본 계산 리소스 및 소프트웨어 라이선스에 대 한 비용 분석을 가져오고, 예산을 설정 하 고, 비용을 줄이는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091330"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Azure Batch에 대 한 비용 분석 및 예산 설정

이 항목에서는 Azure Batch와 관련 된 비용, 배치 풀 또는 계정의 예산 설정 방법 및 Batch 작업의 비용을 줄이는 방법을 이해 하는 데 도움이 됩니다.

## <a name="understand-costs-associated-with-batch-resources"></a>일괄 처리 리소스와 관련 된 비용 이해

일괄 작업을 실행 하는 데 사용 되는 기본 계산 리소스 및 소프트웨어 라이선스에 대 한 요금이 있을 수 있지만 Azure Batch 자체 사용에 대 한 비용은 없습니다. 비용은 풀의 Vm (가상 머신), VM 으로부터의 데이터 전송 또는 클라우드에 저장 된 입력 또는 출력 데이터에서 발생할 수 있습니다.

### <a name="virtual-machines"></a>가상 머신

가상 머신은 Batch 처리에 사용되는 가장 중요한 리소스입니다. Batch의 VM 사용 비용은 유형, 수량 및 사용 기간을 기준으로 계산됩니다. VM 청구 옵션에는 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 및 [예약](../cost-management-billing/reservations/save-compute-costs-reservations.md)(선불) 등이 있습니다. 두 지불 옵션은 계산 워크 로드에 따라 이점이 다르며 청구서에 다르게 영향을 줍니다.

[가상 컴퓨터 구성](nodes-and-pools.md#virtual-machine-configuration) 으로 만든 풀의 각 VM에는 Azure에서 관리 되는 디스크를 사용 하는 연결 된 OS 디스크가 있습니다. Azure 관리 디스크에는 추가 비용이 부과되며 다른 디스크 성능 계층에도 다른 비용이 부과됩니다.

### <a name="storage"></a>스토리지

[애플리케이션 패키지](batch-application-packages.md)를 사용하여 Batch 노드(VM)에 애플리케이션을 배포하는 경우 애플리케이션 패키지에서 사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다. 또한 리소스 파일 및 기타 로그 데이터와 같은 입력 또는 출력 파일의 저장소에 대 한 요금이 청구 됩니다.

일반적으로 Batch와 연결된 스토리지 데이터의 비용이 컴퓨팅 리소스 비용보다 훨씬 적습니다.

### <a name="networking-resources"></a>네트워킹 리소스

Batch 풀은 네트워킹 리소스를 사용 하며, 그 중 일부는 연결 된 비용이 있습니다. 특히 가상 머신 구성 풀의 경우 고정 IP 주소가 필요한 표준 부하 분산 장치가 사용 됩니다. Batch에서 사용 하는 부하 분산 장치는 사용자 구독 모드에서 구성 된 [계정](accounts.md#batch-accounts) 에 대해 표시 되지만 batch 서비스 모드의 부하 분산 장치에는 표시 되지 않습니다.

표준 부하 분산 장치는 Batch 풀 Vm에 전달 되는 모든 데이터에 대해 요금을 부과 합니다. 풀 노드 (예: 작업/노드 파일 가져오기)에서 데이터를 검색 하는 Batch Api를 선택 하 고, 작업 응용 프로그램 패키지, 리소스/출력 파일 및 컨테이너 이미지에도 요금이 부과 됩니다.

### <a name="additional-services"></a>서비스

Batch 솔루션과 함께 사용하는 서비스에 따라 추가 요금이 발생할 수 있습니다. 각 추가 서비스의 비용을 확인하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요. 관련 비용이 포함 될 수 있는 Batch와 함께 일반적으로 사용 되는 서비스는 다음과 같습니다.

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- 그래픽 애플리케이션이 있는 VM

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>비용 분석 보기 및 풀에 대 한 예산 만들기

Azure Portal에서 Batch 풀 또는 Batch 계정에 대 한 예산 및 지출 경고를 만들 수 있습니다. 예산과 경고는 경고를 지출 하는 데 지연이 발생 하 고 예산을 약간 초과 하는 것이 가능 하지만 관련자에 게 과도 한 지출 위험을 알리는 데 유용 합니다.

> [!NOTE]
> 이 예제의 풀은 **가상 머신 구성을** 사용 합니다 .이 구성은 대부분의 풀에 권장 되며 Virtual Machines 가격 책정 구조에 따라 요금이 청구 됩니다. **Cloud Services 구성을** 사용 하는 풀은 Cloud Services 가격 책정 구조를 기준으로 요금이 청구 됩니다.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Batch 풀에 대 한 비용 분석 보기

1. Azure Portal에서를 입력 하거나 **Cost Management + 청구** 를 선택 합니다.
1. **청구 범위** 섹션에서 구독을 선택 합니다.
1. **Cost Management** 에서 **비용 분석** 을 선택합니다.
1. **필터 추가** 를 선택합니다. 첫 번째 드롭다운에서 **리소스** 를 선택 합니다.
1. 두 번째 드롭다운 목록에서 Batch 풀을 선택합니다. 풀을 선택 하면 여기에 표시 된 예제와 비슷한 풀에 대 한 비용 분석이 표시 됩니다.
    ![Azure Portal의 풀에 대 한 비용 분석을 보여 주는 스크린샷](./media/batch-budget/pool-cost-analysis.png)

비용 분석 결과는 이 비용에 영향을 주는 리소스뿐만 아니라 풀의 비용도 보여 줍니다. 이 예에서는 풀에 사용된 VM이 가장 비용이 많이 드는 리소스입니다.

### <a name="create-a-budget-for-a-batch-pool"></a>Batch 풀에 대 한 예산 만들기

1. **비용 분석** 페이지에서 **예산: 없음** 을 선택 합니다.
1. **새 예산 >만들기** 를 선택 합니다.
1. 결과 창을 사용 하 여 풀에 대해 특별히 예산을 구성할 수 있습니다. 자세한 내용은 [자습서: Azure 예산 만들기 및 관리](../cost-management-billing/costs/tutorial-acm-create-budgets.md)를 참조 하세요.

## <a name="minimize-costs-associated-with-azure-batch"></a>Azure Batch와 관련 된 비용 최소화

시나리오에 따라 비용을 최대한 줄일 수 있습니다. 이러한 전략 중 하나 이상을 사용 하 여 워크 로드의 효율성을 최대화 하 고 잠재적인 비용을 줄이는 것이 좋습니다.

### <a name="use-low-priority-virtual-machines"></a>낮은 우선 순위의 가상 컴퓨터 사용

[우선 순위가 낮은 vm](batch-low-pri-vms.md) 은 Azure에서 여분의 컴퓨팅 용량을 활용 하 여 Batch 워크 로드의 비용을 줄입니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Batch는 이러한 남는 용량을 사용하여 워크로드를 실행할 수 있습니다. 전용 Vm 대신 우선 순위가 낮은 Vm을 사용 하는 경우 비용을 크게 절감할 수 있습니다.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>표준 가상 머신 OS 디스크 유형 선택

Azure는 여러 [VM OS 디스크 유형을](../virtual-machines/disks-types.md)제공 합니다. 대부분의 VM 시리즈는 프리미엄 및 표준 스토리지를 모두 지원하는 크기를 가집니다. 풀에 대해의 VM 크기를 선택 하면 Batch는 프리미엄 SSD OS 디스크를 구성 합니다. ' 비 s ' VM 크기를 선택 하면 더 저렴 한 표준 HDD 디스크 유형이 사용 됩니다. 예를 들어 프리미엄 SSD OS 디스크는 `Standard_D2s_v3`에 사용되고 표준 HDD OS 디스크는 `Standard_D2_v3`에 사용됩니다.

프리미엄 SSD OS 디스크는 비용이 더 많이 들지만 성능은 높습니다. 프리미엄 디스크를 사용 하는 Vm은 표준 HDD OS 디스크를 사용 하는 Vm 보다 약간 빠르게 시작할 수 있습니다. 일괄 처리를 사용 하면 응용 프로그램 및 태스크 파일이 VM의 임시 SSD 디스크에 위치 하므로 OS 디스크는 자주 사용 되지 않습니다. 이 때문에 ' VM 크기를 지정할 때 프로 비전 되는 프리미엄 SSD의 비용을 지불 하지 않도록 ' s s ' VM 크기를 선택할 수 있습니다.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>가상 컴퓨터 인스턴스에 대 한 구매 예약

장시간 배치를 사용 하려는 경우 작업에 [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) 를 사용 하 여 vm의 비용을 줄일 수 있습니다. 예약 요금은 종량제 요금보다 훨씬 저렴합니다. 예약 없이 사용 되는 가상 머신 인스턴스는 종 량 제 요금으로 청구 됩니다. 예약을 구매 하는 경우 예약 할인이 적용 됩니다.

### <a name="use-automatic-scaling"></a>자동 크기 조정 사용

[자동 크기 조정](batch-automatic-scaling.md)은 현재 작업의 요구에 따라 Batch 풀의 VM 수를 동적으로 조정합니다. 자동 크기 조정은 작업 수명에 따라 풀의 크기를 조정 하 여 수행할 작업이 있는 경우에만 Vm을 확장 하 고 사용할 수 있도록 합니다. 작업이 완료 되거나 작업이 없는 경우 Vm은 계산 리소스를 저장 하도록 자동으로 축소 됩니다. 스케일링을 사용하면 필요한 리소스만 사용하여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management + 청구](../cost-management-billing/cost-management-billing-overview.md) 에 대해 자세히 알아보기
- [Batch로 우선 순위가 낮은 vm을 사용 하는](batch-low-pri-vms.md)방법에 대해 알아봅니다.
