---
title: "Azure Stream Analytics JavaScript 사용자 정의 함수 | Microsoft Docs"
description: "스트림 분석 및 실시간 데이터 처리와 IoT 센서 태그 및 데이터 스트림"
keywords: "IoT 솔루션, IoT 시작, 도구"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 83b267a610a1d910fab09f8e42f079d269e3f0bb
ms.openlocfilehash: e11e6070002275544fa5a82923cdcad4412e40b7

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure Stream Analytics JavaScript 사용자 정의 함수
Azure Stream Analytics에서는 JavaScript로 작성된 사용자 정의 함수(UDF)를 지원합니다. JavaScript에서 제공하는 풍부한 메서드 집합(String, RegExp, Math, Array, Date)을 통해 Stream Analytics 작업에서 복잡한 데이터 변환을 쉽게 만들 수 있게 되었습니다.

## <a name="overview"></a>개요
JavaScript UDF는 외부 연결이 필요 없는 상태 비저장, 계산 전용 스칼라 함수를 지원합니다. 함수의 반환 값은 스칼라(단일) 값만 될 수 있습니다. 작업에 추가된 함수는 기본 제공 스칼라 함수처럼 쿼리의 어느 위치에나 사용할 수 있습니다.

JavaScript UDF가 도움이 되는 몇 가지 예제 시나리오는 다음과 같습니다.
* 정규식 함수로 문자열을 구문 분석 및 조작(예: Regexp_Replace() 및 Regexp_Extract())
* 데이터 디코딩 및 인코딩(예:&2;진을&16;진으로 변환)
* JavaScript 수학 함수로 수학 계산
* 정렬, 조인, 찾기 및 채우기 등의 배열 작업

다음은 Stream Analytics에서 JavaScript UDF로 수행할 수 없는 작업입니다.
* 외부 REST 끝점 호출(예: 역방향 IP 조회 또는 외부 원본에서 참조 데이터 끌어오기)
* 입력/출력에서 사용자 지정 이벤트 형식 직렬화 또는 역직렬화
* 사용자 지정 집계

함수 정의에서 차단되지는 않지만 Date.GetDate() 또는 Math.random()과 같은 함수는 사용하지 않아야 합니다. 이러한 함수는 호출할 때마다 **다른** 결과를 반환하며 Azure Stream Analytics 서비스에서 함수 호출 및 반환된 결과 저널을 유지하지 않습니다. 따라서 동일한 이벤트에 대해 함수가 다른 결과를 반환하면 사용자 또는 Stream Analytics 서비스에서 작업을 다시 시작할 때 반복성이 보장되지 않습니다.

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Azure Portal에서 JavaScript UDF 추가
기존 Stream Analytics 작업에서 간단한 JavaScript 사용자 정의 함수를 작성하려면 다음 단계를 따르세요.

1.  Azure Portal을 엽니다.

2.  Stream Analytics 작업을 찾은 후 **작업 토폴로지** 아래에서 함수를 클릭합니다.
 
3.  기존 함수의 비어 있는 목록이 표시되면 **추가** 아이콘을 클릭하여 새 UDF를 추가합니다.

4.  **새 함수** 블레이드에서 함수 형식으로 JavaScript를 선택하면 편집기에 기본 함수 템플릿이 표시됩니다.
 
5.  UDF 별칭으로 _hex2Int_를 입력하고 함수 구현을 다음과 같이 변경합니다.

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  **저장** 단추를 클릭하면 함수가 함수 목록에 나타납니다. 

7.  새 함수 **hex2Int**를 클릭하면 함수 정의를 확인할 수 있습니다. 모든 함수에 대해 함수 별칭 앞에 "UDF"라는 접두사가 추가됩니다. Stream Analytics 쿼리에서 **접두사**가 있는 함수를 호출해야 합니다(이 경우 **UDF.hex2Int**).
 
## <a name="calling-javascript-udf-in-a-query"></a>쿼리에서 JavaScript UDF 호출

1. **작업 토폴로지** 아래에서 **쿼리**를 클릭하여 작업 편집기를 엽니다. 

2.  쿼리를 편집하고 아래와 같이 방금 추가한 UDF를 호출합니다.

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  작업 입력을 마우스 오른쪽 단추로 클릭하여 샘플 데이터 파일 업로드
 
4.  **테스트**를 클릭하여 쿼리를 테스트합니다.


## <a name="supported-javascript-objects"></a>지원되는 JavaScript 개체
Azure Stream Analytics JavaScript UDF는 JavaScript 언어의 표준 기본 제공 개체를 지원합니다. 개체 목록은 [전역 개체](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)에 대한 문서를 참조하세요.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics 및 JavaScript 형식 변환

Stream Analytics 쿼리 언어와 JavaScript에서 지원되는 형식 간에는 차이가 있습니다. 다음 표에서는 둘 간의 변환 매핑 목록을 보여 줍니다.


---
스트림 분석 | JavaScript
--- | ---
bigint | Number(JavaScript에서는 정확히 최대 2^53의 정수만 표현할 수 있음)
DateTime | Date(JavaScript에서는 밀리초만 지원) 
double | Number
nvarchar(MAX) | 문자열
레코드 | Object
배열 | 배열
NULL | Null


JavaScript에서 ASA로의 변환도 나열되어 있습니다.

---
JavaScript | 스트림 분석
--- | ---
Number | 숫자가 반올림되고 long.MinValue와 long.MaxValue 사이에 있으면 Bigint이고, 그렇지 않으면 double
Date | DateTime
String | nvarchar(MAX)
Object | 레코드
배열 | 배열
Null, Undefined | NULL
다른 형식(예: 함수, 오류 등) | 지원되지 않음 - 런타임 오류

## <a name="troubleshooting"></a>문제 해결
JavaScript 런타임 오류는 치명적인 것으로 간주되고 활동 로그를 통해 표시됩니다. Azure Portal에서 로그를 검색하려면 작업으로 이동하고 **활동 로그**를 클릭합니다.
 

## <a name="other-javascript-udf-usage-patterns"></a>다른 JavaScript UDF 사용 패턴

### <a name="writing-nested-json-to-output"></a>중첩된 JSON을 출력에 작성
JSON 문자열을 출력에 작성하는 것은 Stream Analytics 작업 출력을 입력으로 사용하고 JSON 형식을 요구하는 후속 처리 단계가 있을 때 일반적인 작업입니다. 다음 예에서는 JSON.stringify() 함수를 호출하여 입력의 모든 이름/값 쌍을 묶고 출력에 단일 문자열 값으로 작성합니다. 

### <a name="javascript-udf-definition"></a>JavaScript UDF 정의:

```
function main(x) {
return JSON.stringify(x);
}
```

**샘플 쿼리:**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)



<!--HONumber=Feb17_HO1-->


