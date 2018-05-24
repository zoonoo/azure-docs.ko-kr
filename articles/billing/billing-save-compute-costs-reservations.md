---
title: Azure 가상 머신 선불로 비용 절감 - Azure | Microsoft Docs
description: 가상 머신 비용을 절약하려면 Azure Reserved Virtual Machine Instance에 대해 알아보세요.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: yashar
ms.openlocfilehash: 9c73102f09e252b449f78603debaf707b3c89c3b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34056689"
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>예약 가상 머신 인스턴스를 사용하여 가상 머신에서 비용을 절감합니다. 
예약 가상 머신 인스턴스를 사용하면 1년 또는 3년 계산 용량을 선불로 결제하여 사용하는 가상 머신에서 할인을 받을 수 있습니다. 1년 또는 3년 사전 약정을 통해 종량제 가격으로 최대 72%까지 가상 머신 비용을 크게 절감할 수 있습니다. 예약 가상 머신 인스턴스는 청구 할인으로, 가상 머신의 런타임 상태에 영향을 주지 않습니다.

[Azure Portal](https://aka.ms/reservations)에서 예약 가상 머신 인스턴스를 구매할 수 있습니다. 자세한 내용은 [가상 머신에 대해 선불 결제하고 예약 가상 머신 인스턴스를 사용하여 비용 절감](https://go.microsoft.com/fwlink/?linkid=861721)을 참조하세요.

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>예약 가상 머신 인스턴스를 구매해야 하는 이유
오랜 시간 동안 실행되는 가상 머신이 있는 경우 예약 가상 머신 인스턴스를 구매하면 가장 효율적인 가격이 제공됩니다. 예를 들어 미국 서부 지역에 표준 D2 인스턴스 4개를 지속적으로 실행하는 경우 예약이 없으면 종량제로 청구됩니다. 4개의 VM에 대해 예약 가상 머신 인스턴스를 구매하면 해당 VM은 즉시 청구 이점을 얻을 수 있습니다. 더 이상 종량제 요금으로 청구되지 않습니다. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>예약 가상 머신 인스턴스에서 적용되는 요금 범위
예약에는 Windows 또는 Linux 가상 머신에 대한 가상 머신 인프라 요금만 적용됩니다. 예약은 소프트웨어, 네트워킹 또는 저장소에 대한 추가 요금이 적용되지 않습니다. Windows 가상 머신의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 Windows 라이선스 비용을 처리할 수 있습니다.

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>예약 가상 머신 인스턴스를 구매할 수 있는 대상
다음과 같은 구독 유형을 사용하는 Azure 고객은 예약 가상 머신 인스턴스를 구매할 수 있습니다.
-   Enterprise 계약 구독 제품 유형(MS-AZR-0017P).
-   [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독 제품 유형(MS-AZR-003P).
예약 인스턴스를 구매하려면 해당 구독에 대한 "소유자" 역할이 있어야 합니다. 엔터프라이즈 등록으로 예약을 구매하는 경우 엔터프라이즈 관리자는 기본적으로 설정을 활성화하여 EA 포털에서 예약을 지원해야 합니다.
-   CSP(클라우드 솔루션 공급자) 파트너는 Azure 관리 포털 또는 [파트너 센터](https://docs.microsoft.com/partner-center/azure-reservations)를 사용하여 예약을 구입할 수 있습니다.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>예약 가상 머신 인스턴스 청구 방법
예약 구매는 구독에 연결된 지불 방법으로 청구됩니다. Enterprise 구독이 있는 경우 예약 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액으로 예약 비용이 해결되지 않는 경우 초과분이 청구됩니다.
종량제 구독이 있는 경우 계정에 등록된 신용 카드로 즉시 청구됩니다. 청구서로 청구하는 경우는 다음 번 청구서에 요금이 표시됩니다.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>예약 가상 머신 인스턴스의 할인 적용 방법
예약 가상 머신 인스턴스 할인은 예약을 구매할 때 선택한 특성과 일치하는 가상 머신에 적용됩니다. 특성에는 일치하는 VM이 실행되는 범위가 포함됩니다. 예를 들어 미국 서부 지역에서 4개의 표준 D2 가상 머신에 대한 예약 VM 인스턴스 할인을 원하는 경우 VM이 실행되고 있는 구독을 선택합니다. 가상 머신이 사용자의 등록/계정 내 다른 구독에서 실행되는 경우는 범위를 공유로 선택합니다. 공유 범위를 사용하면 예약 할인을 구독 전체에 적용할 수 있습니다.
예약 VM 인스턴스를 구매한 후 범위를 변경할 수 있습니다. 범위를 변경하려면 예약 관리 방법에 대한 문서를 참조하세요.

예약 할인은 엔터프라이즈 또는 종량제 제품 유형의 구독에 있는 가상 머신에만 적용됩니다. 다른 제품 유형의 구독에서 실행되는 가상 머신은 예약 할인을 받지 못합니다. 엔터프라이즈 등록의 경우 엔터프라이즈 개발/테스트 구독에서 예약 인스턴스의 혜택을 이용할 수 없습니다.

예약이 가상 머신 청구에 미치는 영향은 [예약 청구 혜택의 응용 사례에 대한 이해](https://go.microsoft.com/fwlink/?linkid=863405)에 설명되어 있습니다.

## <a name="what-happens-when-the-reservation-term-expires"></a>예약 기간이 만료되는 경우
예약 기간 종료 후 청구 할인이 만료되고 가상 머신 인프라는 종량제 가격으로 청구됩니다. 예약은 자동 갱신되지 않습니다. 청구 할인 혜택을 계속 받으려면 새 예약 가상 머신 인스턴스를 구매해야 합니다. 

## <a name="sizes-and-regional-availability"></a>크기 및 국가별 가용성
예약은 대부분의 VM 크기에서 이용할 수 있지만 몇 가지 예외가 있습니다.
- 미리 보기 VM 크기 – 미리 보기의 모든 크기에 대해 예약 가상 머신 인스턴스를 구매할 수는 없습니다.
- 클라우드 - 예약 가상 머신 인스턴스는 Azure 미국 정부, 독일 또는 중국 지역에서 구매할 수 없습니다. 
- 할당량 부족 - 단일 구독으로 범위가 한정되는 예약 VM 인스턴스는 vCPU 할당량을 새 RI에 대한 구독에서 사용할 수 있어야 합니다. 예를 들어 대상 구독에서 D 시리즈 제품군에 대한 할당량 한도가 10개 vCPU인 경우 11개의 Standard_D1 인스턴스에 대한 예약 VM 인스턴스를 구매할 수 없습니다. 예약에 대한 할당량 확인에는 구독에 이미 배포된 VM이 포함됩니다. 구독에 D 시리즈 제품군에 대한 10개 vCPU 할당량이 있는 경우를 예로 들 수 있습니다. 이 구독에서 두 개의 standard_D1 인스턴스가 배포된 경우, 이 구독에서 10개의 standard_D1 인스턴스에 이 구독에 대한 예약 VM 인스턴스를 구매할 수 있습니다. 
- 용량 제한 - 드문 경우, Azure는 지역의 낮은 용량으로 인해 VM 크기의 하위 집합에 대한 새로운 예약 구매를 제한합니다.

## <a name="next-steps"></a>다음 단계
[예약 가상 머신 인스턴스](https://go.microsoft.com/fwlink/?linkid=861721) 구매를 통해 가상 머신에서 비용 절감을 시작합니다. 

예약 가상 머신 인스턴스에 대한 자세한 내용은 다음 문서를 참조하세요.

- [예약 VM 인스턴스를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [예약 가상 머신 인스턴스 관리](billing-manage-reserved-vm-instance.md)
- [예약 가상 머신 인스턴스 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독에서 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage.md)
- [Enterprise 등록의 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [예약 인스턴스를 포함하지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 예약 인스턴스](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
