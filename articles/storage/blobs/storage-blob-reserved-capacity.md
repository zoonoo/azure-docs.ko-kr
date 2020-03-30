---
title: 예약된 용량으로 Blob 저장소비용 최적화 - Azure 저장소
description: 블록 Blob 및 Azure Data Lake Storage Gen2 리소스에 대한 비용을 절감하기 위해 Azure Storage 예약 용량을 구입하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351022"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>예약된 용량으로 Blob 스토리지에 대한 비용 최적화

Azure Storage 예약 용량을 사용하여 Blob 데이터의 저장소 비용을 절감할 수 있습니다. Azure Storage 예약 용량은 1년 또는 3년 동안 예약을 할 때 표준 저장소 계정의 블록 Blob 및 Azure Data Lake Storage Gen2 데이터에 대한 용량을 할인으로 제공합니다. 예약은 예약 기간 동안 고정된 용량의 저장 용량을 제공합니다.

Azure Storage 예약 용량은 블록 Blob 및 Azure Data Lake Storage Gen2 데이터의 용량 비용을 크게 줄일 수 있습니다. 비용 절감은 예약 기간, 예약하기로 선택한 총 용량, 저장소 계정에 대해 선택한 액세스 계층 및 중복 유형에 따라 달라집니다. 예약된 용량은 청구 할인을 제공하며 Azure Storage 리소스의 상태에 영향을 주지 않습니다.

Azure Storage 예약 가격 책정에 대한 자세한 내용은 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 및 [Azure Data Lake Storage Gen 2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/)을 참조하세요.

## <a name="reservation-terms-for-azure-storage"></a>Azure 저장소에 대한 예약 조건

다음 섹션에서는 Azure Storage 예약의 조건을 설명합니다.

### <a name="reservation-capacity"></a>예약 용량

Azure Storage 예약 용량을 1년 또는 3년 기간 동안 매월 1TB 및 1PB 단위로 구입할 수 있습니다.

### <a name="reservation-scope"></a>예약 범위

Azure Storage 예약 용량은 단일 구독 또는 여러 구독(공유 범위)에 사용할 수 있습니다. 단일 구독으로 범위가 설정되면 예약 할인은 선택한 구독에만 적용됩니다. 여러 구독으로 범위가 설정되면 예약 할인은 고객의 청구 컨텍스트 내에서 해당 구독간에 공유됩니다.

Azure Storage 예약 용량을 구입하면 블록 Blob 및 Azure Data Lake Storage Gen2 데이터에 대해 예약을 사용할 수 있습니다. 예약은 구매한 범위 내에서 사용에 적용되며 구독 내의 특정 저장소 계정, 컨테이너 또는 개체로 제한될 수 없습니다. 예약을 여러 구독으로 분할할 수 없습니다.

Azure Storage 예약은 구독 또는 공유 리소스 그룹에 저장된 데이터의 양만 포함합니다. 조기 삭제, 운영, 대역폭 및 데이터 전송 요금은 예약에 포함되지 않습니다. 예약을 구매하는 즉시 예약 속성과 일치하는 용량 요금은 종량제 요금이 아닌 할인 요금으로 부과됩니다. Azure 예약에 대한 자세한 내용은 [Azure 예약이란 무엇입니까?](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>지원되는 계정 유형, 계층 및 중복 옵션

Azure 저장소 예약 용량은 범용 v2(GPv2) 및 Blob 저장소 계정을 비롯한 표준 저장소 계정의 리소스에 사용할 수 있습니다.

모든 액세스 계층(핫, 쿨 및 아카이브)은 예약에 대해 지원됩니다. 액세스 계층에 대한 자세한 내용은 [Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층을](storage-blob-storage-tiers.md)참조하십시오.

모든 유형의 중복성은 예약에 대해 지원됩니다. 중복 옵션에 대한 자세한 내용은 [Azure 저장소 중복을](../common/storage-redundancy.md)참조하십시오.

> [!NOTE]
> 프리미엄 저장소 계정, 범용 v1(GPv1) 저장소 계정, Azure Data Lake Storage Gen1, 페이지 Blob, Azure 큐 저장소, Azure 테이블 저장소 또는 Azure 파일에는 Azure 저장소 예약 용량을 사용할 수 없습니다.  

### <a name="security-requirements-for-purchase"></a>구매를 위한 보안 요구 사항

예약된 용량을 구입하려면 다음을 수행하십시오.

- 종량제 요금으로 하나 이상의 엔터프라이즈 또는 개별 구독의 **소유자** 역할에 있어야 합니다.
- 엔터프라이즈 구독의 경우 **예약 인스턴스 추가는** EA 포털에서 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 Azure Blob Storage 예약 용량을 구입할 수 있습니다.

## <a name="determine-required-capacity-before-purchase"></a>구매 하기 전에 필요한 용량 확인

Azure Storage 예약을 구매할 때 예약에 대한 지역, 액세스 계층 및 중복 옵션을 선택해야 합니다. 예약은 해당 리전, 액세스 계층 및 중복 수준에 저장된 데이터에 대해서만 유효합니다. 예를 들어 ZRS(영역 중복 저장소)를 사용하여 핫 계층에 대해 미국 서부의 데이터에 대한 예약을 구입한다고 가정합니다. 미국 동부의 데이터, 아카이브 계층의 데이터 또는 지리적 중복 저장소(GRS)의 데이터에 대해 동일한 예약을 사용할 수 없습니다. 그러나 추가 필요에 따라 다른 예약을 구입할 수 있습니다.  

현재 100TB 또는 1PB 블록에 대한 예약이 가능하며, 1PB 블록에 대한 할인혜택이 더 높습니다. Azure 포털에서 예약을 구매할 때 Microsoft는 이전 사용량에 따라 권장 사항을 제공하여 구매할 예약을 결정할 수 있습니다.

## <a name="purchase-azure-storage-reserved-capacity"></a>Azure 저장소 예약 용량 구매

Azure [포털을](https://portal.azure.com)통해 Azure 저장소 예약 용량을 구입할 수 있습니다. 예약 요금은 사전 결제 또는 월별 결제로 처리할 수 있습니다. 월별 결제로 구매하는 것에 대한 자세한 내용은 [사전 또는 월별 결제가 있는 Azure 예약 구매를](/azure/billing/billing-monthly-payments-reservations)참조하세요.

시나리오에 적합한 예약 조건을 식별하는 데 도움이 되면 [Azure Storage 예약 용량 할인 이해하기를](../../cost-management-billing/reservations/understand-storage-charges.md)참조하십시오.

예약된 용량을 구입하려면 다음 단계를 따르세요.

1. Azure 포털에서 [구매 예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 창으로 이동합니다.  
1. 새 예약을 구입하려면 **Azure Blob 저장소를** 선택합니다.  
1. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

    ![예약 된 용량을 구입하는 방법을 보여주는 스크린 샷](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |필드  |설명  |
   |---------|---------|
   |**Scope**   |  예약과 연결된 청구 혜택을 사용할 수 있는 구독 수를 나타냅니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다. <br/><br/> **공유를**선택하면 청구 컨텍스트 내의 모든 구독에서 예약 할인이 Azure Storage 용량에 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 경우 공유 범위에는 계정 관리자가 만든 종량제 요금이 포함된 모든 개별 구독이 포함됩니다.  <br/><br/>  **단일 구독을**선택하면 선택한 구독의 Azure Storage 용량에 예약 할인이 적용됩니다. <br/><br/> **단일 리소스 그룹을**선택하면 선택한 구독의 Azure Storage 용량과 해당 구독 내의 선택한 리소스 그룹에 예약 할인이 적용됩니다. <br/><br/> 예약 구매 후 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure 저장소 예약에 대해 비용을 지불하는 데 사용되는 구독입니다. 선택한 구독의 결제 방법은 비용을 청구하는 데 사용됩니다. 구독은 다음 유형 중 하나여야 합니다. <br/><br/>  기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P): 엔터프라이즈 구독의 경우 등록의 통화 약정 잔액에서 요금이 차감되거나 초과로 청구됩니다. <br/><br/> 종량제 요금(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 구독: 종량제 요금이 있는 개별 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다.    |
   | **지역** | 예약이 적용되는 지역입니다. |
   | **액세스 계층** | 예약이 적용되는 액세스 계층입니다. 옵션에는 *핫,* *쿨*또는 *아카이브가*포함됩니다. 액세스 계층에 대한 자세한 내용은 [Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층을](storage-blob-storage-tiers.md)참조하십시오. |
   | **중복** | 예약에 대한 중복 옵션입니다. 옵션으로는 *LRS,* *ZRS,* *GRS*및 *RA-GZRS가*있습니다. 중복 옵션에 대한 자세한 내용은 [Azure 저장소 중복을](../common/storage-redundancy.md)참조하십시오. |
   | **청구 빈도** | 예약에 대한 계정 청구 빈도를 나타냅니다. 옵션에는 *월별* 또는 *선결제가*포함됩니다. |
   | **크기** | 예약이 적용되는 지역입니다. |
   |**용어**  | 1년 또는 3년입니다.   |

1. 예약에 대한 매개 변수를 선택하면 Azure 포털에 비용이 표시됩니다. 포털에는 종량제 청구에 대한 할인 비율도 표시됩니다.

1. 구매 **예약** 창에서 예약의 총 비용을 검토합니다. 예약 의 이름을 제공 할 수도 있습니다.

    ![예약 구매 방법을 보여주는 스크린샷](media/storage-blob-reserved-capacity/purchase-reservations.png)

예약을 구입하면 예약 조건과 일치하는 기존 Azure 저장소 블록 Blob 또는 Azure Data Lake Storage Gen2 리소스에 자동으로 적용됩니다. Azure Storage 리소스를 아직 만들지 않은 경우 예약 조건과 일치하는 리소스를 만들 때마다 예약이 적용됩니다. 두 경우 모두 예약 기간은 성공적인 구매 직후에 시작됩니다.

## <a name="exchange-or-refund-a-reservation"></a>예약 교환 또는 환불

특정 제한 사항에 따라 예약을 교환하거나 환불할 수 있습니다. 이러한 제한 사항은 다음 섹션에서 설명합니다.

예약을 교환하거나 환불하려면 Azure 포털의 예약 세부 정보로 이동합니다. **교환** 또는 **환불을**선택하고 지침에 따라 지원 요청을 제출합니다. 요청이 처리되면 Microsoft는 요청 완료를 확인하는 이메일을 보내드립니다.

Azure 예약 정책에 대한 자세한 내용은 [Azure 예약에 대한 셀프 서비스 교환 및 환불을](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)참조하십시오.

### <a name="exchange-a-reservation"></a>예약 교환

예약을 교환하면 예약의 미사용 부분에 따라 비례 배분된 환불을 받을 수 있습니다. 그런 다음 새 Azure Storage 예약의 구매 가격에 환불을 적용할 수 있습니다.

거래할 수 있는 교환 횟수에는 제한이 없습니다. 또한, 교환과 관련된 수수료는 없습니다. 구매한 새 예약은 원래 예약의 비례 배분 된 크레딧과 동일하거나 더 큰 가치가 있어야 합니다. Azure Storage 예약은 다른 Azure 저장소 예약에 대해서만 교환할 수 있으며 다른 Azure 서비스에 대한 예약은 교환할 수 없습니다.

### <a name="refund-a-reservation"></a>예약 환불

Azure 저장소 예약을 언제든지 취소할 수 있습니다. 취소시 예약의 남은 기간에 따라 12%의 조기 해지 수수료를 제외한 비례 배분된 환불을 받게 됩니다. 연간 최대 환불액은 $50,000입니다.

예약을 취소하는 즉시 예약이 종료되고 남은 달을 Microsoft로 반환합니다. 남은 비례 배분 잔액에서 수수료를 뺀 금액이 원래 구매 양식으로 환불됩니다.

## <a name="expiration-of-a-reservation"></a>예약 만료

예약이 만료되면 해당 예약에서 사용하는 모든 Azure Storage 용량은 종량제 요금으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

예약 만료 30일 전, 그리고 만료일에 다시 이메일 알림을 받게 됩니다. 예약이 제공하는 비용 절감 효과를 계속 활용하려면 만료 날짜까지 갱신하십시오.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://go.microsoft.com/fwlink/?linkid=2083458)

## <a name="next-steps"></a>다음 단계

- [Azure 예약이란 무엇입니까?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Storage에 예약 할인이 적용되는 방식 이해](../../cost-management-billing/reservations/understand-storage-charges.md)
