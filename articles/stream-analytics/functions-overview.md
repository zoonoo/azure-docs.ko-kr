---
title: Azure Stream Analytics의 사용자 정의 함수
description: 이 문서는 Azure Stream Analytics의 사용자 정의 함수에 대한 개요입니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020505"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics의 사용자 정의 함수

Azure Stream Analytics의 SQL 유사 쿼리 언어를 사용 하면 스트리밍 데이터에 대한 실시간 분석 논리를 쉽게 구현할 수 있습니다. Stream Analytics는 쿼리에서 호출되는 사용자 지정 함수를 통해 추가 유연성을 제공 합니다. 다음 코드 예제는 하나의 매개 변수를 허용하는 `sampleFunction` UDF이며, 작업에서 수신하는 각 입력 레코드를 허용하고, 결과는 출력에 `sampleResult`로 기록됩니다.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>함수 유형

Azure Stream Analytics는 다음 네 가지 함수 형식을 지원합니다. 

* JavaScript 사용자 정의 함수 
* JavaScript 사용자 정의 집계 
* C# 사용자 정의 함수(Visual Studio 사용) 
* Azure Machine Learning 

기계 학습 모델, 문자열 조작, 복잡한 수학 계산, 데이터 인코딩 및 디코딩을 사용한 실시간 채점과 같은 시나리오에 이러한 함수를 사용할 수 있습니다. 

## <a name="limitations"></a>제한 사항

사용자 정의 함수는 상태 비저장이며 스칼라 값으로만 반환될 수 있습니다. 이러한 사용자 정의 함수에서는 외부 REST 엔드포인트를 호출할 수 없습니다. 작업의 성능에 영향을 미칠 수 있기 때문입니다. 

Azure Stream Analytics는 모든 함수 호출 및 반환된 결과에 대한 레코드를 유지하지 않습니다. 반복성을 보장하기 위해, 예를 들어 이전 타임스탬프에서 작업을 다시 실행하면 동일한 결과가 다시 생성 됩니다. 이러한 함수는 각 호출에 대해 동일한 결과를 반환하지 않으므로 `Date.GetData()` 또는 `Math.random()`과 같은 함수를 사용하지 마세요.  

## <a name="resource-logs"></a>리소스 로그

모든 런타임 오류는 치명적인 것으로 간주되고 활동 및 리소스 로그를 통해 표시됩니다. 함수에서 모든 예외와 오류를 처리하고 유효한 결과를 쿼리에 반환하는 것이 좋습니다. 이렇게 하면 작업이 [실패한 상태](job-states.md)로 전환되지 않습니다.  

## <a name="exception-handling"></a>예외 처리

데이터를 처리하는 동안 발생하는 모든 예외는 Azure Stream Analytics에서 데이터를 사용할 때 치명적인 오류로 간주됩니다. 사용자 정의 함수는 예외를 발생시키고 프로세싱을 중단시킬 가능성이 더 높습니다. 이 문제를 방지하려면 JavaScript 또는 C#에서 *try-catch* 블록을 사용하여 코드를 실행하는 동안 예외를 포착합니다. 포착된 예외는 시스템 오류를 일으키지 않고 기록되며 처리될 수 있습니다. 처리 엔진에 예기치 않은 예외가 발생하지 않도록 항상 사용자 지정 코드를 *try-catch* 블록에 래핑하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에서 JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics JavaScript 사용자 정의 집계](stream-analytics-javascript-user-defined-aggregates.md)
* [Azure Stream Analytics 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Machine Learning과 Azure Stream Analytics 통합](machine-learning-udf.md)
