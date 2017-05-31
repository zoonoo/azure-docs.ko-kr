---
title: "U-SQL 언어 시작 | Microsoft Docs"
description: "U-SQL 언어의 기본 사항에 대해 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4884d96e8126337f62af23316935978cfe219ec8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="get-started-with-u-sql"></a>U-SQL 시작
U-SQL은 선언적 SQL을 명령적 C#에 결합하여 규모에 관계 없이 데이터를 처리할 수 있도록 하는 언어입니다. U-SQL의 확장성 있는 분산 쿼리 기능을 통해 Azure SQL Database와 같은 관계형 저장소의 데이터를 효율적으로 분석할 수 있습니다. U-SQL을 사용하면 읽기에 대한 스키마를 적용하고 사용자 지정 논리 및 UDF를 삽입하여 구조화되지 않은 데이터를 처리할 수 있습니다. 또한 U-SQL에는 모든 규모에서 실행하는 방법을 세부적으로 제어할 수 있게 해주는 확장성이 포함되어 있습니다. 

## <a name="learning-resources"></a>학습 리소스

**U-SQL 언어 구문**에 대한 자세한 내용은 [U-SQL 언어 참조](http://go.microsoft.com/fwlink/p/?LinkId=691348)를 참조하세요.

**U-SQL 디자인 철학**을 이해하려면 Visual Studio 블로그 게시물 [Introducing U-SQL – A Language that makes Big Data Processing Easy(U-SQL 소개 - 빅 데이터 처리를 수월하게 해주는 언어)](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서의 U-SQL 샘플을 살펴본 후에 [자습서: Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 읽고 완료하세요. 이 자습서는 Azure Data Lake Tools for Visual Studio에서 U-SQL을 사용하는 기법에 대해 설명합니다.

## <a name="your-first-u-sql-script"></a>첫 번째 U-SQL 스크립트

다음 U-SQL 스크립트는 매우 간단하며 U-SQL 언어의 많은 측면을 파악하도록 합닏.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

이 스크립트에는 변환 단계가 없습니다. `SearchLog.tsv`라는 원본 파일을 읽어와서 스키마를 만들고, 행 집합을 SearchLog-first-u-sql.csv 파일에 다시 씁니다.

`Duration` 필드의 데이터 형식 옆에 있는 물음표를 보세요. 이는 `Duration` 필드가 null이어도 된다는 것을 의미합니다.

### <a name="key-concepts"></a>주요 개념
* **Rowset 변수**: 행 집합을 생성하는 각 쿼리 식은 변수에 할당될 수 있습니다. U-SQL은 스크립트의 T-SQL 변수 이름 지정 패턴(예: `@searchlog`)을 따릅니다.
* **EXTRACT** 키워드는 파일에서 데이터를 읽고 읽기에 대한 스키마를 정의합니다. `Extractors.Tsv`는 탭으로 구분된 값 파일에 대한 기본 제공 U-SQL 추출기입니다. 사용자 지정 추출기를 개발할 수 있습니다.
* **OUTPUT**은 행 집합의 데이터를 파일에 씁니다. `Outputters.Csv()`는 쉼표로 구분된 값 파일을 만들기 위한 기본 제공 U-SQL 출력기입니다. 사용자 지정 출력기를 개발할 수 있습니다.

### <a name="file-paths"></a>파일 경로

EXTRACT 및 OUTPUT 문은 파일 경로를 사용합니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다.

이 절대 파일 경로는 `mystore`라는 Data Lake Store의 파일을 참조합니다.

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

이 절대 파일 경로는 `myblobaccount`라는 Azure Blob Storage 계정 및 `mycontainer`라는 컨테이너의 파일을 참조합니다.

    wasb://mycontainer@myblobaccount.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >공용 Blob 또는 공용 컨테이너 액세스 권한이 있는 Azure Blob Storage 컨테이너는 현재 지원되지 않습니다.

이 상대 파일 경로는 `"/"`로 시작합니다. 이 경로는 Data Lake Analytics 계정과 연결된 기본 Data Lake Store 계정의 파일을 참조합니다.

    TO "/output/SearchLog-first-u-sql.csv"

## <a name="use-scalar-variables"></a>스칼라 변수 사용

스크립트 유지 관리를 간편하게 만들기 위해 스칼라 변수를 사용할 수 있습니다. 이전 U-SQL 스크립트를 다음과 같이 작성할 수도 있습니다.

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>변환 행 집합

**SELECT** 를 사용하여 행 집합을 변환합니다.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 절에는 [C# 부울 식](https://msdn.microsoft.com/library/6a71f45d.aspx)을 사용합니다. 자신의 식 및 함수에 C# 식 언어를 사용할 수 있습니다. 식 및 함수를 논리 결합(ANDs) 및 분리(ORs)와 결합하여 더 복잡한 필터링을 수행할 수 있습니다.

다음 스크립트는 DateTime.Parse() 메서드와 논리 결합을 사용합니다.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >두 번째 쿼리는 첫 번째 행 집합의 결과를 기반으로 작동하며, 두 필터의 결합이 결과가 됩니다. 또한 변수 이름은 재사용이 가능하며 이름에는 어휘 범위(정적 범위)가 적용됩니다.

## <a name="aggregate-rowsets"></a>집계 행 집합
U-SQL에는 개발자에게 친숙한 ORDER BY, GROUP BY 및 집계가 제공됩니다.

다음 쿼리는 지역 당 총 기간을 알아내고 최장 기간 다섯 개를 순서대로 표시합니다.

U-SQL 행 집합은 다음 쿼리를 위해 이 순서를 유지하지 않습니다. 따라서 출력 순서를 정하려면 OUTPUT 문에 ORDER BY를 추가해야 합니다.

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL ORDER BY 절에서는 SELECT 식에 FETCH 절을 사용해야 합니다.

U-SQL HAVING 절은 HAVING 조건을 만족하는 그룹으로 출력을 제한하는 데 사용될 수 있습니다.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)

## <a name="let-us-know-what-you-think"></a>의견 제시
* [기능 요청 제출](http://aka.ms/adlafeedback)
* [포럼에서 도움말 보기](http://aka.ms/adlaforums)
* [U-SQL에 대한 피드백 제공](http://aka.ms/usqldiscuss)

