---
title: Azure Stream Analytics 리소스 로그 데이터 오류
description: 이 문서에서는 Azure Stream Analytics 사용할 때 발생할 수 있는 다양한 입력 및 출력 데이터 오류를 설명합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 2ca289cd52b9a406e486ee9c186be683e71f02d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020114"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics 데이터 오류

데이터 오류는 데이터를 처리하는 동안 발생하는 오류입니다.  이러한 오류는 데이터 역직렬화, 직렬화, 쓰기 작업 중에 가장 자주 발생합니다.  데이터 오류가 발생하면 Stream Analytics이 자세한 정보 및 예시 이벤트를 리소스 로그에 작성합니다. 작업에서 진단 로그를 사용하도록 설정하여 이러한 추가 세부 정보를 얻습니다. 경우에 따라 포털 알림을 통해 이 정보의 요약도 제공됩니다.

이 문서에서는 입력 및 출력 데이터 오류에 대한 다양한 오류 유형, 원인, 리소스 로그 세부 정보를 간략하게 설명합니다.

## <a name="resource-logs-schema"></a>리소스 로그 스키마

리소스 로그에 대한 스키마를 보려면 [진단 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md#resource-logs-schema)을 참조하세요. 다음 JSON은 데이터 오류에 대한 리소스 로그의 **속성** 필드에 대한 예시 값입니다.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>입력 데이터 오류

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 원인: 선택한 입력 압축 유형이 데이터와 일치하지 않습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못된 압축 유형을 포함하여 역직렬화 오류가 있는 메시지가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 설명 메시지 식별자. Event Hub의 경우 식별자는 PartitionId, Offset, Sequence Number입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 원인: 입력 데이터의 헤더가 잘못되었습니다. 예를 들어, CSV에 이름이 중복된 열이 있습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못된 헤더를 포함하여, 역직렬화 오류가 있는 메시지가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 설명 메시지 식별자. 
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 원인: CREATE TABLE 또는 TIMESTAMP BY를 통해 정의된 입력 열이 없습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 누락된 열이 있는 이벤트는 입력에서 삭제됩니다.
* 로그 세부 정보
   * 설명 메시지 식별자. 
   * 누락된 열의 이름. 
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 원인: 입력을 CREATE TABLE 문에 지정된 형식으로 변환할 수 없습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 형식 변환 오류가 있는 이벤트는 입력에서 삭제됩니다.
* 로그 세부 정보
   * 설명 메시지 식별자. 
   * 열의 이름 및 예상 형식.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 원인: 입력 데이터가 올바른 형식이 아닙니다. 예를 들어, 입력이 유효한 JSON이 아닙니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못된 데이터 오류가 발생한 후 메시지의 모든 이벤트가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 설명 메시지 식별자. 
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 원인: TIMESTAMP BY 식의 값을 날짜/시간으로 변환할 수 없습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 입력 타임스탬프가 잘못된 이벤트가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 설명 메시지 식별자. 
   * 오류 메시지입니다. 
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 원인: TIMESTAMP BY OVER timestampColumn의 값이 NULL입니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 입력 타임스탬프 키가 잘못된 이벤트는 입력에서 삭제됩니다.
* 로그 세부 정보
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 원인: 응용 프로그램 시간과 도착 시간 간의 차이가 지연 도착 허용 시간보다 큽니다.
* 포털 알림 제공: 아니요
* 리소스 로그 수준: 정보
* 영향: 지연 입력 이벤트는 작업 구성의 이벤트 순서 섹션에 있는 "다른 이벤트 처리" 설정에 따라 처리됩니다. 자세한 내용은 [시간 처리 정책](/stream-analytics-query/time-skew-policies-azure-stream-analytics)을 참조하세요.
* 로그 세부 정보
   * 응용 프로그램 시간 및 도착 시간. 
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 원인: 응용 프로그램 시간과 도착 시간 간의 차이가 5분보다 큽니다.
* 포털 알림 제공: 아니요
* 리소스 로그 수준: 정보
* 영향: 초기 입력 이벤트는 작업 구성의 이벤트 순서 섹션에 있는 "다른 이벤트 처리" 설정에 따라 처리됩니다. 자세한 내용은 [시간 처리 정책](/stream-analytics-query/time-skew-policies-azure-stream-analytics)을 참조하세요.
* 로그 세부 정보
   * 응용 프로그램 시간 및 도착 시간. 
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 원인: 이벤트는 정의된 고장 허용 오차 창에 따라 고장으로 간주됩니다.
* 포털 알림 제공: 아니요
* 리소스 로그 수준: 정보
* 영향: 잘못된 이벤트는 작업 구성의 이벤트 순서 섹션 "다른 이벤트 처리" 설정에 따라 처리됩니다. 자세한 내용은 [시간 처리 정책](/stream-analytics-query/time-skew-policies-azure-stream-analytics)을 참조하세요.
* 로그 세부 정보
   * 실제 페이로드는 최대 몇 킬로바이트입니다.

**오류 메시지**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>출력 데이터 오류

Azure Stream Analytics는 구성에 따라 출력 싱크에 대한 I/O 요청을 포함하거나 포함하지 않고 출력 데이터 오류를 식별할 수 있습니다. 예를 들어, `PartitionKey`과 같이 Azure Table을 사용할 때 필요한 열이 누락된 경우, 출력을 I/O 요청 없이 식별할 수 있습니다. 그러나 SQL 출력의 제약 조건 위반에는 I/O 요청이 필요합니다.

출력 싱크를 호출한 후에만 검색할 수 있는 몇 가지 데이터 오류가 있으며,이로 인해 처리 속도가 느려질 수 있습니다. 이 문제를 해결하려면 작업의 구성 또는 데이터 오류를 일으키는 쿼리를 변경합니다.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 원인: 출력에 필요한 열이 없습니다. 예를 들어, Azure Table PartitionKey로 정의된 열이 존재하지 않습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 누락된 필수 열을 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](./stream-analytics-output-error-policy.md) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열 이름, 레코드 식별자 또는 레코드의 일부.

**오류 메시지**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 원인: 열 값이 출력과 일치하지 않습니다. 예를 들어, 열 이름은 유효한 Azure 테이블 열이 아닙니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못된 열 이름을 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](./stream-analytics-output-error-policy.md) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름 및 레코드 식별자 또는 레코드의 일부.

**오류 메시지**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 원인: 열을 출력의 유효한 형식으로 변환할 수 없습니다. 예를 들어, 열 값이 SQL 테이블에 정의된 제약 조건 또는 형식과 호환되지 않습니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 형식 변환 오류를 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](./stream-analytics-output-error-policy.md) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부.

**오류 메시지**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 원인: 메시지 값이 지원되는 출력 크기보다 큰 경우. 예를 들어 이벤트 허브 출력의 경우 레코드가 1MB보다 큽니다.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 레코드 초과 크기 제한을 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](./stream-analytics-output-error-policy.md) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 레코드 식별자 또는 레코드의 일부.

**오류 메시지**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 원인: 레코드에 시스템 열과 이름이 같은 열이 이미 포함되어 있습니다. 예를 들어, ID 열이 다른 열에 있는 경우, 이름이 ID인 열을 가진 CosmosDB.
* 제공된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 중복 키를 비롯 한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](./stream-analytics-output-error-policy.md) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>다음 단계

* [진단 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)

* [Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해](stream-analytics-monitoring.md)