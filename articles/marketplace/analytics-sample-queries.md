---
title: 프로그래밍 분석에 대 한 샘플 쿼리
description: 이러한 샘플 쿼리를 사용 하 여 Microsoft 상업적 marketplace 분석 데이터에 프로그래밍 방식으로 액세스 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584011"
---
# <a name="sample-queries-for-programmatic-analytics"></a>프로그래밍 분석에 대 한 샘플 쿼리

이 문서에서는 Microsoft 상업적 marketplace 주문, 사용량 및 고객 보고서에 대 한 샘플 쿼리를 제공 합니다. [보고서 쿼리](analytics-programmatic-access.md#create-report-query-api) API 끝점 만들기를 호출 하 여 이러한 쿼리를 사용할 수 있습니다. 필요한 경우 [보고서 쿼리](analytics-programmatic-access.md#create-report-query-api) API 호출을 수정 하 여 더 많은 열을 추가 하 고 계산 기간을 조정 하 고 필터 조건을 추가할 수 있습니다. 지원 되는 기간은 6 개월 (6M), 12 개월 (12M) 및 사용자 지정 기간입니다.

열 이름, 특성 및 설명에 대 한 자세한 내용은 다음 표를 참조 하세요.

- [고객 정보 표](customer-dashboard.md#customer-details-table)
- [주문 정보 테이블](orders-dashboard.md#orders-details-table)
- [사용 정보 테이블](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>고객 보고서 쿼리

이러한 샘플 쿼리는 고객 보고서에 적용 됩니다.

| **쿼리 설명** | **예제 쿼리** |
| --- | --- |
| 선택한 날짜까지 파트너의 활성 고객 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| 선택한 날짜까지 파트너의 고객 변동 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| 지난 6 개월 동안 특정 지리에서 새 고객의 목록 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>사용 보고서 쿼리

이러한 샘플 쿼리는 사용 현황 보고서에 적용 됩니다.

| **쿼리 설명** | **예제 쿼리** |
| --- | --- |
| 지난 6M에 대 한 "Azure에서 청구 된" Marketplace 라이선스 형식에 대 한 가상 머신 (VM)의 표준화 된 사용 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 지난 12M에 대 한 "Azure로 청구 됨" Marketplace 라이선스 형식에 대 한 VM 원시 사용 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| 최근 6M에 대 한 "사용자 라이선스 가져오기" 마켓플레이스 라이선스 형식에 대 한 VM 정규화 된 사용 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 최근 6M에 대 한 "사용자 라이선스 가져오기" 마켓플레이스 라이선스 형식에 대 한 VM 원시 사용 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 지난 달의 유료 요금제에 대 한 사용량 날짜, 일일 총 정규화 된 사용량 및 "예상 연장 요금 (PC/CC)"에 기반 | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| 지난 달의 유료 요금제에 대 한 사용량 날짜, 일일 총 원시 사용량 및 "예상 연장 요금 (PC/CC)" 기준 | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| 특정 제품 이름에 대해, 지난 6M에 대 한 "Azure에서 청구 된" Marketplace 라이선스 유형의 VM 정규화 된 사용 | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 특정 제품 이름, 지난 6M에 대 한 요금제 사용량 | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>보고서 쿼리 주문

이러한 샘플 쿼리는 Orders 보고서에 적용 됩니다.

| **쿼리 설명** | **예제 쿼리** |
| --- | --- |
| 최근 6M에 대 한 Azure 라이선스 유형에 대 한 주문 보고서 ("Enterprise") | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| 최근 6M에 대 한 "종 량 제"으로 Azure 라이선스 형식에 대 한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| 최근 6M의 특정 제품 이름에 대 한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 최근 6M에 대 한 활성 주문의 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| 지난 6M의 취소 된 주문에 대 한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>다음 단계

- [상용 marketplace 분석 데이터에 액세스 하기 위한 Api](analytics-available-apis.md)
