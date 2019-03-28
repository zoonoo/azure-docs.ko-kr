---
title: 기업 계약에서 Microsoft 고객 계약 Api-Azure로 마이그레이션 | Microsoft Docs
description: 이 문서는 Microsoft EA (기업 계약)는 Microsoft 고객 계약을로 마이그레이션할 경우의 결과 이해 하도록 도와줍니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: d9c5d731120f939cf7fb28c718cc4159a3702e44
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518788"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>기업 계약에서 Microsoft 고객 계약 Api로 마이그레이션

이 문서에서는 데이터 구조, API 및 EA (기업 계약) 및 Microsoft 고객 계약 (MCA) 계정 다른 시스템 통합 차이점을 이해 하도록 도와줍니다. Azure Cost Management 계정 유형 모두에 대 한 Api를 지원합니다. 검토 합니다 [에 대 한 대금 청구 계정 설정](../billing/billing-mca-setup-account.md) 계속 하기 전에 Microsoft 고객 계약 문서.

조직에 기존 EA 계정 MCA 계정을 설정 하 고와 함께에서이 문서를 검토 해야 합니다. 이전에 EA 계정 갱신 이전 등록에서 새 위치로 이동 하려면 일부 최소한의 작업만이 필요 합니다. 그러나 마이그레이션 MCA 계정에 추가 작업이 필요 합니다. 추가 작업이 기본 청구 하위 시스템의 모든 비용 관련 Api 및 서비스 제품에 영향을 주는 변경으로 인해 하는 것입니다.

## <a name="mca-apis-and-integration"></a>MCA Api와 통합

MCA Api 및 새로운 통합을 통해 수 있습니다.

- 네이티브 Azure Api를 통해 전체 API 가용성 경우
- 단일 청구 계정에 여러 개의 송장이 구성 합니다.
- Azure 서비스 사용, 제 3 자 Marketplace 사용량 및 Marketplace 구매를 사용 하 여 결합 된 API에 액세스 합니다.
- 청구 프로필 (동일 등록)에서 비용을 보려면 Azure Cost Management를 사용 하 여 합니다.
- 비용을 표시 하려면 비용 미리 정의 된 임계값을 초과 하 고 원시 데이터를 자동으로 내보낼 때 알림 받기 새 Api에 액세스 합니다.

## <a name="migration-checklist"></a>마이그레이션 검사 목록

다음 항목 도움말 MCA Api로 전환 합니다.

- 새 기능을 살펴보고 [대금 청구 계정 Microsoft 고객 계약](../billing/billing-mca-overview.md)합니다.
- 어떤 Api를 사용 하는 다음 섹션에서 바뀝니다 참조를 확인 합니다.
- 숙지 [Azure Resource Manager REST Api](/rest/api/azure)합니다.
- 아직 Azure Resource Manager Api를 사용 하는 경우 [Azure AD를 사용 하 여 클라이언트 앱 등록](/rest/api/azure/#register-your-client-application-with-azure-ad)합니다.
- 프로그래밍 코드를 업데이트할 [Azure AD 인증 사용](/rest/api/azure/#create-the-request)합니다.
- MCA API 호출을 사용 하 여 EA API 호출을 대체 하는 프로그래밍 코드를 업데이트 합니다.
- 새 오류 코드를 사용 하 여에 오류 처리를 업데이트 합니다.
- 검토 추가 통합 제품 Cloudyn 및 Power BI와 같은 다른 작업을 필요합니다.

## <a name="ea-apis-replaced-with-mca-apis"></a>MCA Api를 사용 하 여 EA Api 대체

EA Api 인증 및 권한 부여에 대 한 API 키를 사용합니다. MCA Api는 Azure AD 인증을 사용 합니다.

| 목적 | EA API | MCA API |
| --- | --- | --- |
| 잔액 및 크레딧 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 사용량 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 사용 현황 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 사용 현황 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 청구 기간 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 가격표 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download 형식 json =|csv Microsoft.Billing/billingAccounts/... / billingProfiles /... /providers/Microsoft.Consumption/pricesheets/download  |
| 예약 구매 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 예약 권장 사항 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 예약 사용량 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> azure 서비스 및 제 3 자 Marketplace 사용량 사용할 합니다 [사용량 세부 정보 API](/rest/api/consumption/usagedetails)합니다.

다음 Api MCA 청구 계정에 사용할 수 있습니다.

| 목적 | Microsoft 고객 계약 (MCA) API |
| --- | --- |
| 대금 청구 계정<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 청구 프로필<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 섹션에서는 송장<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 송장 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 청구 구독 | {scope}/billingSubscriptions |

<sup>2</sup> Api 범위에 있는 Azure portal에서 비용 관리 환경을 하 고 Api 작동 하는 개체의 목록을 반환 합니다. 비용 관리 범위에 대 한 자세한 내용은 참조 하세요. [이해 및 작업 범위를 사용 하 여](understand-work-scopes.md)입니다.

모든 기존 EA Api를 사용 하는 경우 청구 계정을 MCA 지원 하도록 업데이트 해야 합니다. 다음 표에서 다른 통합 변경 내용:

| 목적 | 이전 제품 | 새로운 혜택 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) 콘텐츠 팩 및 커넥터 | [Microsoft Azure 소비 Insights Power BI 앱](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) 고 [ Azure Consumption Insights 커넥터](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Api를 잔액 및 크레딧

합니다 [잔액 요약 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API의 월별 요약을 제공 합니다.

- 잔액
- 새 구매
- Azure Marketplace 서비스 요금
- 조정
- 서비스 초과 요금

모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다.

균형 요약 가져오기 API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API로 대체 됩니다.

사용 가능한 잔액 API를 사용 하 여 사용 가능한 잔액을 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>비용 및 사용량을 확인 하는 Api

Azure 서비스 사용, 제 3 자 Marketplace 사용량 및 다음과 같은 Api 사용 하 여 다른 Marketplace 구매에서 비용의 일별 분석 결과를 가져옵니다. 다음과 같은 별도 Api는 Azure 서비스 및 제 3 자 Marketplace 사용에 대 한 병합 되었습니다. 이전 Api로 대체 되는 [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. 이전에 표시 된 잔액에 날짜를 요약 하는 Marketplace 구매를 추가 합니다.

- [사용량 세부 정보/다운로드 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [사용량 세부 정보/제출 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [사용량 세부 정보/사용량 세부 정보 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [사용량 세부 정보/usagedetailsbycustomdate 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Marketplace 저장소 요금/marketplacecharges 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Marketplace 저장소 요금/marketplacechargesbycustomdate 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다.

위의 모든 Api/사용량 세부 정보 API에 의해 대체 됩니다.

사용량 세부 정보 API를 사용 하 여 사용량 세부 정보를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

사용량 세부 정보 API 모든 비용 관리 Api와 마찬가지로 제품은 여러 범위에서입니다. 송장 발부 된 비용에 대 한 등록 수준에서 수신할 일반적으로 사용 하 여 청구 프로필 범위.  비용 관리 범위에 대 한 자세한 내용은 참조 하세요. [이해 및 작업 범위를 사용 하 여](understand-work-scopes.md)입니다.

| Type | ID 형식 |
| --- | --- |
| 청구 계정 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 청구 프로필 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 구독 | `/subscriptions/{subscriptionId}` |
| 리소스 그룹 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

다음 쿼리 문자열 매개 변수를 사용 하 여 프로그래밍 코드를 업데이트 합니다.

| 이전 매개 변수 | 새 매개 변수 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 지원되지 않음 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

또한 변경 응답의 본문입니다.

이전 응답 본문:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

새 응답 본문:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

사용 레코드의 배열을 포함 하는 속성 이름에서 데이터를 변경 _값_합니다. 각 레코드는 자세한 속성 목록이 플랫 하는 데 사용 합니다. 그러나 모든 세부 정보는 이제 라는 중첩된 속성이 이제 각 레코드 _속성_, 태그를 제외 하 고 있습니다. 새 구조는 다른 Azure Api와 일치 합니다. 일부 속성 이름이 변경 되었습니다. 다음 표에서 해당 속성을 보여 줍니다.

| 이전 속성 | 새 속성 | 메모 |
| --- | --- | --- |
| 계정 ID | N/A | 구독 작성자 추적 되지 않습니다. InvoiceSectionId (departmentId 동일)을 사용 합니다. |
| AccountNameAccountOwnerId 및 AccountOwnerEmail | N/A | 구독 작성자 추적 되지 않습니다. InvoiceSectionName (departmentName 동일)을 사용 합니다. |
| 추가 정보 | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 이러한 속성은 반대는 참고 합니다. IsAzureCreditEnabled true 이면 ChargesBilledSeparately false가 됩니다. |
| ConsumedQuantity | quantity | &nbsp; |
| 사용한 서비스 | consumedService | 정확한 문자열 값이 달라질 수 있습니다. |
| 사용한 서비스 ID | 없음 | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| 날짜 및 usageStartDate | date | &nbsp;  |
| 일 | 없음 | 날짜에서 하루를 구문 분석합니다. |
| 부서 ID | InvoiceSectionId | 정확한 값이 다릅니다. |
| DepartmentName | InvoiceSectionName | 정확한 문자열 값이 달라질 수 있습니다. 필요한 경우 학부에 맞게 송장 섹션을 구성 합니다. |
| ExtendedCost 및 비용 | costInBillingCurrency | &nbsp;  |
| InstanceId | ResourceId | &nbsp;  |
| 반복 청구 여부 | 없음 | &nbsp;  |
| 위치 | location | &nbsp;  |
| 미터 범주 | meterCategory | 정확한 문자열 값이 달라질 수 있습니다. |
| MeterId | meterId | 정확한 문자열 값이 다릅니다. |
| 요금제 이름 | meterName | 정확한 문자열 값이 달라질 수 있습니다. |
| 요금제 지역 | meterRegion | 정확한 문자열 값이 달라질 수 있습니다. |
| 미터 하위 범주 | meterSubCategory | 정확한 문자열 값이 달라질 수 있습니다. |
| 월 | 없음 | 날짜에서 월을 구문 분석합니다. |
| 제품 이름 | 없음 | PublisherName과 productOrderName를 사용 합니다. |
| OfferId | 없음 | &nbsp;  |
| 주문 번호 | 없음 | &nbsp;  |
| PartNumber | 없음 | 및 사용 하 여 meterId productOrderName 가격을 고유 하 게 식별 합니다. |
| 계획 이름 | productOrderName | &nbsp;  |
| 제품 | 제품 |   |
| ProductId | productId | 정확한 문자열 값이 다릅니다. |
| 게시자 이름 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 정확한 문자열 값이 다릅니다. |
| 리소스 위치 | resourceLocation | &nbsp;  |
| 리소스 위치 ID | 없음 | &nbsp;  |
| 리소스 비용 | effectivePrice | &nbsp;  |
| 서비스 관리자 ID | N/A | &nbsp;  |
| 서비스 정보 1 | serviceInfo1 | &nbsp;  |
| 서비스 정보 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 정확한 문자열 값이 달라질 수 있습니다. |
| ServiceTier | meterSubCategory | 정확한 문자열 값이 달라질 수 있습니다. |
| 저장소 서비스 식별자 | N/A | &nbsp;  |
| 구독 GUID | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| 태그들 | tags | Tags 속성은 중첩 된 properties 속성 필요가 루트 개체에 적용 됩니다. |
| 측정 단위 | unitOfMeasure | 정확한 문자열 값이 다릅니다. |
| usageEndDate | date | &nbsp;  |
| Year | 없음 | 날짜의 연도 구문 분석합니다. |
| (새로 만들기) | 청구 통화 | 요금은 사용 되는 통화입니다. |
| (새로 만들기) | 청구 프로필 ID | 청구 프로필 (등록 동일)에 대 한 고유 ID입니다. |
| (새로 만들기) | 청구 프로필 이름 | (등록 동일) 청구 프로필의 이름입니다. |
| (새로 만들기) | chargeType | Azure 서비스 사용량이, Marketplace 사용량 및 구매를 구분 하기 위해 사용 합니다. |
| (새로 만들기) | 청구서 ID | 청구서에 대 한 고유 ID입니다. 현재 달에 비어 있습니다. |
| (새로 만들기) | 게시자 유형 | 구매에 대 한 게시자의 유형입니다. 사용량에 대 한 비어 있습니다. |
| (새로 만들기) | 서비스 제품군 | 구매의 형식입니다. 사용량에 대 한 비어 있습니다. |
| (새로 만들기) | 서비스 기간 종료 날짜 | 구입한 서비스에 대 한 종료 날짜입니다. |
| (새로 만들기) | 서비스 기간 시작 날짜 | 구입한 서비스에 대 한 날짜를 시작 합니다. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>청구 기간 API 청구서 API로 대체 되었습니다.

MCA 청구 계정 청구 기간을 사용 하지 마세요. 대신 특정 청구 기간에 대 한 범위 비용 청구서 사용 합니다. 합니다 [청구 기간 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 청구서 API에 의해 대체 됩니다. 모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다.

청구서 API를 사용 하 여 청구서를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>시트 Api 가격

이 섹션에서는 기존 가격 시트 Api에 설명 하 고 Microsoft 고객 계약에 대 한 가격표 API는 이동할 권장 사항을 제공 합니다. 또한 Microsoft 고객 계약에 대 한 가격표 API를 설명 하 고를 가격표의 필드에 설명 합니다. [가격표 Enterprise 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 하 고 [Enterprise는 청구 기간을 가져옵니다](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) Api 바뀝니다 가격표 API는 Microsoft 고객 계약 (Microsoft.Billing/billingAccounts/billingProfiles에 대 한 / 가격표). 새 API는 JSON 및 CSV 형식과 같은 비동기 REST 형식을 지원 합니다. 모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다.

### <a name="billing-enterprise-apis"></a>청구 엔터프라이즈 Api

엔터프라이즈 등록을 사용 하 여 가격 및 청구 기간 정보를 가져오는 청구 엔터프라이즈 Api를 사용 합니다. 인증 및 권한 부여는 Azure Active Directory 웹 토큰을 사용 합니다.

가격표 및 청구 기간 Api를 사용 하 여 지정 된 기업 등록 계약에 대 한 적용 가능한 가격을 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft 고객 계약에 대 한 가격표 API

모든 Azure 소비 및 Marketplace 서비스에 대 한 가격을 보려면 Microsoft 고객 계약에 대 한 가격 가격표 API를 사용 합니다. 청구 프로필에 대 한 표시 된 가격은 청구 프로필에 속하는 모든 구독에 적용 됩니다.

CSV 형식으로 모든 Azure 소비 서비스 가격표 데이터를 볼 수는 가격표 API를 사용 합니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

가격표 API를 사용 하 여 JSON 형식으로 모든 Azure 소비 서비스 가격표 데이터가 표시.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

API를 사용 하 여 전체 계정에 대 한 가격표를 반환 합니다. 그러나 PDF 형식으로 가격표의 요약된 버전을 가져올 수 있습니다. 특정 청구서에 대 한 청구 되는 Azure 소비 및 Marketplace 사용 서비스를 포함 하는 요약 합니다. 청구서 {invoiceId}로 식별 되는 동일를 **청구서 번호** 청구서 요약 PDF 파일에 표시 합니다. 예제는 다음과 같습니다.

![InvoiceId에 해당 하는 청구서 번호를 보여 주는 예제 이미지](./media/migrate-cost-management-api/invoicesummary.png)

CSV 형식으로 된 가격표 API를 사용 하 여 송장 정보를 보려면:

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

JSON 형식으로 된 가격표 API를 사용 하 여 송장 정보를 보려면:

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

또한 서비스 기간을 열고 현재 청구 주기에서 모든 Azure 소비 또는 Marketplace 사용량 서비스에 대 한 예상된 가격을 볼 수 있습니다.

CSV 형식으로 된 가격표 API를 사용 하 여 서비스에 대 한 예상된 가격을 보려면:

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

JSON 형식으로 된 가격표 API를 사용 하 여 서비스에 대 한 예상된 가격을 보려면:

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 고객 계약 가격 시트 api *비동기 REST Api*합니다. 이전 동기 Api에서 Api에 대 한 응답을 변경 합니다. API 응답의 본문도 변경 되었습니다.

#### <a name="old-response-body"></a>이전 응답 본문

동기 REST API 응답의 예는 다음과 같습니다.

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
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
    ]
```

#### <a name="new-response-body"></a>새 응답 본문

Api를 지원 합니다 [비동기 Azure REST](../azure-resource-manager/resource-manager-async-operations.md) 형식입니다. GET을 사용 하 여 API를 호출 하 고 다음 응답이 나타납니다.

```
No Response Body

HTTP Status 202 Accepted
```

다음 헤더는 출력의 위치를 사용 하 여 전송 됩니다.

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

다른 GET 위치에 대 한 호출을 확인 합니다. GET 호출에 대 한 응답 작업을 완료 또는 실패 상태에 도달할 때까지 동일 합니다. 완료 되 면 다운로드 URL GET 호출 위치에 대 한 응답을 반환 합니다. 것 처럼 작업을 동시에 실행 되었습니다. 예를 들면 다음과 같습니다.

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

클라이언트에 대 한 GET 호출을 축소할 수는 `Azure-AsyncOperation`합니다. 작업의 상태를 반환 하는 끝점입니다.

다음 표에서 이전 Enterprise 가져오기 가격표 API의 필드를 표시 합니다. Microsoft 고객 계약에 대 한 새 가격표에 해당 필드 포함:

| 이전 속성 | 새 속성 | 메모 |
| --- | --- | --- |
| billingPeriodId  | _해당 사항 없음_ | 사용할 수 없습니다. Microsoft 고객 계약에 대 한 송장 및 연결 된 가격표 billingPeriodId의 개념을 대체 합니다. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 정확한 문자열 값이 달라질 수 있습니다. |
| includedQuantity  | includedQuantity | Microsoft 고객 계약의 서비스에 대 한 적용 되지 않습니다. |
| partNumber  | _해당 사항 없음_ | 대신, productOrderName (offerId 동일) 및 meterid의 조합을 사용 합니다. |
| unitPrice  | unitPrice | 단가는 Microsoft 고객 계약에서 사용 하는 서비스에 적용 됩니다. |
| currencyCode  | pricingCurrency | Microsoft 고객 계약에서 통화 가격 책정 및 대금 청구 통화 가격 표현이 있습니다. currencyCode Microsoft 고객 계약의 pricingCurrency에 해당합니다. |
| offerId | productOrderName | OfferId, 대신 productOrderName를 사용할 수 있지만 OfferId 동일 하지는 않습니다. 그러나 productOrderName 및 미터 Microsoft 고객 계약의 가격 책정 및 관련 된 meterId Offerid 레거시 등록에서 결정 합니다. |

## <a name="consumption-price-sheet-api-operations"></a>가격표 API 작업 사용

소비 가격 시트 API를 사용 하면 기업 계약에 대 한 [가져오기](/rest/api/consumption/pricesheet/get) 하 고 [청구 기간에서 가져올](/rest/api/consumption/pricesheet/getbybillingperiod) subscriptionId 또는 청구 기간 범위에 대 한 작업입니다. API는 Azure 리소스 관리 인증을 사용 합니다.

가격표 API를 사용 하 여 범위에 대 한 가격표 정보를 얻으려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

청구 기간별 가격표 API를 사용 하 여 가격표 정보를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

위의 API 끝점 대신 Microsoft 고객 계약에 대해 다음 항목을 사용 합니다.

**Microsoft 고객 계약 (비동기 REST API)에 대 한 가격표 API**

이 API는 Microsoft 고객 계약 및 추가 특성을 제공 합니다.

**대금 청구 계정에 청구 프로필 범위에 대 한 가격표**

이 API는 기존 API입니다. 이 대금 청구 계정에 청구 프로필에 대 한 가격표를 제공 하도록 업데이트 되었습니다.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>대금 청구 계정에서 범위에 대 한 가격표

Azure Resource Manager 인증에는 대금 청구 계정에 등록 범위에서 가격표를 가져올 때 사용 됩니다.

청구 계정에 등록 계정에서 가격표를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft 고객 규약의 경우 다음 섹션의 정보를 사용 합니다. Microsoft 고객 계약에 사용 되는 필드 속성을 제공 합니다.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>청구 계정에 청구 프로필 범위에 대 한 가격표

대금 청구 계정 API에서 업데이트 된 가격표 가격표 CSV 형식으로 가져옵니다. MCA 프로그램에 대 한 청구 프로필 범위의 가격표를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

EA의 등록 범위의 API 응답 및 속성은 동일 합니다. 속성을 동일한 MCA 속성에 해당 합니다.

이전 속성을 [Azure Resource Manager 가격 시트 Api](/rest/api/consumption/pricesheet) 같은 새 속성은 다음 표에 및입니다.

| 이전 Azure Resource Manager 가격 시트 API 속성  | 새 Microsoft 고객 계약 가격표 API 속성   | 설명 |
| --- | --- | --- |
| 측정기 ID | _meterId_ | 측정기에 대 한 고유 식별자입니다. MeterId와 동일 합니다. |
| 요금제 이름 | meterName | 측정기의 이름입니다. 미터는 Azure 서비스 배포 가능한 리소스를 나타냅니다. |
| 미터 범주  | 서비스 | 측정기에 대 한 분류 범주 이름입니다. Microsoft 고객 계약 가격표에서 서비스와 동일 합니다. 정확한 문자열 값이 다릅니다. |
| 미터 하위 범주 | meterSubCategory | 미터 subclassification 범주의 이름입니다. 서비스의 고급 기능 집합 차별화의 분류를 기반으로 합니다. 예를 들어, 기본 SQL DB은 표준 SQL DB를 vs. |
| 요금제 지역 | meterRegion | &nbsp;  |
| 단위 | _해당 사항 없음_ | UnitOfMeasure에서 구문 분석할 수 있습니다. |
| 측정 단위 | unitOfMeasure | &nbsp;  |
| 부품 번호 | _해당 사항 없음_ | 대신 partNumber, 사용 하 여 productOrderName 및 MeterId 청구 프로필에 대 한 가격을 고유 하 게 식별 합니다. 필드는 MCA 청구서에서 partNumber 대신 MCA 청구서에 표시 됩니다. |
| 단가 | unitPrice | Microsoft 고객 계약 단위 가격입니다. |
| 통화 코드 | pricingCurrency | Microsoft 고객 계약에서 통화 가격 책정 및 청구 통화 가격을 나타냅니다. 통화 코드 Microsoft 고객 계약의 pricingCurrency와 같습니다. |
| 포함된 용량 | includedQuantity | Microsoft 고객 계약의 서비스에는 적용 되지 않습니다. 값이 0 인을 사용 하 여 보여 줍니다. |
|  ID 제공  | productOrderName | OfferId, 대신 productOrderName를 사용 합니다. 그러나 동일 하지는 않습니다 OfferId, productOrderName 및 미터 확인 Microsoft 고객 계약의 가격 책정 합니다. 레거시 등록에서 meterId 및 Offerid 관련이 있습니다. |

Microsoft 고객 계약에 대 한 가격은 기업 계약 다르게 정의 됩니다. 기업 등록 계약에서 서비스에 대 한 가격은 제품, PartNumber, 측정기, 및 제품에 대 한 고유한 것입니다. PartNumber Microsoft 고객 계약에서 사용 되지 않습니다.

Microsoft 고객 계약의 일부인 Azure 소비 서비스 가격은 meterId productOrderName에 고유 합니다. 서비스 측정기 및 제품 계획을 나타냅니다.

가격표 사이의 사용량 세부 정보 API의 사용량을 조정 하려는 productOrderName 및 meterId를 사용할 수 있습니다.

청구 소유자, 참가자, 판독기 프로필 및 송장 관리자 권한이 있는 사용자는 가격표를 다운로드할 수 있습니다.

가격표는 가격이 사용량 기반 서비스에 대 한 가격을 포함 합니다. 서비스는 Azure 사용량 및 Marketplace 사용량에 포함 됩니다. 각 서비스 기간의 끝 최신 가격과 잠긴 이며 단일 서비스 기간 동안에서 사용에 적용 됩니다. Azure 사용량 서비스에 대 한 서비스 기간은 일반적으로 달력 달입니다.

### <a name="retired-price-sheet-api-fields"></a>사용 중지 된 가격표 API 필드

다음 필드는 없거나 Microsoft 고객 계약 가격 시트 Api에서 사용할 수 있거나 동일한 필드입니다.

|사용 중지 된 필드| 설명|
|---|---|
| billingPeriodId | 적용 되지 않습니다. MCA에 대 한 InvoiceId에 해당합니다. |
| offerId | 사용할 수 없습니다. MCA에서 productOrderName에 해당합니다. |
| meterCategory  | 사용할 수 없습니다. MCA에서 서비스에 해당합니다. |
| 단위 | 사용할 수 없습니다. UnitOfMeasure에서 구문 분석할 수 있습니다. |
| currencyCode | MCA에서 pricingCurrency와 동일 합니다. |
| meterLocation | MCA에서 meterRegion와 동일 합니다. |
| partNumber partnumber | 부품 번호 MCA 청구서에 표시 되지 않으면 때문에 해당 되지 않습니다. 대신 partnumber, meterId 및 productOrderName 조합을 사용 하 여 가격을 고유 하 게 식별 합니다. |
| totalIncludedQuantity | 사용할 수 없습니다. |
| pretaxStandardRate  | 사용할 수 없습니다. |

## <a name="reservation-instance-charge-api-replaced"></a>예약 인스턴스 요금 API 대체

트랜잭션을 사용 하 여 예약 구매에 대 한 청구 가져올 수는 [예약 인스턴스 요금 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)합니다. 새 API는 제 3 자 Marketplace 제품을 포함 하 여 모든 구매를 포함 합니다. 모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다. 예약 인스턴스 요금 API는 트랜잭션 API로 대체 됩니다.

트랜잭션 API를 사용 하 여 구매 트랜잭션을 예약을 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>권장 사항 Api 대체

예약된 인스턴스 구매 권장 사항 Api 마지막 7, 30 일 또는 60 일 동안 가상 머신 사용량을 제공합니다. Api에는 또한 예약 구매 권장 사항을 제공합니다. 다음과 같습니다.

- [예약된 인스턴스 권장 사항 API 공유](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [단일 예약된 인스턴스 Recommendations API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다. Api는 이전에 나열 된 예약 권장 바뀝니다 합니다 [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

예약 Recommendations API를 사용 하 여 예약 권장 사항을 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>예약 사용량 Api 대체

예약 인스턴스 사용량 API를 사용 하 여 등록에서 예약 사용량을 가져올 수 있습니다. 에 있는 둘 이상의 예약 된 인스턴스를 등록 하는 경우에 예약된 인스턴스 구매에이 API를 사용 하 여 모든 사용을 가져올 수 있습니다.

다음과 같습니다.

- [예약된 인스턴스 사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [예약된 인스턴스 사용량 요약](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

모든 사용량 Api 인증 및 권한 부여에 대 한 Azure AD를 사용 하는 네이티브 Azure Api 바뀝니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 참조 하세요. [REST를 사용 하 여 시작](/rest/api/azure/#create-the-request)합니다. Api는 이전에 나열 된 예약 권장 바뀝니다 합니다 [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) 하 고 [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) Api.

예약 세부 정보 API를 사용 하 여 예약 세부 정보를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

예약 요약 API를 사용 하 여 예약 요약을 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Cloudyn의 Cost Management로 이동

사용 하는 조직은 [Cloudyn](https://cloudyn.com) 사용 하 여 시작 해야 [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) 비용 관리 요구 사항에 대 한 합니다. 비용 관리가 없는 온 보 딩 및 8 시간의 대기 시간을 사용 하 여 Azure portal에서 사용할 수 있습니다. 자세한 내용은 참조는 [Cost Management 설명서](index.yml)합니다.

Azure Cost Management를 사용 하 여 다음을 수행할 수 있습니다.

- 미리 정의 된 예산에 대 한 시간에 따른 비용을 봅니다. 식별 하 여 비정상 지출 한도 중지 비용 패턴을 매일 분석 합니다. 태그, 리소스 그룹, 서비스 및 위치 별로 비용을 나눕니다.
- 사용량 및 비용에 대 한 제한을 설정 하 여 중요 한 임계값에 도달 되 면 알림 받기 예산을 만듭니다. 사용자 지정 이벤트를 트리거할 조건에 대 한 엄격한 제한을 적용 하는 작업 그룹을 사용 하 여 자동화를 설정 합니다.
- 비용 및 Azure Advisor의 권장 사항이 포함 된 사용량을 최적화 합니다. 예약 구매 최적화를 검색 하 고 충분히 사용 되지 않는 virtual machines의 크기를 줄일 예산 내에서 사용 되지 않는 리소스를 삭제 합니다.
- 저장소 계정에 매일 CSV 파일을 게시 하는 비용 및 사용량 데이터 내보내기가 예약 합니다. 청구 데이터를 동기화 하 고 최신 상태로 유지 하려면 외부 시스템과 통합을 자동화 합니다.

## <a name="power-bi-integration"></a>Power BI 통합

Power BI를 사용 하 여 비용 보고 하는 경우 다음으로 전환 해야 합니다.

- Microsoft Azure 소비 Insights Power BI 앱
- Azure Consumption Insights 데스크톱 커넥터


커넥터는 최고의 유연성을 원하는 조직에 권장 됩니다. 그러나 Power BI 앱 빠른 설치에 사용할 수도 있습니다.

- 설치는 [Microsoft Azure Consumption Insights Power BI 앱](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Azure Consumption Insights 커넥터를 사용 하 여 연결](/power-bi/desktop-connect-azure-consumption-insights)

이전 Consumption Insights 콘텐츠 팩 및 커넥터는 등록 수준에서 작동합니다. 최소 읽기 권한이 필요 합니다. 새 소비 Insights Power BI 앱 및 새 Azure Consumption Insights 커넥터 청구 프로필 사용자에 대해 사용할 수 있습니다. 비용을 검토 하거나 비용 청구 프로필 가로로 표시 되도록 하려면 추가 옵션을 해야 하는 팀에서 사용 해야 [비용 분석](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) Azure portal.

## <a name="next-steps"></a>다음 단계

- 읽기를 [Cost Management 설명서](index.yml) 를 모니터링 하 고 Azure 지출 한도 제어 하는 방법을 알아봅니다. 또는 Cost Management를 사용 하 여 리소스 사용을 최적화 하려는 경우.
