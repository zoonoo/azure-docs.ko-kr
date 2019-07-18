---
title: Microsoft 고객 계약-Azure에 가격표의 용어 이해
description: 읽기 및 Microsoft 고객 계약에 대 한 사용량 및 청구서를 이해 하는 방법에 알아봅니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490656"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Microsoft 고객 계약 가격 시트에서 용어

이 문서는 Microsoft 고객 계약에 대 한 Azure 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

청구 프로필 소유자, 참가자, 판독기 또는 송장 관리자 라면 Azure portal에서 조직의 가격표를 다운로드할 수 있습니다. 참조 [뷰와 조직의 가격 책정 다운로드](billing-ea-pricing.md)합니다.

## <a name="terms-and-descriptions-in-your-price-sheet"></a>사용 약관에 가격표의 설명

다음 섹션에서는 Microsoft 고객 계약 가격 시트에 표시 된 중요 한 용어를 설명 합니다.

| **필드 이름**   | **설명**   |
| --- | --- |
| billingAccountId  | 대금 청구 계정에 대 한 고유 식별자입니다.   |
| billingAccountName  | 청구 계정의 이름입니다.  |
| billingProfileId  | 청구 프로필에 대 한 고유 식별자입니다.   |
| billingProfileName  | 청구서를 수신 하도록 설정 하는 청구 프로필의 이름입니다. 가격표의 가격은이 청구 프로필을 사용 하 여 연결 합니다. |
| productOrderName  | 구매한 제품 계획의 이름입니다. |
| serviceFamily  | Azure 서비스의 형식입니다. 예: 계산, 분석, 보안 |
| Product  | 요금이 발생 하는 제품의 이름입니다. 예: 기본 SQL DB 및 표준 SQL DB  |
| productId  | 해당 미터 되는 제품에 대 한 고유 식별자입니다. |
| unitOfMeasure  | 서비스에 대 한 청구에 대 한 측정 단위를 식별합니다. 예를 들어, 계산 서비스는 시간당 요금이 청구 됩니다. |
| meterId  | 측정기에 대 한 고유 식별자입니다. |
| meterName  | 측정기의 이름입니다. 측정기의 Azure 서비스를 배포할 수 있는 리소스를 나타냅니다. |
| meterCategory  | 측정기에 대 한 분류 범주 이름입니다. 예를 들어 _Cloud services_하십시오 _네트워킹_등입니다. |
| meterType  |  미터 형식의 이름입니다. |
| meterSubCategory  | 미터 하위 분류 범주의 이름입니다.  |
| meterRegion  | 서비스에 대 한 미터를 사용할 수 있는 영역의 이름입니다. 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다.    |
| tierId  | 해당 하는 경우 가격 책정 계층을 식별 합니다. 이 설정에 대 한 계층화 된 가격 가격은 다를 때 기준에 사용 된 단위 수에 tierMinimumUnits와 함께에서 작동 합니다.    |
| tierMinimumUnits  | 가격은 정의 된 계층 범위 하한값을 정의 합니다. 예를 들어 범위는 0-100, tierMinimumUnits 0이 됩니다.  |
| effectiveStartDate  | 시작 가격에 적용 될 때 날짜입니다. |
| effectiveEndDate  | 효과적인 가격 종료 날짜입니다. |
| unitPrice  | (없습니다 효과적인 혼합된 가격) 청구 시 단위당 가격 미터 및 제품 순서 이름으로 특정 합니다.  참고: 단가 다릅니다 사용량 세부 정보에 효과적인 가격 차등 가격 계층 전반에 걸쳐 있는 서비스의 경우 다운로드 합니다.  다중 계층 가격으로 서비스의 경우 효과적인 가격 계층 전반에 걸쳐 혼합 비율이 및 계층 관련 단위 가격이 표시 되지 않습니다. 혼합된 가격 또는 실효 가격에는 소비 된 수량에 걸쳐 여러 계층 (각 계층에는 특정 단위 가격)에 대 한 순 가격입니다. |
| basePrice  | 때 시장 가격이 고객 서명 하거나 로그온 한 후에 경우 시 시장 가격 서비스 측정기를 시작 합니다.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [조직의 가격 책정 보기 및 다운로드](billing-ea-pricing.md)
