---
title: Azure Time Series Insights |의 수집 및 평면화 규칙에 대 한 예정 된 변경 내용 Microsoft Docs
description: 수집 규칙 변경
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919031"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>새 환경에 대 한 JSON 평면화 및 이스케이프 규칙의 예정 된 변경 내용

이러한 변경 내용은 *새로운* Azure Time Series Insights 종 량 제 (PAYG) 환경에만 적용 됩니다. 이러한 변경 내용은 표준 SKU 환경에는 적용 되지 않습니다.

Azure Time Series Insights 환경에서는 특정 명명 규칙 집합에 따라 저장소 열을 동적으로 만듭니다. 이벤트가 수집 되 면 규칙 집합이 JSON 페이로드 및 속성 이름에 적용 됩니다. JSON 데이터를 평면화 하 고 저장 하는 방법에 대 한 변경 내용은 7 월 2020 일에 새로운 Azure Time Series Insights 종 량 제 환경에 적용 됩니다. 이 변경 내용은 다음과 같은 경우에 영향을 줍니다.

* JSON 페이로드에 중첩 된 개체가 포함 된 경우
*  JSON 페이로드가 배열을 포함 하는 경우
*  JSON 속성 이름에 다음 네 가지 특수 문자 중 하나를 사용 하는 경우: [\. '
*  하나 이상의 TS ID 속성이 중첩 된 개체 내에 있는 경우

새 환경을 만들고 위의 사례 중 하나 이상이 이벤트 페이로드에 적용 되는 경우 데이터를 다른 방식으로 결합 하 여 저장 하는 것을 볼 수 있습니다. 다음은 변경 내용에 대 한 요약입니다.

| 현재 규칙 | 새 규칙 | 예제 JSON | 이전 열 이름 | 새 열 이름
|---|---| ---| ---|  ---|
| 중첩 된 JSON은 delineator로 밑줄을 사용 하 여 평면화 됩니다. |중첩 된 JSON은 delineator로 마침표를 사용 하 여 평면화 됩니다.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 특수 문자는 이스케이프 되지 않습니다. | 특수 문자를 포함 하는 JSON 속성 이름입니다. [\ 및 '는 [' 및 ']를 사용 하 여 이스케이프 됩니다. [' 및 '] 내에서 작은따옴표와 백슬래시를 추가로 이스케이프 합니다. 작은따옴표는로 작성 되 고 백슬래시는로 작성 됩니다. \'\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| 기본 형식의 배열은 문자열로 저장 됩니다. | 기본 형식의 배열은 동적 형식으로 저장 됩니다.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
개체의 배열은 항상 평면화 되어 여러 이벤트를 생성 합니다. | 배열 내의 개체에 TS ID 또는 timestamp 속성가 없는 경우 개체의 배열은 전체에 동적 형식으로 저장 됩니다. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>새 환경에 대 한 권장 변경 내용

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>TS ID 및/또는 타임 스탬프 속성이 개체 내에 중첩 된 경우:

*  새 배포는 새 수집 규칙과 일치 해야 합니다. 예를 들어 TS ID가 인 경우 `telemetry_tagId` 환경 TS id로 구성할 ARM 템플릿 또는 자동화 된 배포 스크립트를 업데이트 해야 `telemetry.tagId` 합니다. 이 변경은 중첩 된 JSON의 이벤트 원본 타임 스탬프에도 필요 합니다.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>페이로드에서 중첩 된 JSON 또는 특수 문자를 포함 하 고 [시계열 모델](.\time-series-insights-update-tsm.md) 변수 식을 자동으로 작성 하는 경우

*  새 수집 규칙과 일치 하도록 [유형 Batchput](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) 을 실행 하는 클라이언트 코드를 업데이트 합니다. 예를 들어의 이전 [시계열 식을](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` 다음 옵션 중 하나로 업데이트 해야 합니다.
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>다음 단계

- [Long 데이터 형식에 대 한 지원 추가를](./time-series-insights-long-data-type.md)참조 하세요.

- 읽기 [Azure Time Series Insights 미리 보기 저장소 및 수신](./time-series-insights-update-storage-ingress.md)합니다.

