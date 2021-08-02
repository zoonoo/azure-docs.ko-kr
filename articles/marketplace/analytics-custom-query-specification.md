---
title: 사용자 지정 쿼리 사양
description: 사용자 지정 쿼리를 사용하여 Microsoft 상업용 Marketplace의 제품에 대한 분석 테이블에서 프로그래밍 방식으로 데이터를 추출하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: b82fb574b134065f3c0f1a7dc5a4742258914ff6
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017418"
---
# <a name="custom-query-specification"></a>사용자 지정 쿼리 사양

파트너는 이 쿼리 사양을 통해 분석 테이블에서 데이터를 추출하기 위한 사용자 지정 쿼리를 쉽게 작성할 수 있습니다. 쿼리를 사용하여 특정 조건과 일치하는 열과 메트릭만 선택할 수 있습니다. 언어 사양의 핵심은 사용자 지정 쿼리를 작성할 수 있는 데이터 세트 정의입니다.

## <a name="datasets"></a>데이터 세트

사용자 지정 쿼리는 테이블과 열을 포함하는 데이터베이스에 대해 일부 쿼리를 실행하는 것과 동일한 방식으로 열과 메트릭이 포함된 데이터 세트에서 작동합니다. 쿼리 작성에 사용할 수 있는 데이터 세트의 전체 목록은 데이터 세트 API를 사용하여 가져올 수 있습니다.

다음은 JSON으로 표시되는 데이터 세트의 예입니다.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>데이터 세트의 구성

- 데이터 세트 이름은 데이터베이스 테이블 이름과 같습니다. 예를 들어 ISVUsage입니다. 데이터 세트에는 MarketplaceSubscriptionId와 같이 선택 가능한 열 목록이 있습니다.
- 데이터 세트에는 데이터베이스의 집계 함수와 같은 메트릭도 있습니다. 예를 들면 NormalizedUsage입니다.
- 데이터를 내보낼 수 있는 고정된 시간 범위가 있습니다.

### <a name="formulating-a-query-on-a-dataset"></a>데이터 세트에 대한 쿼리 작성

다음은 다양한 유형의 데이터를 추출하는 방법을 보여주는 몇 가지 샘플 쿼리입니다.

| 쿼리 | Description |
| ------------ | ------------- |
| **SELECT** MarketplaceSubscriptionId,CustomerId **FROM** ISVUsage **TIMESPAN LAST_MONTH** | 이 쿼리는 지난 한 달 동안의 `MarketplaceSubscriptionId` 및 그에 해당하는 `CustomerId`를 모두 가져옵니다. |
| **SELECT** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **FROM** ISVUsage **ORDER BY** EstimatedExtendedChargeCC **LIMIT** 10 | 이 쿼리는 각 구독에서 판매된 라이선스의 수를 내림차순으로 정렬하여 상위 10개의 구독을 가져옵니다. |
| **SELECT** CustomerId, NormalizedUsage, RawUsage **FROM** ISVUsage **WHERE** NormalizedUsage > 100000 **ORDER BY** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | 이 쿼리는 NormalizedUsage가 10만 보다 큰 모든 고객의 NormalizedUsage와 RawUsage를 가져옵니다. |
| **SELECT** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **FROM** ISVUsage **WHERE** CustomerId IN (‘2a31c234-1f4e-4c60-909e-76d234f93161’, ‘80780748-3f9a-11eb-b378-0242ac130002’) | 이 쿼리는 두 개의 `CustomerId` 값(`2a31c234-1f4e-4c60-909e-76d234f93161` 및 `80780748-3f9a-11eb-b378-0242ac130002`)으로 `MarketplaceSubscriptionId` 및 매월 정규화된 사용량을 가져옵니다. |
|||

## <a name="query-specification"></a>쿼리 사양

이 섹션에서는 쿼리 정의와 구조에 대해 설명합니다.

### <a name="grammar-reference"></a>문법 참조

이 표에서는 쿼리에 사용되는 기호에 대해 설명합니다.

| 기호 | 의미 |
| ------------ | ------------- |
| ? | 선택 사항 |
| * | 없거나 1개 이상 |
| + | 1개 이상 |
| &#124; | 또는/목록 중 하나 |
| | |

### <a name="query-definition"></a>쿼리 정의

쿼리 문에는 SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause?, TimeSpan?와 같은 절이 있습니다.

- **SelectClause**: **SELECT** ColumOrMetricName (, ColumOrMetricName)*
    - **ColumOrMetricName**: 데이터 세트 내에 정의된 열 및 메트릭
- **FromClause**: **FROM** DatasetName
    - **DatasetName**: 데이터 세트 내에 정의된 데이터 세트 이름
- **WhereClause**: **WHERE** FilterCondition (**AND** FilterCondition)*
    - **FilterCondition**: ColumOrMetricName Operator 값
        - **Operator**:  = | > | < | >= | <= | != | LIKE | NOT LIKE | IN | NOT IN
        - **Value**: Number | StringLiteral | MultiNumberList | MultiStringList 
            - **Number**: -? [0-9]+ (. [0-9] [0-9]*)?
            - **StringLiteral**:  ' [a-zA-Z0-9_]*'
            - **MultiNumberList**: (Number  (,Number)*)
            - **MultiStringList**: (StringLiteral (,StringLiteral)*)
- **OrderClause**: **ORDER BY** OrderCondition (,OrderCondition)*
    - **OrderCondition**: ColumOrMetricName (**ASC** | **DESC**)*
    - **LimitClause**: **LIMIT** [0-9]+
    - **TimeSpan**: **TIMESPAN** ( TODAY | YESTERDAY | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | LIFETIME)

### <a name="query-structure"></a>쿼리 구조

보고서 쿼리는 여러 부분으로 구성됩니다.

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

각 파트에 대한 설명은 다음과 같습니다.

#### <a name="select"></a>SELECT

쿼리의 이 부분은 내보낼 열을 지정합니다. 선택할 수 있는 열은 데이터 세트의 `selectableColumns` 및 `availableMetrics` 섹션에 나열된 필드입니다. 선택한 필드 목록에 메트릭 열이 포함된 경우 비메트릭 열의 모든 고유한 조합에 대해 메트릭이 계산됩니다. 

**예제**:
- **SELECT** `OfferName`, `NormalizedUsage`

#### <a name="distinct"></a>DISTINCT

SELECT 다음에 DISTINCT 키워드를 추가하면 최종 내보낸 데이터에 중복 행이 없습니다. DISTINCT 키워드는 메트릭 열의 선택 여부에 관계없이 작동합니다.

**예제**:
- **SELECT DISTINCT** `MarketplaceSubscriptionId, OfferType`

#### <a name="from"></a>FROM

쿼리의 이 부분은 데이터를 내보내야 하는 데이터 세트를 나타냅니다. 여기에 지정된 데이터 세트 이름은 데이터 세트 API에서 반환되는 유효한 데이터 세트 이름이어야 합니다.

**예제**:
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

쿼리의 이 부분은 데이터 세트에 대한 필터 조건을 지정하는 데 사용됩니다. 최종적으로 내보낸 파일에는 이 절에 나열된 모든 조건과 일치하는 행만 표시됩니다. 필터 조건은 `selectableColumns` 및 `availableMetrics`에 나열된 열에 사용할 수 있습니다. 필터 조건에 지정되는 값은 연산자가 `IN` 또는 `NOT IN`인 경우에만 숫자 목록 또는 문자열 목록일 수 있습니다. 값은 항상 리터럴 문자열로 주어질 수 있으며, 이러한 값은 기본 형식의 열로 변환됩니다. 여러 필터 조건은 `AND` 연산자로 구분해야 합니다.

**예제**:

- MarketplaceSubscriptionId = ‘868368da-957d-4959-8992-3c12dc7e6260’
- CustomerName **LIKE** ‘%Contosso%’
- CustomerId **NOT IN** (1000, 1001, 1002)
- OrderQuantity=100
- OrderQuantity=‘100’
    - MarketplaceSubscriptionId=’7b487ac0-ce12-b732-dcd6-91a1e4e74a50’ AND CustomerId=’ 0f8b7fa0-eb83-a183-1225-ca153ef807aa’

#### <a name="order-by"></a>ORDER BY

쿼리의 이 부분은 내보낸 행의 정렬 조건을 지정합니다. 정렬 조건을 정의할 수 있는 열은 데이터 세트의 `selectableColumns` 및 `availableMetrics`에 속한 것이어야 합니다. 정렬의 방향을 지정하지 않으면 해당 열에서 `DESC`로 설정됩니다. 정렬은 여러 열에 대해 정의할 수 있으며, 쉼표로 구분합니다.

**예제**:

- **ORDER BY** NormalizedUsage **ASC**, EstimatedExtendedCharge(CC) **DESC**
- **ORDER BY** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

쿼리의 이 부분은 내보낼 행의 수를 지정합니다. 지정하려는 숫자는 0이 아닌 양의 정수여야 합니다.

#### <a name="timespan"></a>TIMESPAN

쿼리의 이 부분은 데이터를 내보내고자 하는 기간을 지정합니다. 값은 데이터 세트 정의에서 `availableDateRanges` 필드에 해당하는 값이어야 합니다.

### <a name="case-sensitivity-in-query-specification"></a>쿼리 사양의 대/소문자 구분

쿼리 사양은 대/소문자를 구분하지 않습니다. 미리 정의된 키워드, 열 이름 및 값은 대문자나 소문자를 사용하여 지정할 수 있습니다.

## <a name="see-also"></a>참조

- [시스템 쿼리 목록](analytics-system-queries.md)
