---
title: Azure VMware 솔루션 예약 인스턴스를 사용 하 여 비용 절감
description: Azure VMware 솔루션에 대 한 예약 인스턴스를 구매 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: b57e985068adabccecbbdb43dd11bcf6596bf422
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578953"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Azure VMware 솔루션을 사용 하 여 비용 절감

[Azure VMware 솔루션](introduction.md)의 예약 된 인스턴스로 커밋하면 비용을 절감할 수 있습니다.  예약 할인은 예약 범위 및 특성과 일치 하는 실행 중인 Azure VMware 솔루션 호스트에 자동으로 적용 됩니다. 예약 인스턴스 구매는 사용량의 계산 부분만 다루며 소프트웨어 라이선스 비용이 포함 됩니다. 

## <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약 된 인스턴스는 일부 예외와 함께 사용할 수 있습니다.

-   **클라우드** -예약은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) 페이지에 나열 된 지역 에서만 사용할 수 있습니다.

-   **할당량이 부족** 합니다. 단일/공유 구독으로 범위가 지정 된 예약에는 새 예약 인스턴스의 구독에서 사용할 수 있는 호스트 할당량이 있어야 합니다. [할당량 증가 요청을 만들어](enable-azure-vmware-solution.md) 이 문제를 해결할 수 있습니다.

-   **제품 자격** -Microsoft와 함께 [AZURE 기업계약 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 가 필요 합니다.

-   **용량 제한** -드문 경우 지만 azure는 지역에 용량이 부족 하 여 Azure VMware 솔루션 호스트 sku에 대 한 새 예약 구매를 제한 합니다.

## <a name="buy-a-reservation"></a>예약 구입

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 Azure VMware 솔루션 호스트 인스턴스의 예약 인스턴스를 구입할 수 있습니다.

예약에 대 한 요금을 지불 [하거나 월별 지불](../cost-management-billing/reservations/prepare-buy-reservation.md)을 수행할 수 있습니다.

이러한 요구 사항은 예약 된 전용 호스트 인스턴스를 구입 하는 데 적용 됩니다.

-   하나 이상의 EA 구독에 대 한 *소유자* 역할 또는 종 량 제 요금이 있는 구독 이어야 합니다.

-   EA 구독의 경우 [ea 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용 하도록 설정 해야 합니다. 사용 하지 않도록 설정 된 경우 구독을 사용 하도록 설정 하려면 EA 관리자 여야 합니다.

-   CSP (클라우드 솔루션 공급자) Azure 요금제에서 구독의 경우 파트너는 고객에 대 한 Azure Portal 예약 된 인스턴스를 구입 해야 합니다. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>EA 구독을 위한 예약 인스턴스 구매

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **모든 서비스** > **예약** 을 선택합니다.

3. **지금 구매** 를 선택 하 고 **Azure VMware 솔루션** 을 선택 합니다.

4. 필수 필드를 입력 합니다. Azure VMware 솔루션을 실행 하는 것과 일치 하는 선택한 특성은 예약 할인에 적합 합니다.  특성에는 SKU, 지역(해당 하는 경우) 및 범위가 포함됩니다. 예약 범위는 예약 절감액이 적용될 위치를 선택합니다.

   EA 계약이 있는 경우 **추가 옵션** 을 사용 하 여 인스턴스를 신속 하 게 추가할 수 있습니다. 다른 구독 유형에 대해서는이 옵션을 사용할 수 없습니다.

   | 필드        |  Description |
   | ------------ | ------------ |
   | 구독 | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업 계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p), Microsoft 고객 계약 또는 종 량 제 요금이 있는 개별 구독 (제품 번호: MS-MS-AZR-0017P-0003P 또는 MS-MS-AZR-0017P-0017P) 이어야 합니다. 요금은 약정 잔액에서 차감되거나(있는 경우) 초과분 요금으로 청구됩니다. 종 량 제 요금이 있는 구독의 경우 요금은 구독의 신용 카드나 청구서 지불 방법으로 청구 됩니다. |
   | Scope        | 예약 범위는 하나의 구독 또는 여러 구독 (공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우:<br><ul><li><b>단일 리소스 그룹 범위</b> -선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.</li><li><b>단일 구독 범위</b> -선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.</li><li><b>공유 범위</b> -청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. EA 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li></ul>       |
   | 지역       | 예약이 적용 되는 Azure 지역입니다.   |
   | 호스트 크기    | AV36    |
   | 용어         | 1년 또는 3년입니다.  |
   | 수량     | 예약 내에서 구입할 인스턴스 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 Azure VMware 솔루션 호스트의 수입니다.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>CSP 구독에 대해 예약 인스턴스 구입

고객을 위해 예약 된 인스턴스를 구입 하려는 Csp는 [파트너 센터 설명서](https://docs.microsoft.com/partner-center/azure-plan-manage)의 (aobo) 절차를 대신 하 여 **관리자** 를 사용 해야 합니다. 자세한 내용은 [(AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 비디오를 대신 하 여 관리를 참조 하세요.

1. [파트너 센터](https://partner.microsoft.com)에 로그인합니다.

2. **CSP** 를 선택 하 여 **고객** 영역에 액세스 합니다.

3. 고객 세부 정보를 확장 하 고 **Microsoft Azure 관리 포털** 를 선택 합니다. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft 파트너 센터 고객 영역" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Azure Portal에서 **모든 서비스**  >  **예약** 을 선택 합니다.

5. **지금 구매** 를 선택 하 고 **Azure VMware 솔루션** 을 선택 합니다.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure portal 예약" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. 필수 필드를 입력 합니다. Azure VMware 솔루션을 실행 하는 것과 일치 하는 선택한 특성은 예약 할인에 적합 합니다.  특성에는 SKU, 지역(해당 하는 경우) 및 범위가 포함됩니다. 예약 범위는 예약 절감액이 적용될 위치를 선택합니다.

   | 필드        |  Description |
   | ------------ | ------------ |
   | 구독 | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 적합 한 유형 이어야 합니다 .이 경우에는 CSP 구독입니다.|
   | Scope        | 예약 범위는 하나의 구독 또는 여러 구독 (공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우:<br><ul><li><b>단일 리소스 그룹 범위</b> -선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.</li><li><b>단일 구독 범위</b> -선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.</li><li><b>공유 범위</b> -청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. EA 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li></ul>       |
   | 지역       | 예약이 적용 되는 Azure 지역입니다.   |
   | 호스트 크기    | AV36    |
   | 용어         | 1년 또는 3년입니다.  |
   | 수량     | 예약 내에서 구입할 인스턴스 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 Azure VMware 솔루션 호스트의 수입니다.     |

고객에 대해 구매한 예약을 보는 방법에 대 한 자세한 내용은 [CSP (클라우드 솔루션 공급자)로 Azure 예약 보기](../cost-management-billing/reservations/how-to-view-csp-reservations.md) 문서를 참조 하세요.

## <a name="usage-data-and-reservation-usage"></a>사용 현황 데이터 및 예약 사용량

예약 할인을 가져오는 사용량의 유효 가격은 0입니다. 각 예약에 대해 예약 할인을 받은 Azure VMware 솔루션 인스턴스를 확인할 수 있습니다.

사용 현황 데이터에 예약 할인이 표시 되는 방법에 대 한 자세한 내용은 다음과 같습니다.

- EA 고객의 경우 [기업 등록에 대 한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 를 참조 하세요.
- 개별 구독의 경우 [종 량 제 구독에 대 한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md) 를 참조 하세요.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매 후 예약을 다음과 같이 변경할 수 있습니다.

-   예약 범위 업데이트

-   인스턴스 크기 유연성 (해당 하는 경우)

-   소유권

또한 예약을 더 작은 청크 또는 병합 예약으로 분할할 수 있습니다. 새 상업용 트랜잭션을 발생 시키거나 예약의 종료 날짜를 변경 하지 않습니다.

CSP 관리 예약에 대 한 자세한 내용은 [파트너 센터, Azure Portal 또는 api를 사용 하 여 고객에 게 Microsoft Azure 예약 판매](https://docs.microsoft.com/partner-center/azure-reservations)를 참조 하세요.



>[!NOTE]
>예약을 구매한 후에는 다음과 같은 유형의 변경을 직접 수행할 수 없습니다.
>
> - 기존 예약 영역
> - SKU
> - 수량
> - 기간
>
>그러나 변경을 수행 하려는 경우 예약을 *교환할* 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

Csp는 고객을 위해 구매한 특정 제한 사항과 함께 취소, 교환 또는 환불 예약을 수행할 수 있습니다. 자세한 내용은 [고객에 대 한 관리, 취소, 교환 또는 환불 Microsoft Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations-manage)을 참조 하세요.