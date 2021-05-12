---
title: 시스템 쿼리 목록
description: Microsoft 상업적 마켓플레이스에서 제품에 대한 분석 데이터를 프로그래밍 방식으로 가져오는데 사용할 수 있는 시스템 쿼리에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584010"
---
# <a name="list-of-system-queries"></a>시스템 쿼리 목록

다음 시스템 쿼리는 `QueryId`을 직접 사용하여 [보고서 만들기 API](analytics-programmatic-access.md#create-report-api)에서 사용할 수 있습니다. 시스템 쿼리는 6 개월(6M) 계산 기간 동안 파트너 센터의 보고서를 내보내는 것과 같습니다.

열 이름, 특성 및 설명에 대한 자세한 내용은 다음 문서를 참조하세요.

- [상업용 마켓플레이스 분석의 고객 대시보드](customer-dashboard.md#customer-details-table)
- [상업용 마켓플레이스 분석의 주문 대시보드](orders-dashboard.md#orders-details-table)
- [상업용 마켓플레이스 분석의 사용량 대시보드](usage-dashboard.md#usage-details-table)
- [상업용 마켓플레이스 분석의 Marketplace Insights 대시보드](insights-dashboard.md#marketplace-insights-details-table)

다음 섹션에서는 다양한 보고서에 대한 보고서 쿼리를 제공합니다.

## <a name="customers-report-query"></a>고객 보고서 쿼리

**보고서 설명**: 지난 6M에 대한 고객 보고서

**QueryID**: `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**보고서 쿼리**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>주문 보고서 쿼리

**보고서 설명**: 지난 6개월에 대한 주문 보고서

**QueryId**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**보고서 쿼리**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>사용량 보고서 쿼리

**보고서 설명**: 지난 6개월에 대한 VM 정규화된 사용량 보고서

**QueryID**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**보고서 쿼리**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**보고서 설명**: 지난 6개월에 대한 VM 원시 사용량 보고서

**QueryID**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**보고서 쿼리**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**보고서 설명**: 지난 6개월에 대한 요금제 사용량 보고서

**QueryID**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**보고서 쿼리**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>마켓플레이스 인사이트 보고서 쿼리

**보고서 설명**: 지난 6개월에 대한 마켓플레이스 인사이트 보고서

**QueryID**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**보고서 쿼리**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>다음 단계

- [상업용 마켓플레이스 분석 데이터에 액세스하기 위한 API](analytics-available-apis.md)
- [상업용 마켓플레이스 분석 데이터에 액세스하기 위한 샘플 애플리케이션](analytics-sample-application.md)
