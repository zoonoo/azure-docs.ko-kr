---
title: 분석 데이터에 대한 프로그래밍 방식의 액세스 일반적인 질문
description: 상업적 Marketplace 제품에 대한 파트너 센터의 분석 데이터에 프로그래밍 방식으로 액세스하는 데 대한 일반적인 질문입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583971"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>분석 데이터에 대한 프로그래밍 방식의 액세스 일반적인 질문

이 문서에서는 상업적 Marketplace 제품에 대한 파트너 센터의 분석 데이터에 프로그래밍 방식으로 액세스하는 방법에 대한 일반적인 질문을 다룹니다.

## <a name="api-responses"></a>API 응답

200(성공) 이외의 API 응답을 받을 수 있는 다양한 시나리오는 어떤 것이 있나요?

이 표에서는 API 응답 및 응답을 수신할 경우에 수행할 작업을 설명합니다.

| 오류 설명 | 오류 코드 | 문제 해결 |
| ------------ | ------------- | ------------- |
| 권한 없음 | 401 | 이는 인증 예외입니다. Azure AD(Azure Active Directory) 토큰이 올바른지 확인합니다. Azure AD 토큰은 60분 동안 유효하며, 이 시간 후에는 Azure AD 토큰을 다시 생성해야 합니다. |
| 잘못된 표 이름 | 400 | 데이터 집합 이름이 잘못되었습니다. "모든 데이터 집합 가져오기" API를 호출하여 데이터 집합 이름을 다시 확인합니다. |
| 열 이름이 잘못되었습니다. | 400| 쿼리의 열 이름이 잘못되었습니다. "모든 데이터 집합 가져오기" API를 호출하여 열 이름을 다시 확인하거나 다음 표에서 열 이름을 참조하세요.<br><ul><li>[주문 세부 정보 표](orders-dashboard.md#orders-details-table)</li><li>[사용량 세부 정보 표](usage-dashboard.md#usage-details-table)</li><li>[고객 정보 표](customer-dashboard.md#customer-details-table)</li><li>[Marketplace 인사이트 세부 정보 테이블](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Null 또는 누락 값 | 400 | API의 요청 페이로드의 일부로 필요한 매개 변수가 누락되었을 수 있습니다. |
| 잘못된 보고서 매개 변수 | 400 | 보고서 매개 변수가 올바른지 확인합니다. 예를 들어 `RecurrenceInterval` 매개 변수의 값을 4 미만으로 지정할 수 있습니다. |
| 되풀이 간격은 4에서 90 사이여야 합니다. | 400 | `RecurrenceInterval` 요청 매개 변수의 값이 4에서 90 사이인지 확인합니다. |
| 잘못된 QueryId | 400 | 생성된 `QueryId`를 다시 확인합니다. |
| 보고서 생성 시작 시간에 대한 잘못된 보고서 매개 변수는 현재 UTC 시간에서 적어도 4시간 이후여야 합니다. | 400 | 요청 페이로드의 일부로 사용되는 시작 시간 매개 변수는 과거일 수 없습니다. 보고서의 시작 시간은 현재 UTC 시간에서 적어도 4시간 이후여야 합니다. |
| 요청된 값 ‘string’을 찾을 수 없음 | 400 | 요청 매개 변수 `callbackurl` 또는 `format`을 업데이트했는지 확인합니다. |
| 지정된 필터를 사용하여 항목을 찾을 수 없습니다. | 404 | 보고서 실행 API 가져오기에 사용된 `reportID` 매개 변수를 확인합니다. |
| 지정된 필터 조건에 대해 발생한 실행이 없습니다. reportId 또는 executionId를 다시 확인하고 보고서의 예약된 실행 시간 후에 API를 다시 시도하세요. | 404 | `reportId`가 올바른지 확인합니다. 요청 페이로드에 지정된 대로 보고서의 예약된 실행 시간 이후에 API를 다시 시도하세요. |
| 보고서를 만드는 동안 내부 오류가 발생했습니다. 상관관계 ID <> | 500 | "StartTime", "QueryStartTime" 및 "Querystarttime" 필드의 날짜 형식이 올바른지 확인합니다. |
| 서비스를 사용할 수 없음 | 500 | 계속해서 서비스를 사용할 수 없는 경우(5xx 오류) [지원 티켓](support.md)을 만드세요. |
||||

## <a name="no-records"></a>레코드 없음

보안 위치에서 보고서를 다운로드하면 API 응답 200이 나타납니다. 레코드가 없는 이유는 무엇인가요?

열 머리글에 허용되는 값 중 하나가 쿼리의 문자열에 있는지 확인합니다. 예를 들어 이 쿼리가 0개의 결과를 반환합니다.

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

이 예제에서 SKUBillingType에 대해 허용되는 값은 유료 또는 무료입니다. 다음 표에서 다양한 열에 가능한 모든 값을 참조하세요.

- [주문 세부 정보 표](orders-dashboard.md#orders-details-table)
- [사용량 세부 정보 표](usage-dashboard.md#usage-details-table)
- [고객 정보 표](customer-dashboard.md#customer-details-table)
- [Marketplace 인사이트 세부 정보 테이블](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace 분석 데이터에 액세스하기 위한 API](analytics-available-apis.md)
