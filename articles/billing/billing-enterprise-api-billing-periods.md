---
title: "Azure 청구 엔터프라이즈 API - 청구 기간 | Microsoft Docs"
description: "Azure 기업 고객이 사용량 데이터를 프로그래밍 방식으로 끌어올 수 있게 하는 보고 API에 대해 알아봅니다."
services: 
documentationcenter: 
author: cwatson-cat
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
ms.openlocfilehash: c6880b79189e0683387a7aafbd6fa4805b3b42ef
ms.contentlocale: ko-kr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---billing-periods"></a>기업 고객을 위한 보고 API - 청구 기간

청구 기간 API는 지정된 등록에 대한 사용량 데이터를 역방향 시간 순서로 표시한 청구 기간 목록을 반환합니다. 각 기간에는 4개의 데이터 집합(잔액 요약, 사용량 세부 정보, Marktplace 요금 및 가격표)에 대한 API 경로를 가리키는 속성이 포함되어 있습니다. 기간에 데이터가 없으면 해당 속성은 null입니다. 


##<a name="request"></a>요청 
추가해야 할 공통 헤더 속성은 [여기](billing-enterprise-api.md)에 지정되어 있습니다. 

|메서드 | 요청 URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingperiods|

> [!Note]
> API의 미리 보기 버전을 사용하려면 위 URL에서 v2를 v1로 바꿉니다.
>

## <a name="response"></a>응답
 
    
    
      [
            {
                "billingPeriodId": "201704",
                "billingStart": "2017-04-01T00:00:00Z",
                "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },          
            ....
      ]
    

**응답 속성 정의**

|속성 이름| 형식| 설명
|-|-|-|
|billingPeriodId| string| 특정 청구 기간을 나타내는 고유 ID|
|billingStart| datetime| 기간 시작 날짜는 나타내는 ISO 8601 문자열|
|billingEnd| datetime| 기간 종료 날짜는 나타내는 ISO 8601 문자열|
|balanceSummary| string| 이 기간의 잔액 요약 데이터에 라우팅하는 URL 경로|
|usageDetails| string| 이 기간의 사용량 세부 정보 데이터에 라우팅하는 URL 경로|
|marketplaceCharges| string| 이 기간의 Marketplace 요금 데이터에 라우팅하는 URL 경로|
|priceSheet| string| 이 기간의 가격표 데이터에 라우팅하는 URL 경로|

<br/>
## <a name="see-also"></a>참고 항목

* [잔액 및 요약 API](billing-enterprise-api-balance-summary.md)

* [사용량 세부 정보 API](billing-enterprise-api-usage-detail.md) 

* [Marketplace 저장소 요금 API](billing-enterprise-api-marketplace-storecharge.md) 

* [가격표 API](billing-enterprise-api-pricesheet.md)
