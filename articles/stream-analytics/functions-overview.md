---
title: Azure 스트림 분석의 사용자 정의 함수
description: 이 문서는 Azure Stream Analytics의 사용자 정의 함수에 대한 개요입니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115587"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure 스트림 분석의 사용자 정의 함수

Azure Stream Analytics의 SQL과 같은 쿼리 언어를 사용하면 스트리밍 데이터에 대한 실시간 분석 논리를 쉽게 구현할 수 있습니다. Stream Analytics는 쿼리에서 호출되는 사용자 지정 기능을 통해 추가적인 유연성을 제공합니다. 다음 코드 예제는 하나의 `sampleFunction` 매개 변수를 허용 하는 UDF, 각 입력 작업 수신 레코드 `sampleResult`및 결과 출력에 기록 됩니다.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>함수 유형

Azure Stream Analytics는 다음과 같은 네 가지 기능 유형을 지원합니다. 

* JavaScript 사용자 정의 함수 
* JavaScript 사용자 정의 집계 
* C# 사용자 정의 함수(Visual Studio 사용) 
* Azure Machine Learning 

기계 학습 모델을 사용한 실시간 채점, 문자열 조작, 복잡한 수학 계산, 인코딩 및 디코딩 데이터와 같은 시나리오에 이러한 함수를 사용할 수 있습니다. 

## <a name="limitations"></a>제한 사항

사용자 정의 함수는 상태 비수기이며 반환 값은 스칼라 값일 수 있습니다. 이러한 사용자 정의 함수에서 외부 REST 끝점을 호출할 수 없습니다. 

Azure 스트림 분석은 모든 함수 호출 및 반환된 결과에 대한 레코드를 유지하지 않습니다. 반복성을 보장하기 위해(예: 이전 타임스탬프에서 작업을 다시 실행하면 동일한 결과가 다시 생성됨) `Date.GetData()` `Math.random()`이러한 함수는 각 호출에 대해 동일한 결과를 반환하지 않기 때문에 또는 와 같은 함수를 사용하지 마십시오.  

## <a name="diagnostic-logs"></a>진단 로그

모든 런타임 오류는 치명적인 것으로 간주되며 활동 및 진단 로그를 통해 표시됩니다. 함수는 모든 예외 및 오류를 처리하고 쿼리에 유효한 결과를 반환하는 것이 좋습니다. 이렇게 하면 작업이 [실패한 상태로](job-states.md)이동하지 않습니다.  


## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에서 JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [Azure 스트림 분석 자바 스크립트 사용자 정의 집계](stream-analytics-javascript-user-defined-aggregates.md)
* [Azure 스트림 분석 작업에 대한 .NET 표준 사용자 정의 기능 개발](stream-analytics-edge-csharp-udf-methods.md)
* [Azure 스트림 분석을 Azure 기계 학습과 통합](machine-learning-udf.md)

