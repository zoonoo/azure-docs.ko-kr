---
title: "Azure 청구 엔터프라이즈 API - 사용량 세부 정보 | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>기업 고객을 위한 보고 API - 사용량 세부 정보(미리 보기)

사용량 세부 정보 API는 등록에 따른 사용량과 예상 요금 의 일별 분석 결과를 제공합니다. 이 결과에는 인스턴스, 측정기 및 부서에 대한 정보도 포함 됩니다. API는 청구 기간 또는 지정된 시작 날짜와 종료 날짜를 기준으로 쿼리할 수 있습니다. 
## <a name="consumption-apis"></a>사용량 API


##<a name="request"></a>요청 
추가해야 할 공통 헤더 속성은 [여기](billing-enterprise-api.md)에 지정되어 있습니다. 청구 기간을 지정하지 않으면 현재 청구 기간에 대한 데이터가 반환됩니다. 사용자 지정 시간 범위는 yyyy-MM-dd 형식의 시작 날짜 및 종료 날짜 매개 변수로 지정할 수 있으며, 지원되는 최대 시간 범위는 36개월입니다.  

|메서드 | 요청 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>응답

> 잠재적으로 많은 양의 데이터로 인해 결과 집합이 페이징됩니다. nextLink 속성(있는 경우)은 데이터의 다음 페이지에 대한 링크를 지정합니다. 링크가 비어 있으면 이 링크가 마지막 페이지임을 나타냅니다. 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**응답 속성 정의**

|속성 이름| 형식| 설명
|-|-|-|
|id| string| API 호출의 고유 ID |
|데이터| JSON 배열| 모든 인스턴스/측정기에 대한 일별 사용량 세부 정보의 배열|
|nextLink| string| 더 많은 데이터 페이지가 있으면 nextLink에서 다음 데이터 페이지를 반환하는 URL을 가리킵니다. |

## <a name="see-also"></a>참고 항목
* [청구 기간 API](billing-enterprise-api-billing-periods.md)

* [잔액 및 요약 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 저장소 요금 API](billing-enterprise-api-marketplace-storecharge.md) 

* [가격표 API](billing-enterprise-api-pricesheet.md)
