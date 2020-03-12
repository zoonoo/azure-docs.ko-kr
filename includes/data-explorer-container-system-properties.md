---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128754"
---
### <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

> [!Note]
> * 시스템 속성은 단일 레코드 이벤트에 대해 지원 됩니다.
> * `csv` 매핑의 경우 레코드의 시작 부분에 속성이 추가 됩니다. `json` 매핑의 경우 드롭다운 목록에 표시 되는 이름에 따라 속성이 추가 됩니다.

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 다음 속성을 포함 해야 합니다.

**테이블 스키마 예제**

데이터에 세 개의 열 (`Timespan`, `Metric`및 `Value`)이 포함 되어 있는 경우 포함 하는 속성은 `x-opt-enqueued-time` 하 고 `x-opt-offset`다음 명령을 사용 하 여 테이블 스키마를 만들거나 변경 합니다.

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**CSV 매핑 예**

다음 명령을 실행 하 여 레코드의 시작 부분에 데이터를 추가 합니다. 서 수 값을 메모 합니다.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**JSON 매핑 예**

데이터는 **데이터 연결** 블레이드 **이벤트 시스템 속성** 목록에 표시 되는 시스템 속성 이름을 사용 하 여 추가 됩니다. 다음 명령을 실행합니다.

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
