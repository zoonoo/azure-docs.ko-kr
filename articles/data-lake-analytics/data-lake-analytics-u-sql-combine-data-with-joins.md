---
title: "Azure Data Lake Analytics에서 U-SQL 조인으로 행 집합 결합 | Microsoft Docs"
description: "U-SQL에서 조인 연산자를 사용하여 Azure Data Lake Analytics에서 행 집합을 결합하는 방법을 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5ffc7116812e9f18f972eac2daff7935b00891f1
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="combine-rowsets-with-u-sql-joins"></a>U-SQL 조인을 사용하여 행 집합 결합

U-SQL은 테이블뿐만 아니라 행 집합(파일로부터 생성된 경우라도)도 조인할 수 있도록, INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN과 같은 일반적인 조인 연산자를 제공합니다.

다음 스크립트는 ``@searchlog``와 광고 느낌 로그를 조인하고 주어진 날짜에 쿼리 문자열의 광고를 제공합니다.

```
@adlog =
    EXTRACT UserId int,
            Ad string,
            Clicked int
    FROM "/Samples/Data/AdsLog.tsv"
    USING Extractors.Tsv();

@join =
    SELECT a.Ad, s.Query, s.Start AS Date
    FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                    ON a.UserId == s.UserId
    WHERE a.Clicked == 1;

OUTPUT @join   
    TO "/output/Searchlog-join.csv"
    USING Outputters.Csv();
```

U-SQL은 ANSI 규격 조인 구문(Rowset1 JOIN Rowset2 ON 조건자)만 지원합니다. FROM Rowset1, Rowset2 WHERE 조건자의 이전 구문은 지원되지 _않습니다_.
JOIN에 포함된 조건자는 동등 조인이고 식이 없어야 합니다. 식을 사용하는 경우 이전 행 집합의 select 절에 추가합니다. 다른 비교를 하려면 식을 WHERE 절로 이동합니다.

### <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)


