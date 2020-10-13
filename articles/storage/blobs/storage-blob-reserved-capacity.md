---
title: 예약된 용량으로 Blob 스토리지에 대한 비용 최적화
titleSuffix: Azure Storage
description: 블록 blob 및 Azure Data Lake Storage Gen2 리소스에 대 한 비용을 절감 하기 위해 예약 된 Azure Storage 용량을 구매 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cf96906b0dab9a94febe83468f813c7cae0675b0
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874818"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>예약된 용량으로 Blob 스토리지에 대한 비용 최적화

Azure Storage reserved capacity를 사용 하 여 blob 데이터에 대 한 저장소 비용을 절감할 수 있습니다. Azure Storage reserved capacity는 1 년 또는 3 년간의 예약을 커밋할 때 블록 blob에 대 한 용량과 standard Storage 계정에 있는 Azure Data Lake Storage Gen2 데이터에 대 한 할인을 제공 합니다. 예약은 예약 기간에 고정 된 크기의 저장소 용량을 제공 합니다.

예약 된 용량 Azure Storage 블록 blob 및 Azure Data Lake Storage Gen2 데이터에 대 한 용량 비용을 크게 줄일 수 있습니다. 비용은 예약 기간, 예약 하도록 선택한 총 용량, 액세스 계층 및 저장소 계정에 대해 선택한 중복성 유형에 따라 달라 집니다. 예약 된 용량은 청구 할인을 제공 하며 Azure Storage 리소스의 상태에 영향을 주지 않습니다.

Azure Storage 예약 가격 책정에 대한 자세한 내용은 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 및 [Azure Data Lake Storage Gen 2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/)을 참조하세요.

## <a name="reservation-terms-for-azure-storage"></a>Azure Storage에 대 한 예약 용어

다음 섹션에서는 Azure Storage 예약의 조건에 대해 설명 합니다.

### <a name="reservation-capacity"></a>예약 용량

1 년 또는 3 년 기간 동안 매월 100 TiB 및 1 개의 PiB 단위로 예약 된 용량 Azure Storage 구매할 수 있습니다.

### <a name="reservation-scope"></a>예약 범위

단일 구독 또는 여러 구독 (공유 범위)에 대해 예약 된 Azure Storage 용량을 사용할 수 있습니다. 단일 구독으로 범위가 지정 된 경우 예약 할인은 선택한 구독에만 적용 됩니다. 여러 구독으로 범위가 지정 된 경우 고객의 청구 컨텍스트 내에서 해당 구독 간에 예약 할인이 공유 됩니다.

Azure Storage 예약 된 용량을 구입 하는 경우 블록 blob 및 Azure Data Lake Storage Gen2 데이터에 대 한 예약을 사용할 수 있습니다. 예약은 구매한 범위 내 사용량에 적용 되며, 구독 내의 특정 저장소 계정, 컨테이너 또는 개체로 제한 될 수 없습니다.

Azure Storage 예약은 구독 또는 공유 리소스 그룹에 저장 된 데이터의 양만 포함 합니다. 초기 삭제, 작업, 대역폭 및 데이터 전송 요금은 예약에 포함 되지 않습니다. 예약을 구매 하는 즉시, 예약 특성과 일치 하는 용량 요금은 종 량 제 요금이 아닌 할인 요금으로 청구 됩니다. Azure 예약에 대 한 자세한 내용은 [Azure Reservations?](/azure/billing/billing-save-compute-costs-reservations)을 참조 하세요.

### <a name="supported-account-types-tiers-and-redundancy-options"></a>지원 되는 계정 유형, 계층 및 중복성 옵션

Azure Storage reserved capacity는 범용 v2 (GPv2) 및 Blob storage 계정을 포함 하 여 표준 저장소 계정의 리소스에 사용할 수 있습니다.

모든 액세스 계층 (핫, 쿨 및 보관)은 예약에 대해 지원 됩니다. 액세스 계층에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)을 참조 하세요.

모든 유형의 중복성은 예약에 대해 지원 됩니다. 중복성 옵션에 대 한 자세한 내용은 [Azure Storage 중복성](../common/storage-redundancy.md)을 참조 하세요.

> [!NOTE]
> Azure Storage 예약 된 용량은 premium storage 계정, 범용 v1 (GPv1) 저장소 계정, Azure Data Lake Storage Gen1, 페이지 blob, Azure Queue storage, Azure Table storage 또는 Azure Files에 사용할 수 없습니다.  

### <a name="security-requirements-for-purchase"></a>구매를 위한 보안 요구 사항

예약 된 용량을 구매 하려면:

- 종 량 제 요금은 하나 이상의 Enterprise 또는 개별 구독에 대 한 **소유자** 역할에 속해야 합니다.
- 엔터프라이즈 구독의 경우 EA 포털에서 **예약 된 인스턴스 추가** 를 사용 하도록 설정 해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 Azure Blob Storage 예약 된 용량을 구매할 수 있습니다.

## <a name="determine-required-capacity-before-purchase"></a>구매 하기 전에 필요한 용량 확인

Azure Storage 예약을 구매할 때 예약에 대 한 지역, 액세스 계층 및 중복성 옵션을 선택 해야 합니다. 예약은 해당 지역, 액세스 계층 및 중복성 수준에 저장 된 데이터에 대해서만 유효 합니다. 예를 들어 ZRS (영역 중복 저장소)를 사용 하 여 핫 계층에 대 한 미국 서 부에 데이터에 대 한 예약을 구매 한다고 가정 합니다. 미국 동부, 보관 계층의 데이터 또는 GRS (지역 중복 저장소)의 데이터에 대해 동일한 예약을 사용할 수 없습니다. 그러나 추가 요구 사항에 대 한 다른 예약을 구매할 수 있습니다.  

예약은 현재 100 TiB 또는 1 pib 블록에 대해 제공 되며, 1 PiB 블록에 대해 더 높은 할인이 제공 됩니다. Azure Portal에서 예약을 구매 하는 경우 Microsoft는 이전 사용량을 기준으로 권장 사항을 제공 하 여 구매할 예약을 결정할 수 있습니다.

## <a name="purchase-azure-storage-reserved-capacity"></a>Azure Storage 예약 된 용량 구입

[Azure Portal](https://portal.azure.com)를 통해 Azure Storage 예약 된 용량을 구매할 수 있습니다. 예약 요금은 사전 결제 또는 월별 결제로 처리할 수 있습니다. 월간 지불로 구매 하는 방법에 대 한 자세한 내용은 [앞으로 또는 매월 지불 하 여 Azure 예약 구매](/azure/billing/billing-monthly-payments-reservations)를 참조 하세요.

시나리오에 적합 한 예약 약관을 식별 하는 데 도움이 필요 하면 [Azure Storage reserved capacity 할인율 이해](../../cost-management-billing/reservations/understand-storage-charges.md)를 참조 하세요.

예약 된 용량을 구매 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [구매 예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 창으로 이동 합니다.  
1. 새 예약을 구입 하려면 **Azure Blob Storage** 을 선택 합니다.  
1. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

    ![예약 된 용량을 구매 하는 방법을 보여 주는 스크린샷](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |필드  |설명  |
   |---------|---------|
   |**범위**   |  예약과 관련 된 청구 혜택을 사용할 수 있는 구독 수를 나타냅니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다. <br/><br/> **공유**를 선택 하면 청구 컨텍스트 내의 모든 구독에서 Azure Storage 용량에 예약 할인이 적용 됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종 량 제 고객의 경우 공유 범위에는 계정 관리자가 만든 종 량 제 요금이 포함 된 모든 개별 구독이 포함 됩니다.  <br/><br/>  **단일 구독**을 선택 하면 선택한 구독의 Azure Storage 용량에 예약 할인이 적용 됩니다. <br/><br/> **단일 리소스 그룹**을 선택 하는 경우 예약 할인이 선택한 구독의 Azure Storage 용량과 해당 구독 내에서 선택한 리소스 그룹에 적용 됩니다. <br/><br/> 예약을 구매한 후 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure Storage 예약에 대해 지불 하는 데 사용 되는 구독입니다. 선택한 구독의 결제 방법을 사용 하 여 비용을 청구 합니다. 구독은 다음 형식 중 하나 여야 합니다. <br/><br/>  기업계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p): Enterprise 구독의 경우 요금 청구는 등록의 금액 약정 잔액에서 공제 되거나 초과분로 청구 됩니다. <br/><br/> 종 량 제 요금이 있는 개별 구독 (제품 번호: MS-MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0023P): 종 량 제 요금이 있는 개별 구독의 경우 요금 청구는 구독에 대 한 신용 카드나 청구서 지불 방법으로 청구 됩니다.    |
   | **지역** | 예약이 적용 되는 지역입니다. |
   | **액세스 계층** | 예약이 적용 되는 액세스 계층입니다. *핫*, *쿨*또는 *Archive*옵션을 포함 합니다. 액세스 계층에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)을 참조 하세요. |
   | **중복** | 예약에 대 한 중복성 옵션입니다. 옵션에는 *LRS*, *ZRS*, *GRS*, *GZRS*, *ra-GRS*및 *RA-GZRS*가 포함 됩니다. 중복성 옵션에 대 한 자세한 내용은 [Azure Storage 중복성](../common/storage-redundancy.md)을 참조 하세요. |
   | **청구 빈도** | 예약에 대 한 계정 청구 빈도를 나타냅니다. 옵션에는 *월별* 또는 *선행*이 포함 됩니다. |
   | **크기** | 예약할 용량의 양입니다. |
   |**기간**  | 1년 또는 3년입니다.   |

1. 예약에 대 한 매개 변수를 선택 하면 Azure Portal에 비용이 표시 됩니다. 또한 포털은 종 량 제 요금 청구에 대 한 할인율을 보여 줍니다.

1. **구매 예약** 창에서 예약의 총 비용을 검토 합니다. 예약의 이름을 제공할 수도 있습니다.

    ![예약을 구매 하는 방법을 보여 주는 스크린샷](media/storage-blob-reserved-capacity/purchase-reservations.png)

예약을 구매한 후에는 예약의 조건과 일치 하는 기존 Azure Storage 블록 blob 또는 Azure Data Lake Storage Gen2 리소스에 자동으로 적용 됩니다. Azure Storage 리소스를 아직 만들지 않은 경우 예약의 조건과 일치 하는 리소스를 만들 때마다 예약이 적용 됩니다. 두 경우 모두 성공적으로 구매한 후 예약 기간이 즉시 시작 됩니다.

## <a name="exchange-or-refund-a-reservation"></a>예약 교환 또는 환불

특정 제한 사항을 사용 하 여 예약을 교환 하거나 환불 할 수 있습니다. 이러한 제한 사항은 다음 섹션에 설명 되어 있습니다.

예약을 교환 하거나 환불 하려면 Azure Portal의 예약 세부 정보로 이동 합니다. **Exchange** 또는 **환불**을 선택 하 고 지침에 따라 지원 요청을 제출 합니다. 요청이 처리 되 면 Microsoft에서 요청 완료를 확인 하는 전자 메일을 보냅니다.

Azure Reservations 정책에 대 한 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대 한 환불](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)를 참조 하세요.

### <a name="exchange-a-reservation"></a>예약 교환

예약을 교환 하면 예약의 사용 되지 않는 부분을 기준으로 비례 있는 환불을 받을 수 있습니다. 그런 다음 새 Azure Storage 예약의 구매 가격에 환불을 적용할 수 있습니다.

만들 수 있는 교환의 수에는 제한이 없습니다. 또한 exchange와 관련 된 요금은 없습니다. 구매한 새 예약은 원래 예약에서 비례 하는 크레딧을 초과 하는 값 보다 크거나 같아야 합니다. Azure Storage 예약은 다른 Azure 서비스에 대 한 예약이 아닌 다른 Azure Storage 예약에 대해서만 교환할 수 있습니다.

### <a name="refund-a-reservation"></a>예약 상환

언제 든 지 Azure Storage 예약을 취소할 수 있습니다. 취소 하는 경우 예약의 남은 기간을 기준으로 비례를 계산 하 여 12%의 조기 종료 수수료를 계산 합니다. 연간 최대 환불은 $5만입니다.

예약을 취소 하면 즉시 예약이 종료 되 고 남은 월이 Microsoft로 돌아옵니다. 남은 비례 잔액을 제외 하 고 원래 구매 형태에 환불 됩니다.

## <a name="expiration-of-a-reservation"></a>예약 만료

예약이 만료 되 면 해당 예약에서 사용 하는 모든 Azure Storage 용량은 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신되지 않습니다.

예약이 만료 되기 30 일 전에 전자 메일 알림을 받고 만료 날짜가 다시 청구 됩니다. 예약이 제공 하는 비용 절감 효과를 계속 활용 하려면 만료 날짜 보다 나중에 갱신 합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 예약이란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Storage에 예약 할인이 적용되는 방식 이해](../../cost-management-billing/reservations/understand-storage-charges.md)
