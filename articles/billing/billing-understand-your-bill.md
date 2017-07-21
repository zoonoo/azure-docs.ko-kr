---
title: "Azure 청구서 이해 | Microsoft Docs"
description: "Azure 구독에 대한 사용량 및 청구를 읽고 이해하는 방법에 대해 알아봅니다."
services: 
documentationcenter: 
author: genlin
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: erihur;genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: b5268239fb4c50e6bdea77323267a42e267268db
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure 청구서 이해
Azure 청구서를 이해하려면 청구서 요금 요약 및 별도의 자세한 일별 사용 현황 파일을 검토합니다. 이 문서에서는 대부분의 청구서 및 일별 사용 현황 파일에 표시된 용어 대부분에 대해 설명합니다. 이러한 파일을 가져오려면 [Azure 청구서 및 일별 사용 현황 데이터를 받는 방법](billing-download-azure-invoice-daily-usage-date.md)을 참조하세요. 무료 평가판 구독을 사용하는 경우 일별 사용 현황 정보를 얻을 수 있으나 청구서는 없습니다.

Microsoft Azure 구독 요금은 요금제에 따라 다릅니다. Visual Studio Enterprise(MPN) 구독자와 같은 일부 요금제에는 필요에 따라 Azure 서비스에서 사용할 수 있는 월별 크레딧이 포함되어 있습니다.

이전 청구 기간이 끝날 때 최대 24시간 동안의 사용량이 현재 청구서에 표시될 수 있습니다. 또한 전 세계 고객용 청구 명세서에 나열된 요금은 예측 용도로만 사용합니다. 은행이 전환율에 대해 서로 다른 비용을 부과하기 때문입니다.

## <a name="pdf"></a> 청구서(.pdf) 이해
청구서는 청구된 요금에 대한 요약을 제공합니다. [Azure Portal](https://portal.azure.com)에서 Portable Document Format(.pdf)으로 다운로드할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용 현황 데이터를 받는 방법](billing-download-azure-invoice-daily-usage-date.md)을 참조하세요. 

다음 섹션에서는 청구서에 표시되는 대부분의 용어와 각 용어에 대한 설명을 제공합니다.

### <a name="account-information"></a>계정 정보
계정 정보 섹션에는 사용 현황 및 프로필에 대한 정보가 표시됩니다.

![머리글](./media/billing-understand-your-bill/Header.png)

| 용어 | 설명 |
| --- | --- |
| 청구서 번호 |추적을 위한 고유한 청구서 식별자입니다. |
| 대금 청구 주기 |이 청구서에 해당되는 날짜 범위 |
| 청구서 날짜 |청구서가 생성된 날짜 |
| 결제 방법 |계정에 사용되는 지불 유형(청구서 또는 신용 카드). 신용 카드를 업데이트해야 하는 경우 [Azure 구독 지불에 사용하는 신용 카드를 변경하는 방법](billing-how-to-change-credit-card.md)을 참조하세요. 비즈니스를 대표하는 경우 수표, 속달 수표(overnight checks) 또는 전신 송금과 같은 청구서 지불로 Azure 구독을 결제할 수 있습니다. [Azure 청구 - 청구하는 방법](https://azure.microsoft.com/pricing/invoicing/)을 참조하세요. |
| 청구지 |Microsoft Azure 지불 주소입니다. [Azure 구독 지불에 사용하는 신용 카드 변경](billing-how-to-change-credit-card.md)을 참조하여 청구 정보를 업데이트하는 방법을 알아보세요. |
| 구독 제품 |구매한 구독 제품의 유형(종량제, BizSpark Plus, Azure Pass 등). 종량제를 다른 구독 제품으로 변경하려면 [Azure 구독을 다른 제품으로 전환](billing-how-to-switch-azure-offer.md)을 참조하세요. |
| 계정 소유자 메일 |Microsoft Azure 계정이 등록된 계정 전자 메일 주소입니다. 전자 메일 주소를 변경하려면 [연락처 메일, 주소 및 전화 번호와 같은 Azure 계정의 프로필 정보를 변경하는 방법](billing-how-to-change-azure-account-profile.md)을 참조하세요. |

### <a name="understand-the-invoice-summary"></a>청구서 요약 이해
청구서의 **청구서 요약** 섹션은 마지막 청구 이후의 트랜잭션 및 현재 사용 요금을 요약합니다.

![청구서 요약](./media/billing-understand-your-bill/InvoiceSummary.png)

청구서의 이전 잔액, 지불 및 미지불 잔액 섹션에는 마지막 청구 이후의 트랜잭션이 요약됩니다.

| 용어 | 설명 |
| --- | --- |
| 이전 잔액 |마지막 청구에서 지불할 총 금액 |
| 지불 |마지막 청구에 적용되는 총 지불액 |
| 미결제 잔액(이전 청구 주기) |마지막 청구 이후 계정에 적용되는 모든 청구 조정액(크레딧 또는 잔액) |

### <a name="understand-the-current-charges"></a>현재 요금 이해
청구서의 현재 요금 섹션에는 월별 요금에 대한 세부 정보가 표시됩니다. 

| 용어 | 설명 |
| --- | --- |
| 사용 요금 |사용 요금은 구독에 대한 총 월별 요금입니다. 지난 달의 사용 요금이 청구됩니다. |
| 할인 |현재 청구서에 적용되는 서비스 할인입니다. |
| 조정 |현재 청구서에 적용된 기타 크레딧 또는 미불 요금입니다. 예를 들어 Visual Studio Enterprise with MSDN 제품을 사용하는 경우 월별 크레딧이 표시됩니다. 구독을 취소하면 구독 제품을 통해 얻을 수 있는 월별 크레딧을 초과하는 월별 사용량 요금이 표시됩니다. 청구 기간은 현재 청구 시작부터 구독 취소 날짜까지입니다. |


### <a name="sold-to-and-payment-instructions"></a>구매처 및 지불 관련 지침
다음 표에서는 청구서에 표시되는 구매처 및 지불 관련 지침에 대해 설명합니다.

| 용어 | 설명 |
| --- | --- |
| 구매처 | 계정에 있는 프로필 주소입니다. 이 주소를 변경하려면 [연락처 메일, 주소 및 전화 번호와 같은 Azure 계정의 프로필 정보를 변경하는 방법](billing-how-to-change-azure-account-profile.md)을 참조하세요. |
| 지불 관련 지침 | 청구서로 지불하는 경우 다음 지침에 따라 수표, 전신 송금 또는 심야 수표를 보낼 위치를 확인하세요. 자세한 내용은 [Azure 청구 - 청구하는 방법](https://azure.microsoft.com/pricing/invoicing/)을 참조하세요. |

## <a name="csv"></a> 세부 사용 요금 이해(.csv)
사용 현황 파일에는 현재 청구 기간 내에 사용된 각 리소스의 양이 표시됩니다. 스프레드시트 응용 프로그램에서 열 수 있는 쉼표로 구분된 값(.csv) 파일 형식으로 제공됩니다. 사용할 수 있는 두 가지 버전이 표시되면 버전 2를 다운로드합니다. 최신 파일 형식입니다. 자세한 내용은 [Azure 청구서 및 일간 사용 현황 데이터를 받는 방법](billing-download-azure-invoice-daily-usage-date.md)을 참조하세요.

사용 요금은 구독에 대한 총 **월별** 요금에서 크레딧 또는 할인을 뺀 금액입니다. 지난 달의 사용 요금이 청구됩니다.  

다음 섹션에서는 자세한 사용 현황 파일 버전 2에 표시된 용어에 대해 설명합니다.

### <a name="statement"></a>문 
파일의 위쪽 섹션에는 전월 청구 주기 동안 사용된 서비스가 표시됩니다. 다음 표에는 이 섹션에 표시되는 용어와 설명이 나와 있습니다.

| 용어 | 설명 |
| --- | --- |
|청구 기간 |리소스 또는 서비스가 사용된 청구 기간입니다. |
|측정기 범주 |이 사용 현황이 속한 최상위 서비스를 식별합니다. |
|측정기 하위 범주 |Azure 서비스의 유형을 정의하며 요율에 영향을 줄 수 있습니다. |
|측정기 이름 |사용 중인 리소스에 대한 측정 단위를 식별합니다. |
|측정기 영역 |데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
|SKU |각 Azure 리소스에 대한 고유한 시스템 식별자를 식별합니다. |
|단위 |서비스 요금이 청구되는 단위를 식별합니다. GB, 시간, 10,000초 등을 예로 들 수 있습니다. |
|소비된 수량 |청구 기간 동안 사용된 리소스의 양입니다. |
|표함된 수량 |현재 청구 기간 동안 무료로 제공되는 리소스의 양입니다. |
|초과 수량 |소비된 수량과 포함된 수량 간의 차이를 나타냅니다. 이 수량에 대해 요금이 청구됩니다. 제품에 포함된 양이 없는 종량제 제품의 경우 이 합계는 사용량과 동일합니다. |
|약정 기간 내 |6개월 또는 12개월 제품과 관련된 약정 금액에서 공제된 리소스 요금을 나타냅니다. 시간순으로 공제(감소)되는 리소스 요금입니다. |
|통화 |현재 청구 기간에 사용되는 통화입니다. |
|초과분 |6개월 또는 12개월 제품과 관련된 약정 금액을 초과하는 리소스 요금을 나타냅니다. |
|약정 요율 |6개월 또는 12개월 제품과 관련된 총 약정 금액을 기반으로 하는 약정 요율을 나타냅니다. |
|비용 |청구 가능 단위당 청구되는 요율입니다. |
|값 |평균 수량 열과 요율 열을 곱한 결과를 보여 줍니다. 소비된 수량이 포함된 수량을 초과하지 않으면 이 열에 청구되는 비용이 없습니다. |

### <a name="daily-usage"></a>일일 사용량 

파일의 일일 사용량 섹션에는 청구 요금에 영향을 주는 사용 현황 세부 정보가 표시됩니다. 다음 표에는 이 섹션에 표시되는 용어와 설명이 나와 있습니다. 

| 용어| 설명 |
| --- | --- |
|사용 날짜 |리소스를 사용한 날짜입니다. |
|측정기 범주 |이 사용 현황이 속한 최상위 서비스를 식별합니다. |
|측정기 ID |가격 결제에 사용하는 대금 청구 측정기 식별자입니다. |
|측정기 하위 범주 |요율에 영향을 줄 수 있는 Azure 서비스 유형을 정의합니다. |
|측정기 이름 |사용 중인 리소스에 대한 측정 단위를 식별합니다. |
|측정기 영역|데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
|단위 |서비스 요금이 청구되는 단위를 식별합니다. GB, 시간, 10,000초 등을 예로 들 수 있습니다. |
|소비된 수량 |해당 날짜에 사용된 리소스의 양입니다. |
|리소스 위치 |리소스가 실행되고 있는 데이터 센터를 식별합니다. |
|사용되는 서비스 |사용한 Azure 플랫폼 서비스입니다. |
|리소스 그룹 |배포된 리소스가 실행되는 리소스 그룹입니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요. |
|인스턴스 ID |리소스에 대한 식별자입니다. 식별자를 만들 때 리소스에 대해 지정한 이름을 포함합니다. 리소스의 이름 또는 정규화된 리소스 ID입니다. 자세한 내용은 [Azure Resource Manager API](/rest/api/resources/resources)를 참조하세요. |
|태그들 |리소스에 할당하는 태그입니다. 태그를 사용하여 청구 레코드를 그룹화합니다. 예를 들어 리소스를 사용하는 부서는 비용을 배분하는데 태그를 사용할 수 있습니다. 태그를 내보내도록 지원하는 서비스에는 [Azure Resource Manager API](/rest/api/resources/resources)를 사용하여 프로비전된 가상 컴퓨터, 저장소 및 네트워킹 서비스가 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)을 참조하세요. |
|추가 정보 |서비스 특정 메타데이터입니다. 예를 들어 가상 컴퓨터용 이미지 형식입니다. |
|서비스 정보 1 |구독에 대해 서비스가 속한 프로젝트 이름입니다. |
|서비스 정보 2 |선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. |

## <a name="tips-for-cost-management"></a>비용 관리 팁
- [가격 계산기](https://azure.microsoft.com/pricing/calculator/), [총 소유 비용 계산기](https://aka.ms/azure-tco-calculator)를 사용하여 서비스를 추가할 때 비용 예측
- [청구 경고 설정](billing-set-up-alerts.md)
- [Azure Portal에서 정기적으로 사용량 및 비용 검토](billing-getting-started.md#costs)

자세한 내용은 [Azure 청구 및 비용 관리 시작](billing-getting-started.md)을 참조하세요.

## <a name="how-do-i-make-a-payment"></a>지불하려면 어떻게 해야 하나요?
결제 방법으로 신용 카드 또는 직불 카드를 설정한 경우 자동으로 결제됩니다. 신용 카드 명세서에서 품목명은 **MSFT Azure**입니다.

[결제를 청구서로 보내려면](https://azure.microsoft.com/pricing/invoicing/) 청구서 아래쪽에 나열된 위치로 결제 금액을 보냅니다. 자세한 도움말은 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>신용 카드 지불 상태를 어떻게 확인하나요?
결제 상태를 요청하는 [지원 티켓을 만듭니다](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>마켓플레이스 주문 또는 외부 서비스 요금은 어떤가요?
외부 서비스는 마켓플레이스 주문으로 불렸습니다. 외부 서비스는 독립 서비스 공급업체에 의해 제공되지만 Azure 내에서 통합됩니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)를 참조하세요.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요. 
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
 




