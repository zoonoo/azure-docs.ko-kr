---
title: 사용자 지정 쿼리 사양
description: 사용자 지정 쿼리를 사용 하 여 Microsoft 상업적 marketplace에서 제품에 대 한 분석 테이블에서 프로그래밍 방식으로 데이터를 추출 하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584021"
---
# <a name="custom-query-specification"></a>사용자 지정 쿼리 사양

파트너는이 쿼리 사양을 사용 하 여 분석 테이블에서 데이터를 추출 하는 사용자 지정 쿼리를 쉽게 작성할 수 있습니다. 쿼리를 사용 하 여 특정 조건과 일치 하는 원하는 열 및 메트릭만 선택할 수 있습니다. 언어 사양의 핵심은 사용자 지정 쿼리를 쓸 수 있는 데이터 집합 정의입니다.

## <a name="datasets"></a>데이터 세트

테이블과 열을 포함 하는 데이터베이스에 대해 일부 쿼리를 실행 하는 것과 동일한 방식으로 사용자 지정 쿼리는 열 및 메트릭이 포함 된 데이터 집합에 대해 작동 합니다. 쿼리를 작성 하기 위해 사용 가능한 데이터 집합의 전체 목록은 데이터 집합 API를 사용 하 여 가져올 수 있습니다.

다음은 JSON으로 표시 되는 데이터 집합의 예입니다.

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

### <a name="parts-of-a-dataset"></a>데이터 집합의 일부

- 데이터 집합 이름은 데이터베이스 테이블 이름과 같습니다. 예를 들어 ISVUsage를 사용 합니다. 데이터 집합에는 선택할 수 있는 열 목록 (예: MarketplaceSubscriptionId)이 있습니다.
- 데이터 집합에는 데이터베이스의 집계 함수와 같은 메트릭도 있습니다. 예를 들면 NormalizedUsage입니다.
- 데이터를 내보낼 수 있는 고정 된 시간 범위가 있습니다.

### <a name="formulating-a-query-on-a-dataset"></a>데이터 집합에 대 한 쿼리 작성

다음은 다양 한 유형의 데이터를 추출 하는 방법을 보여 주는 몇 가지 샘플 쿼리입니다.

| 쿼리 | Description |
| ------------ | ------------- |
| **선택** Isvusage **TIMESPAN LAST_MONTH** **의** CustomerId (MarketplaceSubscriptionId) | 이 쿼리는 `MarketplaceSubscriptionId` 지난 1 달 동안 고유 하 고 해당 하는를 모두 가져옵니다 `CustomerId` . |
| **선택** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **FROM** isvusage **ORDER by** EstimatedExtendedChargeCC **LIMIT** 10 | 이 쿼리는 각 구독에서 판매 된 라이선스 수의 내림차순으로 상위 10 개 구독을 가져옵니다. |
| **선택** CustomerId, NormalizedUsage, RawUsage **FROM** NormalizedUsage **WHERE** NORMALIZEDUSAGE  > 10만 **order by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | 이 쿼리는 NormalizedUsage가 10만 보다 큰 모든 고객의 NormalizedUsage 및 RawUsage를 가져옵니다. |
| **선택** MarketplaceSubscriptionId, **Monthstartdate** , NormalizedUsage **에서 CustomerId (** ' 2a31c234-1f4e-4c60-909e-76d234f93161 ', ' 80780748-3F9a-11eb-b378-0242ac130002 ') 인 Isvusage의 | 이 쿼리는 및의 `MarketplaceSubscriptionId` 두 값을 기준으로 매월 생성 되는 및 수익을 가져옵니다 `CustomerId` . `2a31c234-1f4e-4c60-909e-76d234f93161` `80780748-3f9a-11eb-b378-0242ac130002` |
|||

## <a name="query-specification"></a>쿼리 사양

이 섹션에서는 쿼리 정의와 구조에 대해 설명 합니다.

### <a name="grammar-reference"></a>문법 참조

이 표에서는 쿼리에 사용 되는 기호에 대해 설명 합니다.

| 기호 | 의미 |
| ------------ | ------------- |
| ? | 선택 사항 |
| * | 없거나 1개 이상 |
| + | 1개 이상 |
| &#124; | 또는/목록 중 하나 |
| | |

### <a name="query-definition"></a>쿼리 정의

쿼리 문에는 다음 절이 있습니다. SelectClause, FromClause, WhereClause?, OrderClause?, 범위 절? 및 TimeSpan?

- **Selectclause**: **SELECT** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: 데이터 집합 내에 정의 된 열 및 메트릭
- **FromClause**: DatasetName **에서**
    - **DatasetName**: 데이터 집합 내에 정의 된 데이터 집합 이름
- **WhereClause**: **WHERE** filtercondition (**AND** filtercondition) *
    - **Filtercondition**: ColumOrMetricName Operator 값
        - **연산자**: = | > | < | >= | <= |! = | LIKE | 좋아요 안 함 | 에서 | 안 함
        - **값**: Number | StringLiteral | MultiNumberList | MultiStringList 
            - **숫자**:-? [0-9] + (. [0-9] [0-9] *)?
            - **Stringliteral**: ' [a-za-z0-9_] * '
            - **MultiNumberList**: (number (, number) *)
            - **Multistringlist**: (stringliteral (, stringliteral) *)
- **Orderclause**: **ORDER by** Orderclause (, orderclause) *
    - **Ordercondition**: ColumOrMetricName (**ASC**  |  **DESC**) *
    - LIMIT **절**: **LIMIT** [0-9] +
    - **Timespan**: **timespan** (오늘 | 어제 | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | 수명

### <a name="query-structure"></a>쿼리 구조

보고서 쿼리는 여러 부분으로 구성 됩니다.

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

각 파트에 대 한 설명은 다음과 같습니다.

#### <a name="select"></a>SELECT

이 쿼리의 부분은 내보낼 열을 지정 합니다. 선택할 수 있는 열은 `selectableColumns` 및 `availableMetrics` 데이터 집합의 섹션에 나열 된 필드입니다. 내보낸 마지막 행은 항상 선택한 열에 고유한 값을 포함 합니다. 예를 들어 내보낸 파일에 중복 행이 없습니다. 선택한 열의 각 고유 조합에 대해 메트릭이 계산 됩니다.

**예**:
- 을 **선택** `OfferName` 합니다.`NormalizedUsage`

#### <a name="from"></a>FROM

이 쿼리의 부분은 데이터를 내보내야 하는 데이터 집합을 나타냅니다. 여기에 지정 된 데이터 집합 이름은 데이터 집합 API에서 반환 된 유효한 데이터 집합 이름 이어야 합니다.

**예**:
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

쿼리의이 부분은 데이터 집합에 대 한 필터 조건을 지정 하는 데 사용 됩니다. 이 절에 나열 된 모든 조건과 일치 하는 행만 최종 내보낸 파일에 표시 됩니다. 필터 조건은 및에 나열 된 열 중 하나에 있을 수 `selectableColumns` 있습니다 `availableMetrics` . 필터 조건에 지정 된 값은 연산자가 또는 인 경우에만 숫자 목록 또는 문자열 목록 일 수 있습니다 `IN` `NOT IN` . 값은 항상 리터럴 문자열로 지정 될 수 있으며, 이러한 값은 기본 형식의 열로 변환 됩니다. 여러 필터 조건을 작업으로 구분 해야 `AND` 합니다.

**예**:

- MarketplaceSubscriptionId = ' 868368da-957d-4959-8992-3c12dc7e6260 '
- CustomerName ( **예** : '%)
- CustomerId가 **아님** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = ' 100 '
    - MarketplaceSubscriptionId = ' 7b487ac0-ce12-b732-dcd6-91a1e4e74a50 ' 및 CustomerId = ' 0f8b7fa0-eb83-a183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDER BY

이 쿼리의 부분은 내보낸 행의 순서 조건을 지정 합니다. 순서를 정의할 수 있는 열은 `selectableColumns` 데이터 집합의 및에서 시작 해야 합니다 `availableMetrics` . 지정 된 순서 방향이 없으면 `DESC` 열에서 기본값으로 설정 됩니다. 순서는 조건을 쉼표로 구분 하 여 여러 열에 대해 정의할 수 있습니다.

**예**:

- **정렬 기준** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (참조) **DESC**
- **정렬 기준** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

이 쿼리의 부분은 내보낼 행의 수를 지정 합니다. 지정 하는 숫자는 0이 아닌 양의 정수 여야 합니다.

#### <a name="timespan"></a>TIMESPAN

이 쿼리의 부분은 데이터를 내보내야 하는 기간을 지정 합니다. 데이터 집합 정의의 필드에서 가능한 값을 지정 해야 합니다 `availableDateRanges` .

### <a name="case-sensitivity-in-query-specification"></a>쿼리 사양의 대/소문자 구분

사양은 완전히 대/소문자를 구분 하지 않습니다. 미리 정의 된 키워드, 열 이름 및 값은 대문자나 소문자를 사용 하 여 지정할 수 있습니다.

## <a name="see-also"></a>참고 항목

- [시스템 쿼리 목록](analytics-system-queries.md)
