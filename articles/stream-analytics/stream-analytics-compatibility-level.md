---
title: Azure 스트림 분석 호환성 수준
description: Azure Stream Analytics 작업의 호환성 수준을 설정하는 방법과 최신 호환성 수준의 주요 변경 내용을 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087854"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업의 호환성 수준

이 문서에서는 Azure Stream Analytics의 호환성 수준 옵션에 대해 설명합니다. Stream Analytics는 정기적인 기능 업데이트 및 성능 향상기능이 있는 관리형 서비스입니다. 서비스의 런타임 업데이트의 대부분은 최종 사용자가 자동으로 사용할 수 있습니다. 

그러나 서비스의 일부 새로운 기능은 기존 작업의 동작 변경 또는 실행 중인 작업에서 데이터가 사용되는 방식의 변경과 같은 주요 변경이 발생할 수 있습니다. 호환성 수준 설정을 낮게 유지하여 큰 변경 없이 기존 Stream Analytics 작업을 계속 실행할 수 있습니다. 최신 런타임 동작에 대한 준비가 되면 호환성 수준을 높여 옵트인할 수 있습니다. 

## <a name="choose-a-compatibility-level"></a>호환성 수준 선택

호환성 수준은 스트림 분석 작업의 런타임 동작을 제어합니다. 

Azure Stream Analytics는 현재 세 가지 호환성 수준을 지원합니다.

* 1.0 - 몇 년 전 Azure Stream Analytics의 일반 공급 중에 도입된 원래 호환성 수준입니다.
* 1.1 - 이전 동작
* 1.2 - 가장 최근의 개선사항으로 최신 동작

새 Stream Analytics 작업을 만들 때 최신 호환성 수준을 사용하여 만드는 것이 좋습니다. 나중에 추가된 변경 및 복잡성을 피하기 위해 최신 동작에 의존하여 작업 설계를 시작합니다.

## <a name="set-the-compatibility-level"></a>호환성 수준 설정

Azure 포털에서 또는 [작업 REST API 호출 만들기를](/rest/api/streamanalytics/stream-analytics-job)사용하여 스트림 분석 작업에 대한 호환성 수준을 설정할 수 있습니다.

Azure 포털에서 작업의 호환성 수준을 업데이트하려면 다음을 수행합니다.

1. Azure [포털을](https://portal.azure.com) 사용하여 스트림 분석 작업에 찾습니다.
2. 호환성 수준을 업데이트하기 전에 작업을 **중지합니다.** 작업이 실행 중인 상태에서는 호환성 수준을 업데이트할 수 없습니다.
3. **구성** 제목 아래에서 **호환성 수준을 선택합니다.**
4. 원하는 호환성 수준 값을 선택합니다.
5. 페이지 하단에서 **저장을** 선택합니다.

![Azure Portal의 Stream Analytics 호환성 수준](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

호환성 수준을 업데이트할 때 T 컴파일러는 선택한 호환성 수준에 해당하는 구문으로 작업의 유효성을 검사합니다.

## <a name="compatibility-level-12"></a>호환성 수준 1.2

호환성 수준 1.2에서 다음과 같은 주요 변경 사항이 도입되었습니다.

###  <a name="amqp-messaging-protocol"></a>AMQP 메시징 프로토콜

**1.2 수준**: Azure Stream Analytics는 [AMQP(고급 메시지 큐에 프로토콜)](../service-bus-messaging/service-bus-amqp-overview.md) 메시징 프로토콜을 사용하여 서비스 버스 큐 및 토픽에 기록합니다. AMQP를 사용하여 여러 플랫폼 간에 개방형 표준 프로토콜을 사용하는 하이브리드 애플리케이션을 빌드할 수 있습니다.

### <a name="geospatial-functions"></a>지리 공간적 함수

**이전 수준:** Azure 스트림 분석은 지리 계산을 사용했습니다.

**1.2 레벨:** Azure 스트림 분석을 사용하면 기하학적 투영 지리적 좌표를 계산할 수 있습니다. 지리 공간 함수의 서명에는 변화가 없습니다. 그러나 의미 체계는 약간 다르므로 이전보다 더 정확한 계산이 허용됩니다.

Azure Stream Analytics는 지리 공간 참조 데이터 인덱싱을 지원합니다. 지리 공간 요소가 포함된 참조 데이터를 인덱싱하여 조인 계산을 더 빠르게 할 수 있습니다.

업데이트된 지리 공간 함수는 잘 알려진 텍스트(WKT) 지리 공간 형식의 완전한 표현력을 제공합니다. GeoJson에서 이전에 지원되지 않았던 다른 지리 공간 구성요소를 지정할 수 있습니다.

자세한 내용은 [Azure 스트림 분석 - 클라우드 및 IoT Edge의 지리 공간 기능에 대한 업데이트를](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)참조하십시오.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>여러 파티션이 있는 입력 소스에 대한 병렬 쿼리 실행

**이전 수준:** Azure Stream Analytics 쿼리는 입력 된 소스 파티션 간에 쿼리 처리를 병렬화하기 위해 PARTITION BY 절을 사용해야 했습니다.

**1.2 레벨:** 쿼리 논리를 입력 소스 파티션 간에 병렬화할 수 있는 경우 Azure Stream Analytics는 별도의 쿼리 인스턴스를 만들고 계산을 병렬로 실행합니다.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 출력을 위한 네이티브 벌크 API 통합

**이전 수준:** upsert 동작은 *삽입 또는 병합되었습니다.*

**1.2 레벨:** CosmosDB 출력과의 네이티브 벌크 API 통합은 처리량을 극대화하고 제한 요청을 효율적으로 처리합니다. 자세한 내용은 [Azure 코스모스 DB 에 대한 Azure 스트림 분석 출력을](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)참조하십시오.

upsert 동작은 *삽입 또는 바꾸기입니다.*

### <a name="datetimeoffset-when-writing-to-sql-output"></a>SQL 출력에 쓸 때 DateTimeOffset

**이전 수준:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 형식이 UTC로 조정되었습니다.

**1.2 레벨:** DateTimeOffset은 더 이상 조정되지 않습니다.

### <a name="long-when-writing-to-sql-output"></a>SQL 출력에 쓸 때 길다.

**이전 수준:** 대상 유형에 따라 값이 잘렸습니다.

**1.2 레벨:** 대상 유형에 맞지 않는 값은 출력 오류 정책에 따라 처리됩니다.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>SQL 출력에 쓸 때 레코드 및 배열 직렬화

**이전 수준:** 레코드는 "레코드"로 기록되었으며 배열은 "배열"로 작성되었습니다.

**1.2 레벨:** 레코드 및 배열은 JSON 형식으로 직렬화됩니다.

### <a name="strict-validation-of-prefix-of-functions"></a>함수 접두사에 대한 엄격한 검증

**이전 수준:** 함수 접두사에 대한 엄격한 유효성 검사가 없었습니다.

**1.2 레벨:** Azure 스트림 분석에는 함수 접두사에 대한 엄격한 유효성 검사가 있습니다. 기본 제공 함수에 접두사를 추가하면 오류가 발생합니다. 예를 들어`myprefix.ABS(…)` 지원되지 않습니다.

기본 제공 집계에 접두사를 추가하면 오류가 발생합니다. 예를 들어 `myprefix.SUM(…)` 지원되지 않습니다.

사용자 정의 함수에 접두사 "시스템"을 사용하면 오류가 발생합니다.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>코스모스 DB 출력 어댑터의 키 속성으로 배열 및 객체 허용 허용

**이전 수준:** 배열 및 개체 형식은 키 속성으로 지원되었습니다.

**1.2 레벨:** 배열 및 개체 형식은 더 이상 키 속성으로 지원되지 않습니다.

## <a name="compatibility-level-11"></a>호환성 수준 1.1

호환성 수준 1.1의 주요 변경 내용은 다음과 같습니다.

### <a name="service-bus-xml-format"></a>Service Bus XML 형식

**1.0 레벨:** Azure 스트림 분석데이터계약 직렬화기를 사용하므로 메시지 콘텐츠에 XML 태그가 포함되었습니다. 예를 들어:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 레벨:** 메시지 콘텐츠에는 추가 태그 없이 직접 스트림이 포함됩니다. 예: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>필드 이름의 대/소문자 구분 유지

**1.0 레벨:** Azure Stream Analytics 엔진에서 처리할 때 필드 이름이 소문자로 변경되었습니다.

**1.1 수준:** Azure Stream Analytics 엔진에서 처리될 때 필드 이름에 대한 대/소문자 구분이 유지됩니다.

> [!NOTE]
> 지속적인 대/소문자 구분은 에지 환경을 사용하여 호스팅되는 Stream Analytic 작업에 아직 사용할 수 없습니다. 결과적으로, 작업이 에지에서 호스팅되는 경우 모든 필드 이름은 소문자로 변환됩니다.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 레벨:** 테이블 만들기 명령은 NaN(번호가 아님)으로 이벤트를 필터링하지 않았습니다. 왜냐하면 이러한 이벤트는 이러한 숫자에 대해 문서화된 범위를 벗어나기 때문입니다.

**1.1 레벨:** 테이블 만들기를 사용하면 강력한 스키마를 지정할 수 있습니다. Stream Analytics 엔진은 데이터가 이 스키마를 준수하는지 확인합니다. 이 모델을 사용하면 이 명령으로 NaN 값을 사용하는 이벤트를 필터링할 수 있습니다.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>JSON에서 날짜 시간 문자열에 대한 자동 업캐스트 를 사용하지 않도록 설정

**1.0 레벨:** JSON 파서는 날짜/시간/영역 정보를 DateTime 유형으로 사용하여 문자열 값을 자동으로 업캐스트한 다음 UTC로 변환합니다. 이 동작으로 인해 시간대 정보가 손실됩니다.

**1.1 레벨:** DateTime 형식에 날짜/시간/영역 정보가 있는 문자열 값의 더 이상 자동으로 업캐스트가 없습니다. 따라서 표준 시간대 정보가 유지됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 입력 문제 해결](stream-analytics-troubleshoot-input.md)
* [스트림 분석 리소스 상태](stream-analytics-resource-health.md)
