---
title: 비용 분석 및 예산
description: Batch 워크 로드를 실행 하는 데 사용 되는 기본 계산 리소스 및 소프트웨어 라이선스에 대 한 비용 분석을 가져오고, 예산을 설정 하 고, 비용을 줄이는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679319"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch의 비용 분석 및 예산

일괄 작업을 실행 하는 데 사용 되는 기본 계산 리소스 및 소프트웨어 라이선스에 대 한 요금이 있을 수 있지만 Azure Batch 자체 사용에 대 한 비용은 없습니다. 비용은 풀의 Vm (가상 머신), VM 으로부터의 데이터 전송 또는 클라우드에 저장 된 입력 또는 출력 데이터에서 발생할 수 있습니다. 이 항목은 비용이 발생 하는 위치, 배치 풀 또는 계정에 대 한 예산 설정 방법 및 Batch 워크 로드의 비용을 줄이는 방법을 이해 하는 데 도움이 됩니다.

## <a name="batch-resources"></a>Batch 리소스

가상 머신은 Batch 처리에 사용되는 가장 중요한 리소스입니다. Batch의 VM 사용 비용은 유형, 수량 및 사용 기간을 기준으로 계산됩니다. VM 청구 옵션에는 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 및 [예약](../cost-management-billing/reservations/save-compute-costs-reservations.md)(선불) 등이 있습니다. 두 지불 옵션은 계산 워크 로드에 따라 이점이 다르며 청구서에 다르게 영향을 줍니다.

[애플리케이션 패키지](batch-application-packages.md)를 사용하여 Batch 노드(VM)에 애플리케이션을 배포하는 경우 애플리케이션 패키지에서 사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다. 또한 리소스 파일 및 기타 로그 데이터와 같은 입력 또는 출력 파일의 스토리지에 대한 요금도 청구됩니다. 일반적으로 Batch와 연결된 스토리지 데이터의 비용이 컴퓨팅 리소스 비용보다 훨씬 적습니다. [가상 컴퓨터 구성](nodes-and-pools.md#virtual-machine-configuration) 으로 만든 풀의 각 VM에는 Azure에서 관리 되는 디스크를 사용 하는 연결 된 OS 디스크가 있습니다. Azure 관리 디스크에는 추가 비용이 부과되며 다른 디스크 성능 계층에도 다른 비용이 부과됩니다.

Batch 풀은 네트워킹 리소스를 사용합니다. 특히 **VirtualMachineConfiguration** 풀의 경우 고정 IP 주소가 필요한 표준 부하 분산 장치가 사용 됩니다. Batch에서 사용하는 부하 분산 장치는 **사용자 구독** 계정에 표시되지만 **Batch 서비스** 계정에는 표시되지 않습니다. 표준 부하 분산 장치는 Batch 풀 VM에서 들어오고 나가는 모든 데이터에 요금을 부과합니다. 풀 노드(예: 가져오기 작업/노드 파일)에서 데이터를 검색하는 Batch API, 작업 애플리케이션 패키지, 리소스/출력 파일 및 컨테이너 이미지를 선택하면 요금이 발생합니다.

### <a name="additional-services"></a>서비스

VM 및 스토리지를 포함하지 않는 서비스는 Batch 계정의 비용으로 간주될 수 있습니다.

Batch와 함께 일반적으로 사용되는 다른 서비스는 다음과 같습니다.

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- 그래픽 애플리케이션이 있는 VM

Batch 솔루션과 함께 사용하는 서비스에 따라 추가 요금이 발생할 수 있습니다. 각 추가 서비스의 비용을 확인하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요.

## <a name="cost-analysis-and-budget-for-a-pool"></a>풀의 비용 분석 및 예산

Azure Portal에서 Batch 풀 또는 Batch 계정에 대 한 예산 및 지출 경고를 만들 수 있습니다. 예산과 경고는 경고를 지출 하는 데 지연이 발생 하 고 예산을 약간 초과 하는 것이 가능 하지만 관련자에 게 과도 한 지출 위험을 알리는 데 유용 합니다.

이 예에서는 개별 Batch 풀의 비용 분석을 볼 수 있습니다.

1. Azure Portal에서를 입력 하거나 **Cost Management + 청구** 를 선택 합니다.
1. **청구 범위** 섹션에서 구독을 선택 합니다.
1. **Cost Management** 에서 **비용 분석** 을 선택합니다.
1. **필터 추가** 를 선택합니다. 첫 번째 드롭다운에서 **리소스** 를 선택 합니다.
1. 두 번째 드롭다운 목록에서 Batch 풀을 선택합니다. 풀을 선택 하면 여기에 표시 된 예제와 비슷한 풀에 대 한 비용 분석이 표시 됩니다.
    ![Azure Portal의 풀에 대 한 비용 분석을 보여 주는 스크린샷](./media/batch-budget/pool-cost-analysis.png)

비용 분석 결과는 이 비용에 영향을 주는 리소스뿐만 아니라 풀의 비용도 보여 줍니다. 이 예에서는 풀에 사용된 VM이 가장 비용이 많이 드는 리소스입니다.

풀에 대 한 예산을 만들려면 **예산: 없음** 을 선택 하 고 **새 예산 >만들기** 를 선택 합니다. 이제이 창을 사용 하 여 풀에 대해 특별히 [예산을 구성](../cost-management-billing/costs/tutorial-acm-create-budgets.md) 합니다.

> [!NOTE]
> Azure Batch는 Azure Cloud Services 및 Azure Virtual Machines 기술을 기반으로 빌드됩니다. **Cloud Services 구성** 을 선택하는 경우 Cloud Services 가격 책정 체계에 따라 요금이 청구됩니다. **Virtual Machine 구성** 을 선택하는 경우 Virtual Machines 가격 책정 체계에 따라 요금이 청구됩니다. 이 페이지의 예제에서는 대부분의 Batch 풀에 권장 되는 **가상 머신 구성을** 사용 합니다.

## <a name="minimize-cost"></a>비용 최소화

오랜 기간 동안 여러 VM 및 Azure 서비스를 사용하면 비용이 많이 들 수 있습니다. 이러한 전략을 사용 하 여 워크 로드의 효율성을 최대화 하 고 비용을 줄이는 것이 좋습니다.

### <a name="low-priority-virtual-machines"></a>우선 순위가 낮은 가상 머신

[우선 순위가 낮은 vm](batch-low-pri-vms.md) 은 Azure에서 여분의 컴퓨팅 용량을 활용 하 여 Batch 워크 로드의 비용을 줄입니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Batch는 이러한 남는 용량을 사용하여 워크로드를 실행할 수 있습니다. 전용 Vm 대신 우선 순위가 낮은 Vm을 사용 하는 경우 비용을 크게 절감할 수 있습니다.

### <a name="virtual-machine-os-disk-type"></a>가상 머신 OS 디스크 유형

Azure는 여러 [VM OS 디스크 유형을](../virtual-machines/disks-types.md)제공 합니다. 대부분의 VM 시리즈는 프리미엄 및 표준 스토리지를 모두 지원하는 크기를 가집니다. 풀에 대해의 VM 크기를 선택 하면 Batch는 프리미엄 SSD OS 디스크를 구성 합니다. ' 비 s ' VM 크기를 선택 하면 더 저렴 한 표준 HDD 디스크 유형이 사용 됩니다. 예를 들어 프리미엄 SSD OS 디스크는 `Standard_D2s_v3`에 사용되고 표준 HDD OS 디스크는 `Standard_D2_v3`에 사용됩니다.

프리미엄 SSD OS 디스크는 비용이 더 많이 들지만 성능은 높습니다. 프리미엄 디스크를 사용 하는 Vm은 표준 HDD OS 디스크를 사용 하는 Vm 보다 약간 빠르게 시작할 수 있습니다. 일괄 처리를 사용 하면 응용 프로그램 및 태스크 파일이 VM의 임시 SSD 디스크에 위치 하므로 OS 디스크는 자주 사용 되지 않습니다. 이 때문에 ' VM 크기를 지정할 때 프로 비전 되는 프리미엄 SSD의 비용을 지불 하지 않도록 ' s s ' VM 크기를 선택할 수 있습니다.

### <a name="reserved-virtual-machine-instances"></a>예약 가상 머신 인스턴스

장시간 배치를 사용 하려는 경우 작업에 [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) 를 사용 하 여 vm의 비용을 줄일 수 있습니다. 예약 요금은 종량제 요금보다 훨씬 저렴합니다. 예약 없이 사용 되는 가상 머신 인스턴스는 종 량 제 요금으로 청구 됩니다. 예약을 구매 하는 경우 예약 할인이 적용 됩니다.

### <a name="automatic-scaling"></a>자동 크기 조정

[자동 크기 조정](batch-automatic-scaling.md)은 현재 작업의 요구에 따라 Batch 풀의 VM 수를 동적으로 조정합니다. 자동 크기 조정은 작업 수명에 따라 풀의 크기를 조정 하 여 수행할 작업이 있는 경우에만 Vm을 확장 하 고 사용할 수 있도록 합니다. 작업이 완료 되거나 작업이 없는 경우 Vm은 계산 리소스를 저장 하도록 자동으로 축소 됩니다. 스케일링을 사용하면 필요한 리소스만 사용하여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 빌드하고 모니터링하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 자세히 알아봅니다.  
- [Batch로 우선 순위가 낮은 vm을 사용 하는](batch-low-pri-vms.md)방법에 대해 알아봅니다.
