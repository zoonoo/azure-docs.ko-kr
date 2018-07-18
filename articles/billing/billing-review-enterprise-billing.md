---
title: REST API를 통한 Azure 기업 등록 청구 데이터 검토 | Microsoft Docs
description: Azure REST API를 사용하여 기업 등록 청구 정보를 검토하는 방법을 살펴봅니다.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064616"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>REST API를 사용하여 기업 등록 청구 검토

Azure Reporting API는 Azure 비용의 검토 및 관리를 지원합니다.

여기서는 기업계약 등록과 관련하여 현재 청구를 검색하는 방법을 알아봅니다.

현재 청구를 검색하려면
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>요청 빌드  

`{enrollmentID}` 매개 변수가 필요하며 EA(기업계약)의 등록 ID를 포함해야 합니다.

다음과 같은 헤더가 필요합니다. 

|요청 헤더|설명|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [API 키](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)를 설정합니다. |  

이 예에서는 현재 청구 주기의 세부 정보를 반환하는 동기 호출을 나타냅니다. 성능상의 이유로 동기 호출은 지난 달에 대한 정보를 반환합니다.  [API 비동기](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 호출을 통해 36개월의 데이터를 반환할 수도 있습니다.


## <a name="response"></a>response  

상태 코드 200(OK)은 계정의 상세 비용 목록이 포함된 성공적인 응답에 대해 반환됩니다.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

**데이터**의 각 항목은 요금을 나타냅니다.

|응답 속성|설명|
|----------------|----------|
|**비용** | 데이터센터 위치에 해당하는 통화로 나타낸 청구 금액 |
|**subscriptionGuid** | 구독의 전역 고유 ID | 
|**departmentId** | 해당하는 경우 부서 ID |
|**date** | 요금의 청구 날짜 |
|**태그** | 구독과 연결된 태그가 포함된 JSON 문자열 |
|**resourceGroup**|비용이 발생한 개체가 포함된 리소스 그룹의 이름 |
|**nextLink**| 설정한 경우 세부 정보의 다음 "페이지" URL을 지정합니다. 페이지가 마지막이면 비어 있습니다. |  
||
  
부서 ID, 리소스 그룹, 태그, 관련 필드는 EA 관리자가 정의합니다.  

이 예는 요약된 것입니다. 각 응답 필드에 대한 전체 설명은 [사용량 상세 정보 가져오기](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)를 참조하세요. 

다른 상태 코드는 오류 조건을 나타냅니다. 이런 경우 응답 개체가 요청 실패 이유를 설명합니다.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>다음 단계 
- [기업 보고 개요](https://docs.microsoft.com/azure/billing/billing-enterprise-api) 검토
- [기업 청구 REST API](https://docs.microsoft.com/rest/api/billing/) 살펴보기   
- [Azure REST API 시작하기](https://docs.microsoft.com/rest/api/azure/)   
