---
title: Azure Stream Analytics 작업의 호환성 수준 이해
description: Azure Stream Analytics 작업의 호환성 수준을 설정하는 방법과 최신 호환성 수준의 주요 변경 내용을 알아봅니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361394"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업의 호환성 수준
 
호환성 수준이란 Azure Stream Analytics 서비스의 릴리스 관련 동작을 말합니다. Azure Stream Analytics는 관리되는 서비스로, 정기적으로 기능이 업데이트되고 성능이 향상됩니다. 일반적으로 업데이트는 최종 사용자에게 자동으로 제공됩니다. 그러나 일부 새 기능은 기존 작업의 동작 변경, 이러한 작업의 데이터를 사용하는 프로세스 변경 등과 같은 주요 변화를 가져올 수 있습니다. 호환성 수준은 Stream Analytics에 적용된 주요 변경을 나타내는 데 사용됩니다. 주요 변경 내용은 항상 새로운 호환성 수준과 함께 적용됩니다. 

호환성 수준에 따라 기존 작업이 오류 없이 실행 해야 합니다. 새 Stream Analytics 작업을 만들면 최신 호환성 수준을 사용 하 여 만드는 것이 좋습니다. 
 
## <a name="set-a-compatibility-level"></a>호환성 수준 설정 

호환성 수준은 스트림 분석 작업의 런타임 동작을 제어합니다. 포털을 사용하거나 [create job REST API call](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)을 사용하여 Stream Analytics 작업의 호환성 수준을 설정할 수 있습니다 Azure Stream Analytics는 현재 “1.0” 및 “1.1”의 두 가지 호환성 수준을 지원합니다. 기본적으로 호환성 수준은 Azure Stream Analytics 일반 공급 때 도입된 “1.0”으로 설정됩니다. 기본값을 업데이트하려면 **구성** 섹션에서 기존 Stream Analytics 작업으로 이동하여 **호환성 수준** 옵션을 선택한 후 값을 변경합니다. 

호환성 수준을 업데이트하기 전에 작업을 중지해야 합니다. 작업이 실행 중인 상태에서는 호환성 수준을 업데이트할 수 없습니다. 

![Azure Portal의 Stream Analytics 호환성 수준](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
호환성 수준을 업데이트하면 T-SQL 컴파일러는 선택된 호환성 수준에 해당하는 구문을 사용하여 작업의 유효성을 검사합니다. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>최신 호환성 수준 (1.2)의 주요 변경 내용

주요 변경 내용은 호환성 수준 1.2에에서 도입 된:

### <a name="geospatial-functions"></a>지리 공간적 함수 

**이전 버전:** Azure Stream Analytics는 Geography 계산을 사용 합니다.

**현재 버전:** Azure Stream Analytics를 사용 하면 기하학적 예상된 지역 좌표를 계산할 수 있습니다. 지리 공간적 함수 시그니처에 변하지가 않습니다. 그러나 해당 의미 체계는 약간 다를 이전 보다 더 정확한 계산을 허용 합니다.

Azure Stream Analytics는 지리 공간 참조 데이터 인덱싱을 지원합니다. 더 빠른 조인 계산에 대 한 지리 공간 요소를 포함 하는 참조 데이터를 인덱싱할 수 있습니다.

업데이트 된 지리 공간적 함수 텍스트 WKT (Well Known) 지리 공간 형식의 전체 표현을 제공합니다. GeoJson를 사용 하 여 이전에 지원 하지 않는 다른 지리 공간 구성 요소를 지정할 수 있습니다.

자세한 내용은 [Azure Stream Analytics-클라우드 및 IoT Edge에서에서 지리 공간 기능에 대 한 업데이트](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)합니다.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>여러 파티션이 있는 입력된 원본에 대 한 병렬 쿼리 실행 

**이전 버전:** Azure Stream Analytics 쿼리 입력된 원본 파티션 간 쿼리 처리를 병렬 처리에 PARTITION BY 절을 사용을 하도록 설정 합니다.

**현재 버전:** 입력된 원본 파티션 간 쿼리 논리를 병렬 처리할 수 하는 경우 Azure Stream Analytics는 별도 쿼리 인스턴스를 만들고 병렬로 계산을 실행 합니다.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 출력을 사용 하 여 네이티브 대량 API 통합

**이전 버전:** Upsert 동작이 *삽입 또는 병합*입니다.

**현재 버전:** CosmosDB 출력을 사용 하 여 대량 API 통합을 기본 처리량을 최대화 하 고 효율적으로 제한 요청을 처리 합니다.

Upsert 동작은 *삽입 또는 바꾸기*합니다.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset SQL 출력을 쓸 때

**이전 버전:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 형식을 UTC로 조정 했습니다.

**현재 버전:** DateTimeOffset은 더 이상 조정 됩니다.

### <a name="strict-validation-of-prefix-of-functions"></a>함수의 접두사의 엄격한 유효성 검사

**이전 버전:** 함수 접두사에 대 한 엄격한 유효성이 있었습니다.

**현재 버전:** Azure Stream Analytics에 함수 접두사의 엄격한 유효성 검사 합니다. 기본 제공 함수에 접두사를 추가 하면 오류가 발생 합니다. 예를 들어`myprefix.ABS(…)` 지원 되지 않습니다.

오류가 발생도 기본 제공 집계에 접두사를 추가 합니다. 예를 들어 `myprefix.SUM(…)` 지원 되지 않습니다.

"System" 접두사를 사용 하 여 오류에서 사용자 정의 함수 결과 대 한 합니다.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB 출력 어댑터의 키 속성으로 배열 및 개체를 허용 하지 않습니다

**이전 버전:** 배열 및 개체 형식은 키 속성으로 지원 되었습니다.

**현재 버전:** 키 속성으로 더 이상 배열 및 개체 형식은 사용할 수 없습니다.


## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 입력을 문제 해결](stream-analytics-troubleshoot-input.md)
* [Stream Analytics 리소스 상태](stream-analytics-resource-health.md)
