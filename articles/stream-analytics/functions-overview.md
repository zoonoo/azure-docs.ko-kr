---
title: Azure Stream Analytics의 사용자 정의 함수
description: 이 문서는 Azure Stream Analytics의 사용자 정의 함수에 대 한 개요입니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133476"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics의 사용자 정의 함수

Azure Stream Analytics의 SQL 유사 쿼리 언어를 사용 하면 스트리밍 데이터에 대 한 실시간 분석 논리를 쉽게 구현할 수 있습니다. Stream Analytics은 쿼리에서 호출 되는 사용자 지정 함수를 통해 추가 유연성을 제공 합니다. 다음 코드 예제는 하나의 매개 변수를 `sampleFunction` 허용 하는 UDF 이며, 작업에서 수신 하는 각 입력 레코드를 허용 하 고, 결과는 `sampleResult`출력에로 기록 됩니다.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>함수 유형

Azure Stream Analytics는 다음 네 가지 함수 형식을 지원 합니다. 

* JavaScript 사용자 정의 함수 
* JavaScript 사용자 정의 집계 
* C # 사용자 정의 함수 (Visual Studio 사용) 
* Azure Machine Learning 

기계 학습 모델, 문자열 조작, 복잡 한 수학 계산, 인코딩 및 디코딩 데이터를 사용 하 여 실시간 점수 매기기와 같은 시나리오에 이러한 함수를 사용할 수 있습니다. 

## <a name="limitations"></a>제한 사항

사용자 정의 함수는 상태 비저장 이며 반환 값은 스칼라 값일 수만 있습니다. 이러한 사용자 정의 함수에서 외부 REST 끝점을 호출할 수 없습니다. 작업의 성능에 영향을 미칠 수 있기 때문입니다. 

Azure Stream Analytics는 모든 함수 호출 및 반환 된 결과에 대 한 레코드를 유지 하지 않습니다. 반복성를 보장 하기 위해 예를 들어 이전 타임 스탬프에서 작업을 다시 실행 하면 동일한 결과가 다시 생성 됩니다. 이러한 함수는 각 호출 `Date.GetData()` 에 `Math.random()`대해 동일한 결과를 반환 하지 않으므로 또는과 같은 함수를 사용 하지 마세요.  

## <a name="resource-logs"></a>리소스 로그

모든 런타임 오류는 치명적인 것으로 간주 되 고 활동 및 리소스 로그를 통해 표시 됩니다. 함수에서 모든 예외와 오류를 처리 하 고 유효한 결과를 쿼리에 반환 하는 것이 좋습니다. 이렇게 하면 작업이 [실패 한 상태로](job-states.md)전환 되지 않습니다.  


## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에서 JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript 사용자 정의 집계 Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Azure Stream Analytics 작업에 대 한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Machine Learning와 Azure Stream Analytics 통합](machine-learning-udf.md)

