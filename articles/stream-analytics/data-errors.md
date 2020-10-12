---
title: Azure Stream Analytics 리소스 로그 데이터 오류
description: 이 문서에서는 Azure Stream Analytics를 사용 하는 경우 발생할 수 있는 다양 한 입력 및 출력 데이터 오류에 대해 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 1c649499fd9eaedac0ca4ff9c182e13a9da223ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053153"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics 데이터 오류

데이터 오류는 데이터를 처리 하는 동안 발생 하는 오류입니다.  이러한 오류는 데이터 serialization, 직렬화 및 쓰기 작업 중에 발생 하는 경우가 가장 많습니다.  데이터 오류가 발생 하면 Stream Analytics는 자세한 정보와 예제 이벤트를 리소스 로그에 기록 합니다. 이러한 추가 정보를 얻기 위해 작업에서 진단 로그를 사용 하도록 설정 합니다. 경우에 따라 포털 알림을 통해이 정보에 대 한 요약도 제공 됩니다.

이 문서에서는 입력 및 출력 데이터 오류에 대 한 다양 한 오류 유형, 원인 및 리소스 로그 세부 정보를 설명 합니다.

## <a name="resource-logs-schema"></a>리소스 로그 스키마

리소스 로그의 스키마를 보려면 [진단 로그를 사용 하 여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) 을 참조 하세요. 다음 JSON은 데이터 오류에 대 한 리소스 로그의 **속성** 필드에 대 한 예제 값입니다.

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

* 원인: 선택한 입력 압축 형식이 데이터와 일치 하지 않습니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못 된 압축 유형을 포함 하 여 deserialization 오류가 있는 메시지는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 이벤트 허브의 경우 식별자는 PartitionId, 오프셋 및 시퀀스 번호입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError 헤더

* 원인: 입력 데이터의 헤더가 잘못 되었습니다. 예를 들어 CSV에는 이름이 중복 된 열이 있습니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못 된 헤더를 포함 하 여 deserialization 오류가 있는 메시지는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 원인: CREATE TABLE 또는 타임 스탬프를 사용 하 여 정의 된 입력 열이 존재 하지 않습니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 누락 된 열이 있는 이벤트는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 누락 된 열의 이름입니다. 
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 원인: 입력을 CREATE TABLE 문에 지정 된 형식으로 변환할 수 없습니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 형식 변환 오류가 있는 이벤트는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 열 및 필요한 형식의 이름입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError 데이터

* 원인: 입력 데이터가 올바른 형식이 아닙니다. 예를 들어 입력은 유효한 JSON이 아닙니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못 된 데이터 오류가 발생 한 후 메시지의 모든 이벤트가 입력에서 삭제 되었습니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 원인: TIMESTAMP BY 식의 값을 datetime으로 변환할 수 없습니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 입력 타임 스탬프가 잘못 된 이벤트는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 오류 메시지입니다. 
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 원인: TIMESTAMP BY timestampColumn의 값이 NULL입니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 입력 타임 스탬프 키가 잘못 된 이벤트는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 원인: 응용 프로그램 시간과 도착 시간 사이의 차이가 지연 도착 허용 시간 보다 큽니다.
* 제공 된 포털 알림: 아니요
* 리소스 로그 수준: 정보
* 영향: 지연 입력 이벤트는 작업 구성의 이벤트 순서 섹션에서 "다른 이벤트 처리" 설정에 따라 처리 됩니다. 자세한 내용은 [시간 처리 정책](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)을 참조 하세요.
* 로그 세부 정보
   * 응용 프로그램 시간과 도착 시간 
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 원인: 응용 프로그램 시간과 도착 시간 사이의 차이가 5 분을 초과 합니다.
* 제공 된 포털 알림: 아니요
* 리소스 로그 수준: 정보
* 영향: 초기 입력 이벤트는 작업 구성의 이벤트 순서 섹션에서 "다른 이벤트 처리" 설정에 따라 처리 됩니다. 자세한 내용은 [시간 처리 정책](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)을 참조 하세요.
* 로그 세부 정보
   * 응용 프로그램 시간과 도착 시간 
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 원인: 이벤트는 잘못 된 순서 허용 시간 창에 따라 잘못 된 것으로 간주 됩니다.
* 제공 된 포털 알림: 아니요
* 리소스 로그 수준: 정보
* 영향: 잘못 된 이벤트는 작업 구성의 이벤트 순서 섹션에서 "다른 이벤트 처리" 설정에 따라 처리 됩니다. 자세한 내용은 [시간 처리 정책](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)을 참조 하세요.
* 로그 세부 정보
   * 실제 페이로드 (킬로바이트)입니다.

**오류 메시지**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>출력 데이터 오류

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError이 없습니다.

* 원인: 출력에 필요한 열이 없습니다. 예를 들어 Azure Table PartitionKey does't으로 정의 된 열이 존재 합니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 누락 된 필수 열을 포함 한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리 됩니다.
* 로그 세부 정보
   * 열 이름, 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError가 잘못 되었습니다.

* 원인: 열 값이 출력과 일치 하지 않습니다. 예를 들어 열 이름은 유효한 Azure 테이블 열이 아닙니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 잘못 된 열 이름을 포함 한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리 됩니다.
* 로그 세부 정보
   * 열 이름, 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 원인: 출력에서 열을 올바른 형식으로 변환할 수 없습니다. 예를 들어 열 값은 SQL 테이블에 정의 된 제약 조건 또는 유형과 호환 되지 않습니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 형식 변환 오류를 비롯 한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리 됩니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 원인: 메시지의 값이 지원 되는 출력 크기 보다 큽니다. 예를 들어, 레코드는 이벤트 허브 출력에 대해 1mb 보다 큽니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 레코드 크기 제한을 초과 하는 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리 됩니다.
* 로그 세부 정보
   * 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 원인: 레코드에 시스템 열과 이름이 같은 열이 이미 있습니다. 예를 들어 id 열이 다른 열에 있는 경우 ID가 CosmosDB 인 열이 출력 됩니다.
* 제공 된 포털 알림: 예
* 리소스 로그 수준: 경고
* 영향: 중복 키를 비롯 한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리 됩니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부입니다.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>다음 단계

* [진단 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)

* [Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해](stream-analytics-monitoring.md)
