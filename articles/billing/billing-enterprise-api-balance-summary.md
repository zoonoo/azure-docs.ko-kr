---
title: "Azure 청구 엔터프라이즈 API - 잔액 및 요약 | Microsoft Docs"
description: "Azure 리소스 소비 및 추세에 대한 통찰력을 제공하는 데 사용되는 Azure 청구 사용량 및 RateCard API에 대한 자세한 정보를 제공합니다."
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
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: ko-kr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>기업 고객을 위한 보고 API - 잔액 및 요약

잔액 및 요약 API는 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과 요금에 대한 월별 정보 요약을 제공합니다.


##<a name="request"></a>요청 
추가해야 할 공통 헤더 속성은 [여기](billing-enterprise-api.md)에 지정되어 있습니다. 청구 기간을 지정하지 않으면 현재 청구 기간에 대한 데이터가 반환됩니다.

|메서드 | 요청 URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> API의 미리 보기 버전을 사용하려면 위 URL에서 v2를 v1로 바꿉니다.
>

## <a name="response"></a>응답

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**응답 속성 정의**

|속성 이름| 형식| 설명
|-|-|-|
|id|string|특정 청구 기간 및 등록에 대한 고유 ID|
|billingPeriodId|string |청구 기간 ID|
|currencyCode|string |통화 코드|
|beginningBalance|decimal| 청구 기간의 기초 잔액|
|endingBalance|decimal| 청구 기간의 최종 잔액(영업일 기준으로 매일 업데이트됨)|
|newPurchases|decimal| 총 신규 구매 금액|
|adjustments|decimal| 총 조정 금액|
|utilized|decimal| 총 약정 사용량|
|serviceOverage|decimal| Azure 서비스 초과 사용량|
|chargesBilledSeparately|decimal| 별도 청구 사용량|
|totalOverage|decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|decimal| Azure 서비스 약정 사용량 + 총 초과 사용량|
|azureMarketplaceServiceCharges|decimal| Azure Marketplace에 대한 총 요금|
|newPurchasesDetails|이름 값 쌍의 JSON 문자열 배열|신규 구매 목록|
|adjustmentDetails|이름 값 쌍의 JSON 문자열 배열|조정 목록(프로모션 공제, SIE 공제 등) |


<br/>
## <a name="see-also"></a>참고 항목

* [청구 기간 API](billing-enterprise-api-billing-periods.md)

* [사용량 세부 정보 API](billing-enterprise-api-usage-detail.md) 

* [Marketplace 저장소 요금 API](billing-enterprise-api-marketplace-storecharge.md) 

* [가격표 API](billing-enterprise-api-pricesheet.md)
