---
title: "Azure 청구 엔터프라이즈 API - 가격표 | Microsoft Docs"
description: "Azure 기업 고객이 사용량 데이터를 프로그래밍 방식으로 끌어올 수 있게 하는 보고 API에 대해 알아봅니다."
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: ko-kr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>기업 고객을 위한 보고 API - 가격표

가격표 API는 지정된 등록 및 청구 기간에 대한 각 측정기에 적용할 수 있는 가격을 제공합니다.

##<a name="request"></a>요청
추가해야 할 공통 헤더 속성은 [여기](billing-enterprise-api.md)에 지정되어 있습니다. 청구 기간을 지정하지 않으면 현재 청구 기간에 대한 데이터가 반환됩니다.

|메서드 | 요청 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|

> [!Note]
> API의 미리 보기 버전을 사용하려면 위 URL에서 v2를 v1로 바꿉니다.
>

## <a name="response"></a>응답

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                "billingPeriodId": "201704",
                "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
                "meterName": "A1 VM",
                "unitOfMeasure": "100 Hours",
                "includedQuantity": 0,
                "partNumber": "N7H-00015",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            {
                "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                "billingPeriodId": "201404",
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>미리 보기 API를 사용 중인 경우에는 meterId 필드가 제공되지 않습니다.
>

**응답 속성 정의**

|속성 이름| 형식| 설명
|-|-|-|
|id| string| 특정 가격표 항목을 나타내는 고유 ID(청구 기간별 측정기)|
|billingPeriodId| string| 특정 청구 기간을 나타내는 고유 ID|
|meterId| string| 측정기에 대한 식별자입니다. 사용 현황 meterId에 매핑할 수 있습니다.|
|meterName| string| 측정기 이름|
|unitOfMeasure| string| 서비스를 측정하기 위한 측정 단위|
|includedQuantity| decimal| 포함된 수량 |
|partNumber| string| 측정기와 연결된 부품 번호|
|unitPrice| decimal| 측정기의 단가|
|currencyCode| string| unitPrice의 통화 코드|
<br/>
## <a name="see-also"></a>참고 항목

* [청구 기간 API](billing-enterprise-api-billing-periods.md)

* [사용량 세부 정보 API](billing-enterprise-api-usage-detail.md)

* [잔액 및 요약 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 저장소 요금 API](billing-enterprise-api-marketplace-storecharge.md)

