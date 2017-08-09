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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>기업 고객을 위한 보고 API - 사용량 세부 정보

사용량 세부 정보 API는 등록에 따른 사용량과 예상 요금 의 일별 분석 결과를 제공합니다. 이 결과에는 인스턴스, 측정기 및 부서에 대한 정보도 포함 됩니다. API는 청구 기간 또는 지정된 시작 날짜와 종료 날짜를 기준으로 쿼리할 수 있습니다. 
## <a name="consumption-apis"></a>사용량 API


##<a name="request"></a>요청 
추가해야 할 공통 헤더 속성은 [여기](billing-enterprise-api.md)에 지정되어 있습니다. 청구 기간을 지정하지 않으면 현재 청구 기간에 대한 데이터가 반환됩니다. 사용자 지정 시간 범위는 yyyy-MM-dd 형식의 시작 날짜 및 종료 날짜 매개 변수로 지정할 수 있으며, 지원되는 최대 시간 범위는 36개월입니다.  

|메서드 | 요청 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> API의 미리 보기 버전을 사용하려면 위 URL에서 v2를 v1로 바꿉니다.
>

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
            "Cost": 0,
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
        ],
        "nextLink": "string"
    }

<br/>
**응답 속성 정의**

|속성 이름| 형식| 설명
|-|-|-|
|id| string| API 호출의 고유 ID |
|데이터| JSON 배열| 모든 인스턴스/측정기에 대한 일별 사용량 세부 정보의 배열|
|nextLink| string| 더 많은 데이터 페이지가 있으면 nextLink에서 다음 데이터 페이지를 반환하는 URL을 가리킵니다. |
|accountId| int| 사용되지 않는 필드입니다. 이전 버전과의 호환성을 위해 존재합니다. |
|productId| int| 사용되지 않는 필드입니다. 이전 버전과의 호환성을 위해 존재합니다. |
|resourceLocationId| int| 사용되지 않는 필드입니다. 이전 버전과의 호환성을 위해 존재합니다. |
|consumedServiceID| int| 사용되지 않는 필드입니다. 이전 버전과의 호환성을 위해 존재합니다. |
|departmentId| int| 사용되지 않는 필드입니다. 이전 버전과의 호환성을 위해 존재합니다. |
|accountOwnerEmail| string| 계정 소유자의 전자 메일 계정입니다. |
|accountName| string| 계정의 이름을 입력한 사용자입니다. |
|serviceAdministratorId| string| 서비스 관리자의 전자 메일 주소입니다. |
|subscriptionId| int| 사용되지 않는 필드입니다. 이전 버전과의 호환성을 위해 존재합니다. |
|subscriptionGuid| string| 구독의 전역 고유 식별자입니다. |
|subscriptionName| string| 구독의 이름입니다. |
|date| string| 소비가 발생한 날짜입니다. |
|product| string| 측정기에 대한 추가 세부 정보입니다. 예: A1(VM)Windows - 아시아 태평양 동부|
|meterId| string| 사용량을 내보낸 측정기의 식별자입니다. |
|meterCategory| string| 사용된 Azure 플랫폼 서비스입니다. |
|meterSubCategory| string| 요율에 영향을 줄 수 있는 Azure 서비스 유형을 정의합니다. 예: A1 VM(Windows 외)|
|meterRegion| string| 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
|meterName| string| 측정기의 이름입니다. |
|consumedQuantity| double| 사용된 측정기의 양입니다. |
|resourceRate| double| 청구 가능 단위당 해당되는 속도입니다. |
|cost| double| 측정기에 대해 발생한 요금입니다. |
|resourceLocation| string| 측정기가 실행되고 있는 데이터 센터를 식별합니다. |
|consumedService| string| 사용된 Azure 플랫폼 서비스입니다. |
|instanceId| string| 이 식별자는 리소스의 이름 또는 정규화된 리소스 ID입니다. 자세한 내용은 [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources)를 참조하세요. |
|serviceInfo1| string| 내부 Azure 서비스 메타데이터입니다. |
|serviceInfo2| string| 예를 들어, 가상 컴퓨터의 이미지 형식 및 ExpressRoute의 ISP 이름입니다. |
|additionalInfo| string| 서비스 특정 메타데이터입니다. 예를 들어 가상 컴퓨터용 이미지 형식입니다. |
|tags| string| 태그를 추가한 고객입니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags)을 참조하세요. |
|storeServiceIdentifier| string| 이 열이 사용되지 않습니다. 이전 버전과의 호환성을 위해 존재합니다. |
|departmentName| string| 부서 이름입니다. |
|costCenter| string| 사용량이 연결된 비용 센터입니다. |
|unitOfMeasure| string| 서비스 요금이 청구되는 단위를 식별합니다. 예: GB, 시간, 10,000초 |
|resourceGroup| string| 배포된 측정기가 실행되는 리소스 그룹입니다. 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요. |
<br/>
## <a name="see-also"></a>참고 항목

* [청구 기간 API](billing-enterprise-api-billing-periods.md)

* [잔액 및 요약 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 저장소 요금 API](billing-enterprise-api-marketplace-storecharge.md) 

* [가격표 API](billing-enterprise-api-pricesheet.md)

