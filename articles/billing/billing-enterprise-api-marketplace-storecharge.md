---
title: "Azure 청구 엔터프라이즈 API - Marketplace 요금 | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9f084dce3f01466aaa0e4c32d339c925d9faccd3
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---marketplace-charges-preview"></a>기업 고객을 위한 보고 API - Marketplace 요금(미리 보기)

Marketplace 저장소 요금 API는 지정된 청구 기간 또는 시작 날짜 및 종료 날짜(1회 요금은 포함되지 않음)에 대한 일별 사용량 기반 Marketplace 요금 분석 결과를 반환합니다.

##<a name="request"></a>요청 
추가해야 할 공통 헤더 속성은 [여기](billing-enterprise-api.md)에 지정되어 있습니다. 청구 기간을 지정하지 않으면 현재 청구 기간에 대한 데이터가 반환됩니다. 사용자 지정 시간 범위는 yyyy-MM-dd 형식의 시작 날짜 및 종료 날짜 매개 변수로 지정할 수 있으며, 지원되는 최대 시간 범위는 36개월입니다.  

|메서드 | 요청 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>응답
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**응답 속성 정의**

|속성 이름| 형식| 설명
|-|-|-|
|id|string|Marketplace 요금 항목에 대한 고유 ID|
|subscriptionGuid|Guid|구독 Guid|
|subscriptionName|string|구독 이름|
|meterId|string|내보내는 측정기의 ID|
|usageStartDate|DateTime|사용량 기록의 시작 시간|
|usageEndDate|DateTime|사용량 기록의 종료 시간|
|offerName|string|제품 이름|
|resourceGroup|string|리소스 그룹|
|instanceId|string|인스턴스 ID|
|additionalInfo|string|추가 정보 JSON 문자열|
|tags|string|태그 JSON 문자열|
|orderNumber|string|주문 번호|
|unitOfMeasure|string|측정기의 측정 단위|
|costCenter|string|비용 센터|
|accountId|int|계정 ID|
|accountName|string |계정 이름|
|accountOwnerId|string|계정 소유자 ID|
|departmentId|int|부서 ID|
|departmentName|string|부서 이름|
|publisherName|string|게시자 이름|
|planName|string|계획 이름|
|consumedQuantity|decimal|이 기간 동안의 사용량|
|resourceRate|decimal|측정기의 단가|
|extendedCost|decimal|사용량 및 확장 비용에 따른 예상 요금|
<br/>
## <a name="see-also"></a>참고 항목
* [청구 기간 API](billing-enterprise-api-billing-periods.md)

* [사용량 세부 정보 API](billing-enterprise-api-usage-detail.md) 

* [잔액 및 요약 API](billing-enterprise-api-balance-summary.md)

* [가격표 API](billing-enterprise-api-pricesheet.md)
