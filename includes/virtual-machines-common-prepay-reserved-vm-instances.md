---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371660"
---
Azure 예약 VM 인스턴스에 커밋하면 비용을 절약할 수 있습니다. 예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 가상 머신 수에 자동으로 적용됩니다. 할인을 받기 위해 가상 컴퓨터에 예약을 할당할 필요가 없습니다. 예약된 인스턴스 구매는 VM 사용량의 계산 부분만 포함합니다. Windows VM의 경우 사용 미터는 두 개의 별도 미터로 분할됩니다. 리눅스 미터와 같은 계산 미터, 그리고 윈도우 IP 미터가있다. 구매 할 때 표시되는 요금은 계산 비용에 대해서만 표시됩니다. 요금에는 Windows 소프트웨어 비용이 포함되어 있지 않습니다. 소프트웨어 비용에 대한 자세한 내용은 [Azure 예약 VM 인스턴스에 포함되지 않은 소프트웨어 비용을](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)참조하십시오.

## <a name="determine-the-right-vm-size-before-you-buy"></a>구매하기 전에 적절한 VM 크기를 결정합니다.

예약을 구입하기 전에 필요한 VM의 크기를 결정해야 합니다. 다음 섹션에서는 올바른 VM 크기를 결정하는 데 도움이 됩니다.

### <a name="use-reservation-recommendations"></a>예약 권장 사항 사용

예약 권장 사항을 사용하여 구매해야 하는 예약을 결정할 수 있습니다.

- Azure 포털에서 VM 예약 인스턴스를 구입할 때 구매 권장 사항 및 권장 수량이 표시됩니다.
- Azure Advisor는 개별 구독에 대한 구매 권장 사항을 제공합니다.  
- API를 사용하여 공유 범위 및 단일 구독 범위에 대한 구매 권장 사항을 얻을 수 있습니다. 자세한 내용은 [엔터프라이즈 고객을 위한 예약 인스턴스 구매 권장 사항 API를](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)참조하십시오.
- EA(기업 계약) 및 MCA(Microsoft 고객 계약) 고객의 경우 [Azure 소비 인사이트 Power BI 콘텐츠 팩에서](/power-bi/service-connect-to-azure-consumption-insights)공유 및 단일 구독 범위에 대한 구매 권장 사항을 사용할 수 있습니다.

### <a name="services-that-get-vm-reservation-discounts"></a>VM 예약 할인을 가져오는 서비스

VM 예약은 VM 배포뿐만 아니라 여러 서비스에서 내보낸 VM 사용량에 적용될 수 있습니다. 예약 할인을 받는 리소스는 인스턴스 크기 유연성 설정에 따라 변경됩니다.

#### <a name="instance-size-flexibility-setting"></a>인스턴스 크기 유연성 설정

인스턴스 크기 유연성 설정은 예약 인스턴스 할인을 받는 서비스를 결정합니다.

설정이 on 또는 off인지 여부에 따라 예약 할인은 *ConsumedService*가 `Microsoft.Compute`일 때 일치하는 VM 사용량에 자동으로 적용됩니다. 따라서 *ConsumedService* 값에 대한 사용량 데이터를 확인합니다. 일부 사례:

- 가상 머신
- 가상 머신 크기 집합
- 컨테이너 서비스
- Azure Batch 배포(사용자 구독 모드에서)
- AKS(Azure Kubernetes Service)
- Service Fabric

설정이 on일 때 예약 할인은 *ConsumedService*가 다음 항목 중 하나에 해당하는 경우 일치하는 VM 사용량에 자동으로 적용됩니다.

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

사용량이 예약 할인에 적합한지 확인하려면 사용량 데이터의 *ConsumedService* 값을 확인합니다.

인스턴스 크기 유연성에 대한 자세한 내용은 [예약 VM 인스턴스에서 가상 머신 크기 유연성](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)을 참조하세요.

### <a name="analyze-your-usage-information"></a>사용 정보 분석

사용 정보를 분석하여 구매할 예약을 결정합니다. 사용 현황 데이터는 사용 파일 및 API에서 사용할 수 있습니다. 함께 사용하여 구매할 예약을 결정합니다. 매일 사용량이 많은 VM 인스턴스를 확인하여 구매할 예약 수량을 확인합니다. 사용 `Meter` 데이터의 하위 `Product` 범주 및 필드를 피하십시오. 프리미엄 저장소를 사용하는 VM 크기를 구분하지 않습니다. 이러한 필드를 사용하여 예약 구매의 VM 크기를 결정하는 경우 잘못된 크기를 구입할 수 있습니다. 그럼 당신은 당신이 기대하는 예약 할인을받을 수 없습니다. 대신 사용 파일의 `AdditionalInfo` 필드 또는 사용 API를 참조하여 올바른 VM 크기를 확인합니다.

사용 파일에는 청구 기간 및 일일 사용량에 따라 요금이 표시됩니다. 사용 파일 다운로드에 대한 자세한 내용은 [Azure 사용량 및 요금 보기 및 다운로드를](../articles/cost-management-billing/understand/download-azure-daily-usage.md)참조하십시오. 그런 다음 사용 파일 정보를 사용하여 [구매할 예약을 결정할](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)수 있습니다.

### <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약된 VM 인스턴스는 일부 예외를 제외하고 대부분의 VM 크기에 사용할 수 있습니다. 다음 VM에는 예약 할인이 적용되지 않습니다.

- **VM 시리즈** - A 시리즈, Av2 시리즈 또는 G 시리즈.

- **미리 보기 또는 프로모션 VM** - 미리 보기 또는 프로모션 미터를 사용하는 모든 VM 시리즈 또는 크기입니다.

- **클라우드** - 독일 또는 중국 지역에서는 예약을 구매할 수 없습니다.

- **할당량 부족** - 단일 구독으로 범위가 조정된 예약에는 새 RI에 대한 구독에서 vCPU 할당량을 사용할 수 있어야 합니다. 예를 들어 대상 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU인 경우 11개의 Standard_D1 인스턴스에 대해 예약을 구입할 수 없습니다. 예약에 대한 할당량 확인에는 구독에 이미 배포된 VM이 포함됩니다. 예를 들어, 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU이고 두 개의 standard_D1 인스턴스가 배포된 경우 이 구독에서 10개의 standard_D1 인스턴스에 대해 예약을 구입할 수 있습니다. 견적 [증가 요청을 만들어](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) 이 문제를 해결할 수 있습니다.

- **용량 제한** - 드문 경우지만 Azure는 지역의 낮은 용량으로 인해 VM 크기의 하위 집합에 대한 새 예약 구매를 제한합니다.

## <a name="buy-a-reserved-vm-instance"></a>예약 VM 인스턴스 구입

[Azure 포털에서](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)예약된 VM 인스턴스를 구입할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)로 처리할 수 있습니다.
이러한 요구 사항은 예약된 VM 인스턴스를 구입하는 데 적용됩니다.

- 하나 이상의 EA 구독또는 종량제 요금이 있는 구독의 소유자 역할에 있어야 합니다.
- EA 구독의 경우 **예약 인스턴스 추가** 옵션은 [EA 포털에서](https://ea.azure.com/)활성화되어야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구입하려면:

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. **모든 서비스** > **예약을 선택합니다.**
1. 새 예약을 구입하려면 **추가를** 선택한 다음 **가상 컴퓨터를**클릭합니다.
1. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치하는 VM 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받을 실제 VM 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

EA 계약이 있는 경우 **추가 옵션을** 사용하여 인스턴스를 빠르게 추가할 수 있습니다. 다른 구독 유형에는 이 옵션을 사용할 수 없습니다.


| 필드      | 설명|
|------------|--------------|
|Subscription|예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 수단에는 예약 비용이 부과됩니다. 구독 유형은 기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 Microsoft 고객 계약 또는 종량제 요금(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 구독이어야 합니다. 요금은 가능한 경우 금전적 약정 잔액에서 차감되거나 초과로 청구됩니다. 종량제 요금이 있는 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다.|    
|Scope       |예약 범위는 하나의 구독 또는 여러 구독(공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우: <ul><li>**단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li></ul>|
|지역    |예약이 적용되는 Azure 지역입니다.|    
|VM 크기     |VM 인스턴스의 크기입니다.|
|다음에 맞게 최적화     |VM 인스턴스 크기 유연성은 기본적으로 선택됩니다. **고급 설정을** 클릭하여 인스턴스 크기 유연성 값을 변경하여 동일한 [VM 크기 그룹의](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)다른 VM에 예약 할인을 적용합니다. 용량 우선 순위는 배포를 위해 데이터 센터 용량에서 우선됩니다. 필요할 때 VM 인스턴스를 시작할 수 있는 능력에 대한 추가적인 확신을 제공합니다. 용량 우선 순위는 예약 범위가 단일 구독일 때에 사용할 수 있습니다. |
|용어        |1년 또는 3년입니다.|
|수량    |예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 VM 인스턴스의 수입니다. 예를 들어 미국 동부에서 10개의 Standard_D2 VM을 실행하는 경우 수량을 10으로 지정하여 실행 중인 모든 VM의 이점을 최대화합니다. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>사용 데이터 및 예약 활용

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 각 예약에 대해 예약 할인을 받은 VM 인스턴스를 확인할 수 있습니다.

사용 데이터에 예약 할인이 표시되는 방식에 대한 자세한 내용은 EA 고객인 경우 [엔터프라이즈 등록에 대한 Azure 예약 사용량 이해하기를](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 참조하십시오. 개별 구독이 있는 경우 [종량제 구독에 대한 Azure 예약 사용량 이해하기를](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)참조하십시오.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

- 예약 범위 업데이트
- 인스턴스 크기 유연성(해당하는 경우)
- 소유권

또한 예약을 더 작은 청크로 분할하고 이미 분할된 예약을 병합할 수도 있습니다. 변경 사항 중 어느 것도 새로운 상업 거래를 일으키거나 예약 종료 날짜를 변경하지 않습니다.

구매 후 다음과 같은 유형의 변경은 직접 수행할 수 없습니다.

- 기존 예약 지역
- SKU
- 수량
- Duration

그러나 변경하려는 경우 예약을 *교환할* 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure 예약이란 무엇입니까?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Azure에서 예약 관리](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [예약 할인이 적용되는 방식 이해](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)
