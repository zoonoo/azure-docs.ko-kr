---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 19a153a5cdc9d5f878494984313baebd12dbcbb5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631166"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제

가상 머신에 대해 선불 결제하고 Azure Reserved VM(Virtual Machine) Instances를 사용하여 비용을 절감합니다. 자세한 내용은 [Azure Reserved VM Instances 제품](https://azure.microsoft.com/pricing/reserved-vm-instances/)을 참조하세요.

[Azure Portal](https://portal.azure.com)에서 예약 VM 인스턴스를 구입할 수 있습니다. 인스턴스를 구입하려면:

- 사용자가 하나 이상의 Enterprise 또는 종량제 구독에 대해 소유자 역할이어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 예약 구매가 활성화되어야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 예약 구매를 할 수 있습니다.

## <a name="determine-the-right-vm-size-before-purchase"></a>구매하기 전에 적절한 VM 크기를 결정합니다.

사용량 데이터의 미터 하위 범주 및 제품 필드는 프리미엄 저장소를 사용하지 않는 VM 크기와 프리미엄 저장소를 사용하는 VM 크기를 구분하지 않습니다. 이러한 필드를 사용하여 예약할 VM 크기를 결정하면 잘못된 예약 구매가 발생할 수 있고 사용자에게 예약 할인을 제공하지 않을 수 있습니다. 아래 방법 중 하나를 사용하여 예약 구매에 대한 적절한 VM 크기를 결정합니다.

- 예약 구매를 위한 적절한 VM 크기를 결정하려면 사용 파일 또는 사용량 API의 AdditionalInfo 필드를 참조하세요. 이러한 필드는 VM의 S 버전과 S 이외의 버전을 구분하지 않으므로 미터 하위 범주 또는 제품 필드의 값을 사용하지 마세요.
- Powershell, Azure Resource Manager 또는 Azure Portal의 VM 세부 정보를 사용하여 정확한 VM 크기 정보를 얻을 수도 있습니다.

예약 VM 인스턴스는 대부분의 VM 크기에서 이용할 수 있지만 몇 가지 예외가 있습니다.

- 미리 보기 상태의 VM: 미리 보기로 제공되는 VM 시리즈 또는 크기는 예약 구매에 사용할 수 없습니다.
- 클라우드: 예약은 Azure 미국 정부, 독일 또는 중국 지역에서 구매에 사용할 수 없습니다.
- 할당량 부족: 단일 구독으로 범위가 한정되는 예약은 vCPU 할당량을 새 RI에 대한 구독에서 사용할 수 있어야 합니다. 예를 들어 대상 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU인 경우 11개의 Standard_D1 인스턴스에 대해 예약을 구입할 수 없습니다. 예약에 대한 할당량 확인에는 구독에 이미 배포된 VM이 포함됩니다. 예를 들어, 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU이고 두 개의 standard_D1 인스턴스가 배포된 경우 이 구독에서 10개의 standard_D1 인스턴스에 대해 예약을 구입할 수 있습니다. 
- 용량 제한: 드문 경우, Azure는 지역의 낮은 용량으로 인해 VM 크기의 하위 집합에 대한 새로운 예약 구매를 제한합니다.

## <a name="buy-a-reserved-vm-instance"></a>예약 VM 인스턴스 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. **추가**를 선택하여 새 예약을 구입합니다.
4. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치하는 VM 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받을 실제 VM 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

    | 필드      | 설명|
    |:------------|:--------------|
    |Name        |이 예약의 이름입니다.| 
    |구독|예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업 계약(제안 번호: MS-AZR-0017P) 또는 종량제(제안 번호: MS-AZR-0003P)여야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.|    
    |범위       |예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>단일 구독 - 예약 할인이 이 구독의 VM에 적용됩니다. </li><li>공유 - 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 VM에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독(개발/테스트 구독 제외)을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</li></ul>|
    |위치    |예약 범위에 해당하는 Azure 지역입니다.|    
    |VM 크기     |VM 인스턴스의 크기입니다.|
    |다음에 맞게 최적화     |VM 인스턴스 크기 유연성은 동일한 [VM 크기 그룹](https://aka.ms/RIVMGroups)의 다른 VM에 예약 할인을 적용합니다. 용량 우선 순위는 배포를 위해 데이터 센터 용량을 예약합니다. 그러면 필요할 때 VM 인스턴스를 시작하는 기능을 더욱 신뢰할 수 있습니다. 용량 우선 순위는 예약 범위가 단일 구독일 때에 사용할 수 있습니다. |
    |용어        |1년 또는 3년입니다.|
    |수량    |예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 VM 인스턴스의 수입니다. 예를 들어 미국 동부 지역에서 10개의 Standard_D2 VM을 실행 중인 경우 실행 중인 모든 컴퓨터에 대한 혜택을 극대화하려면 수량을 10으로 지정할 수 있습니다. |
5. **비용 계산**을 선택하면 예약 비용을 볼 수 있습니다.

    ![예약 구매를 제출하기 전의 스크린샷](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. **구매**를 선택합니다.
7. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

    ![예약 구매를 제출한 후의 스크린샷](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 가상 머신 수에 자동으로 적용됩니다. [Azure Portal](https://portal.azure.com), PowerShell, CLI 또는 API를 통해 예약의 범위를 업데이트할 수 있습니다.

예약을 관리하는 방법을 알아보려면 [Azure 예약 관리](../articles/billing/billing-manage-reserved-vm-instance.md)를 참조합니다.

Azure 예약에 대한 자세한 내용은 다음 아티클을 참조하세요.

- [Azure 예약이란?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Azure에서 예약 관리](../articles/billing/billing-manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방법 이해](../articles/billing/billing-understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용 이해](../articles/billing/billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록의 예약 사용 이해](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
