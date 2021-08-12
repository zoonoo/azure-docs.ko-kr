---
title: Azure Time Series Insights Gen2의 수집 및 평면화 규칙에 대한 예정된 변경 | Microsoft Docs
description: 수집 규칙 변경
ms.service: time-series-insights
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: f89e7196bcc0bf5dfce94ea0198e6fd255270941
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110615442"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>새 환경에 대한 JSON 평면화 및 이스케이프 규칙의 예정된 변경 내용

> [!IMPORTANT]
> 이러한 변경 내용은 *새로 만든* Microsoft Azure Time Series Insights Gen2 환경에만 적용됩니다. 변경 내용은 Gen1 환경에 적용되지 않습니다.

Azure Time Series Insights Gen2 환경은 특정 명명 규칙 집합에 따라 스토리지 열을 동적으로 만듭니다. 이벤트가 수집될 때 Time Series Insights는 JSON 페이로드 및 속성 이름에 규칙 집합을 적용합니다. JSON 데이터를 평면화하고 저장하는 방법에 대한 변경 내용은 2020년 7월에 새로운 Azure Time Series Insights Gen2 환경에 적용되었습니다. 이 변경은 다음과 같은 경우에 영향을 미칩니다.

* JSON 페이로드에 중첩된 개체가 포함되어 있습니다.
* JSON 페이로드에 배열이 포함되어 있습니다.
* JSON 속성 이름에 `[` `\` `.` `'`와 같은 특수 문자 중 원하는 문자를 사용합니다.
* 하나 이상의 TS(Time Series) ID 속성이 중첩된 개체 내에 있습니다.

새 환경을 만들려고 하며 이러한 사례 중 하나 이상이 이벤트 페이로드에 적용되는 경우 데이터는 평면화되고 다르게 저장됩니다. 다음 표는 이러한 변경 내용을 요약해서 보여 줍니다.

| 현재 규칙 | 새 규칙 | 예제 JSON | 이전 열 이름 | 새 열 이름
|---|---| ---| ---|  ---|
| 중첩된 JSON은 밑줄을 구분 기호로 사용하여 평면화됩니다. |중첩된 JSON은 마침표를 구분 기호로 사용하여 평면화됩니다.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 특수 문자는 이스케이프되지 않습니다. | 특수 문자 `.` `[`  `\` 및 `'`를 포함하는 JSON 속성 이름은 `['` 및 `']`를 사용하여 이스케이프됩니다. `['` 및 `']` 내에서 작은따옴표와 백슬래시를 추가로 이스케이프합니다. 작은 따옴표는 `\'`로, 백슬래시는 `\\`로 작성됩니다.  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| 기본 형식 배열은 문자열로 저장됩니다. | 기본 형식의 배열은 동적 형식으로 저장됩니다.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
개체의 배열은 항상 평면화되어 여러 이벤트를 생성합니다. | 배열 내의 개체에 TS ID 또는 타임스탬프 속성이 없는 경우 개체의 배열은 동적 형식으로 완전히 저장됩니다. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>새 환경에 권장되는 변경 내용

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>TS ID 및/또는 타임스탬프 속성이 개체 내에 중첩된 경우

모든 새 배포는 새 수집 규칙과 일치해야 합니다. 예를 들어, TS ID가 `telemetry_tagId`인 경우 Azure Resource Manager 템플릿 또는 자동화된 배포 스크립트를 업데이트하여 `telemetry.tagId`를 환경 TS ID로 구성해야 합니다. 또한 중첩된 JSON의 이벤트 원본 타임스탬프에 대해 이러한 변경이 필요합니다.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>페이로드에 중첩된 JSON 또는 특수 문자가 포함되어 있고 [Time Series Model](./concepts-model-overview.md) 변수 식 작성을 자동화하는 경우

[TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)을 실행하는 클라이언트 코드를 새 수집 규칙과 일치하도록 업데이트합니다. 예를 들어 `"value": {"tsx": "$event.series_value.Double"}`의 이전 [Time Series 식](/rest/api/time-series-insights/reference-time-series-expression-syntax)을 다음 옵션 중 하나로 업데이트해야 합니다.

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>다음 단계

* [Azure Time Series Insights Gen2 스토리지 및 수신](./concepts-ingestion-overview.md)에 대해 알아봅니다.

* [Time Series 쿼리 API](./concepts-query-overview.md)를 사용하여 데이터를 쿼리하는 방법을 알아봅니다.

* [새 Time Series Expression 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax)에 대해 자세히 읽어보세요.