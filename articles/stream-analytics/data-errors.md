---
title: Azure 스트림 분석 진단 로그 데이터 오류
description: 이 문서에서는 Azure Stream Analytics를 사용할 때 발생할 수 있는 다양한 입력 및 출력 데이터 오류에 대해 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398125"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure 스트림 분석 데이터 오류

데이터 오류는 데이터를 처리하는 동안 발생하는 오류입니다.  이러한 오류는 데이터 디 직렬화, 직렬화 및 쓰기 작업 중에 가장 자주 발생합니다.  데이터 오류가 발생하면 Stream Analytics는 진단 로그에 자세한 정보와 예제 이벤트를 기록합니다.  경우에 따라 이 정보의 요약은 포털 알림을 통해서도 제공됩니다.

이 문서에서는 입력 및 출력 데이터 오류에 대한 다양한 오류 유형, 원인 및 진단 로그 세부 정보를 간략하게 설명합니다.

## <a name="diagnostic-log-schema"></a>진단 로그 스키마

[진단 로그를 사용하여 진단 로그에 대한 스키마를 확인하여 Azure Stream 분석 문제를 해결합니다.](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) 다음 JSON은 데이터 오류에 대한 진단 로그의 **속성** 필드에 대한 예제 값입니다.

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

### <a name="inputdeserializererrorinvalidcompressiontype"></a>입력디직직렬화오류.유효하지 않은압축타입

* 원인: 선택한 입력 압축 유형이 데이터와 일치하지 않습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 잘못된 압축 유형을 포함하여 직렬화 오류가 있는 메시지는 입력에서 삭제됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 이벤트 허브의 경우 식별자는 PartitionId, 오프셋 및 시퀀스 번호입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>입력디직직렬화오류.잘못된헤더

* 원인: 입력 데이터의 헤더가 잘못되었습니다. 예를 들어 CSV에는 중복 된 이름이 있는 열이 있습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 잘못된 헤더를 포함하여 직렬화 오류가 있는 메시지는 입력에서 삭제됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 실제 페이로드는 최대 몇 킬로바이트까지 입니다.

**오류 메시지**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>입력디직직렬화오류.누락된 열

* 원인: CREATE TABLE 또는 TIMESTAMP BY를 통해 정의된 입력 열이 존재하지 않습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 열이 누락된 이벤트는 입력에서 삭제됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 누락된 열의 이름입니다. 
   * 실제 페이로드는 최대 몇 킬로바이트까지입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>입력디직직렬화오류.형식변환오류

* 원인: CREATE TABLE 문에 지정된 유형으로 입력을 변환할 수 없습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 형식 변환 오류가 있는 이벤트가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 열 및 예상 된 형식의 이름입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>입력디직직렬화오류.잘못된 데이터

* 원인: 입력 데이터가 올바른 형식이 아닙니다. 예를 들어 입력이 유효한 JSON이 아닙니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 잘못된 데이터 오류가 발생한 후 메시지의 모든 이벤트가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 실제 페이로드는 최대 몇 킬로바이트까지 입니다.

**오류 메시지**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>유효하지 않은 입력타임스탬프

* 원인: TIMESTAMP BY 식의 값은 날짜 시간으로 변환할 수 없습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 잘못된 입력 타임스탬프가 있는 이벤트가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 입력 메시지 식별자입니다. 
   * 오류 메시지입니다. 
   * 실제 페이로드는 최대 몇 킬로바이트까지 입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>유효하지 않은 입력시간 스탬프키

* 원인: 타임스탬프 초과 열의 값은 NULL입니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 잘못된 입력 타임스탬프 키가 있는 이벤트가 입력에서 삭제됩니다.
* 로그 세부 정보
   * 실제 페이로드는 최대 몇 킬로바이트에 불과합니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>늦은 입력 이벤트

* 원인: 신청 시간과 도착 시간 간의 차이는 늦은 도착 허용 시간 기간보다 큽니까?
* 포털 알림 제공: 아니오
* 진단 로그 수준: 정보
* 영향: 늦은 입력 이벤트는 작업 구성의 이벤트 순서 섹션에서 "다른 이벤트 처리" 설정에 따라 처리됩니다. 자세한 내용은 [시간 처리 정책을](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)참조하십시오.
* 로그 세부 정보
   * 신청 시간 및 도착 시간. 
   * 실제 페이로드는 최대 몇 킬로바이트까지 입니다.

**오류 메시지**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>얼리 입력 이벤트

* 원인: 신청 시간과 도착 시간 의 차이는 5 분을 초과합니다.
* 포털 알림 제공: 아니오
* 진단 로그 수준: 정보
* 영향: 초기 입력 이벤트는 작업 구성의 이벤트 순서 섹션에서 "다른 이벤트 처리" 설정에 따라 처리됩니다. 자세한 내용은 [시간 처리 정책을](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)참조하십시오.
* 로그 세부 정보
   * 신청 시간 및 도착 시간. 
   * 실제 페이로드는 최대 몇 킬로바이트까지 입니다.

**오류 메시지**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>아웃오더이벤트

* 원인: 정의된 순서 를 벗어난 허용 오차 창에 따라 이벤트가 순서가 바도되지 않은 것으로 간주됩니다.
* 포털 알림 제공: 아니오
* 진단 로그 수준: 정보
* 영향: 작업 구성의 이벤트 순서 섹션에서 "다른 이벤트 처리" 설정에 따라 순서가 지난 이벤트가 처리됩니다. 자세한 내용은 [시간 처리 정책을](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)참조하십시오.
* 로그 세부 정보
   * 실제 페이로드는 최대 몇 킬로바이트까지 입니다.

**오류 메시지**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>데이터 오류 출력

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>출력데이터변환오류.필수열 누락

* 원인: 출력에 필요한 열이 없습니다. 예를 들어 Azure 테이블 파티션키로 정의된 열은 존재하지 않습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 누락된 필수 열을 포함한 모든 출력 [데이터](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 변환 오류는 출력 데이터 정책 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름과 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>출력데이터변환오류.열이름 유효하지 않음

* 원인: 열 값이 출력과 일치하지 않습니다. 예를 들어 열 이름이 유효한 Azure 테이블 열이 아닙니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 잘못된 열 이름을 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름과 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>출력데이터변환오류.형식변환오류

* 원인: 열은 출력의 유효한 유형으로 변환할 수 없습니다. 예를 들어 열 값은 SQL 테이블에 정의된 제약 조건 또는 형식과 호환되지 않습니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 유형 변환 오류를 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>출력데이터변환오류.기록초과크기제한

* 원인: 메시지 값이 지원되는 출력 크기보다 큽습니다. 예를 들어 레코드는 이벤트 허브 출력에 대해 1MB보다 큽합니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 레코드 크기 제한을 초과하는 레코드를 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>출력데이터변환오류.중복키

* 원인: 레코드에 시스템 열과 이름이 같은 열이 이미 포함되어 있습니다. 예를 들어, CosmosDB는 ID 열이 다른 열에 있을 때 ID라는 열이 있는 출력입니다.
* 포털 알림 제공: 예
* 진단 로그 수준: 경고
* 영향: 중복 키를 포함한 모든 출력 데이터 변환 오류는 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정에 따라 처리됩니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부입니다.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>다음 단계

* [진단 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)

* [Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해](stream-analytics-monitoring.md)
