---
title: "Azure 청구 엔터프라이즈 API | Microsoft Docs"
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
ms.openlocfilehash: c47b652f1a7397b07deee2704509ea2803467416
ms.contentlocale: ko-kr
ms.lasthandoff: 07/31/2017

---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>기업 고객을 위한 보고 API 개요
Azure 기업 고객은 보고 API를 통해 사용량 및 청구 데이터를 기본 데이터 분석 도구로 프로그래밍 방식으로 끌어올 수 있습니다. 

## <a name="enabling-data-access-to-the-api"></a>API에 대한 데이터 액세스 사용
* **API 키 생성/검색** - 엔터프라이즈 포털에 로그인하고, 도움말 - Reporting API 아래의 자습서를 따릅니다. 이 도움말 문서의 첫 번째 섹션에서는 지정된 등록에 대한 API 키를 생성하거나 검색하는 방법에 대해 설명합니다.
* **API에서 키 전달** - API 키는 인증 및 권한 부여를 호출할 때마다 전달되어야 합니다. HTTP 헤더에 있어야 하는 속성은 다음과 같습니다.

|요청 헤더 키 | 값|
|-|-|
|권한 부여| **bearer {API_KEY}** 형식의 값을 지정합니다. <br/> 예: bearer eyr....09|

## <a name="consumption-apis"></a>사용량 API
Swagger 끝점은 [AutoRest](https://github.com/Azure/AutoRest) 또는 [Swagger CodeGen](http://swagger.io/swagger-codegen/)을 사용하여 손쉬운 API 검사와 클라이언트 SDK 생성 기능을 사용할 수 있게 하는 아래에서 설명하는 API에 대해 [여기](https://consumption.azure.com/v2/swagger/index)에서 제공됩니다. 2014년 5월 1일부터 시작하는 데이터는 이 API를 통해 사용할 수 있습니다. 

* **잔액 및 요약** - [잔액 및 요약 API](billing-enterprise-api-balance-summary.md)는 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과 요금에 대한 월별 정보 요약을 제공합니다.

* **사용량 세부 정보** - [사용량 세부 정보 API](billing-enterprise-api-usage-detail.md)는 등록에 따른 사용량과 예상 요금 의 일별 분석 결과를 제공합니다. 이 결과에는 인스턴스, 측정기 및 부서에 대한 정보도 포함 됩니다. API는 청구 기간 또는 지정된 시작 날짜와 종료 날짜를 기준으로 쿼리할 수 있습니다. 

* **Marketplace 저장소 요금** - [Marketplace 저장소 요금 API](billing-enterprise-api-marketplace-storecharge.md)는 지정된 청구 기간 또는 시작 날짜 및 종료 날짜(1회 요금은 포함되지 않음)에 대한 일별 사용량 기반 Marketplace 요금 분석 결과를 반환합니다.

* **가격표** - [가격표 API](billing-enterprise-api-pricesheet.md)는 지정된 등록 및 청구 기간에 대한 각 측정기에 적용할 수 있는 가격을 제공합니다. 

## <a name="helper-apis"></a>도우미 API
 **청구 기간 나열** - [청구 기간 API](billing-enterprise-api-billing-periods.md)는 지정된 등록에 대한 사용량 데이터를 역방향 시간 순서로 표시한 청구 기간 목록을 반환합니다. 각 기간에는 4개의 데이터 집합(잔액 요약, 사용량 세부 정보, Marketplace 요금 및 가격표)에 대한 API 경로를 가리키는 속성이 포함되어 있습니다.


## <a name="api-response-codes"></a>API 응답 코드  
|응답 상태 코드|Message|설명|
|-|-|-|
|200| 확인|오류 없음|
|401| 권한 없음| API 키를 찾을 수 없음, 유효하지 않음, 만료됨 등|
|404| 사용할 수 없음| 보고서 끝점을 찾을 수 없음|
|400| 잘못된 요청| 잘못된 매개 변수 - 날짜 범위, EA 숫자 등|
|500| 서버 오류| 예기치 않은 오류 처리 요청| 










