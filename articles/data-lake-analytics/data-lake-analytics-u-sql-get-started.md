---
title: "자습서: Azure Data Lake Analytics U-SQL 언어 시작 | Microsoft 문서"
description: "Azure Data Lake Analytics U-SQL 언어에 대해 자세히 알아보려면 이 자습서를 사용하세요."
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
translationtype: Human Translation
ms.sourcegitcommit: 96747e898b2d84cbce9251758a90941f85112dd0
ms.openlocfilehash: 7aa3844b9bdc2c5372c6e54b05296dcde6b1c05f


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>자습서: Azure 데이터 레이크 분석 U-SQL 언어 시작
U-SQL은 모든 규모의 데이터를 처리하도록 SQL의 장점을 사용자 코드의 표현력과 결합하는 언어입니다. U-SQL의 확장성 있는 분산 쿼리 기능을 통해 Azure SQL Database와 같은 관계형 저장소의 데이터를 효율적으로 분석할 수 있습니다. U-SQL을 사용하면 읽기에 대한 스키마를 적용하고 사용자 지정 논리 및 UDF를 삽입하여 구조화되지 않은 데이터를 처리할 수 있습니다. 또한 U-SQL에는 모든 규모에서 실행하는 방법을 세부적으로 제어할 수 있게 해주는 확장성이 포함되어 있습니다. U-SQL 이면의 디자인 철학에 대해 자세히 알아보려면 Visual Studio 블로그 게시물 [Introducing U-SQL – A Language that makes Big Data Processing Easy(U-SQL 소개 - 빅 데이터 처리를 수월하게 해주는 언어)](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)를 참조하세요.

U-SQL은 어떤 면에서 ANSI SQL 또는 T-SQL과 다릅니다. 예를 들어 SELECT와 같은 키워드가 모두 대문자여야 합니다.

 SELECT 절 및 WHERE 조건자 내부의 형식 시스템과 식 언어가 C#로 작성됩니다. 다시 말해, 데이터 형식은 C# 형식이고, C# NULL 의미 체계가 사용되며, 조건부 안의 비교 연산은 C# 구문(예: a == "foo")을 따릅니다. 또한 값 전체가 .NET 개체이기 때문에 개체(예: "f o o o".Split(' ') )에 작동하는 모든 메서드를 쉽게 사용할 수 있습니다.

U-SQL에 대한 자세한 내용은 [U-SQL 언어 참조](http://go.microsoft.com/fwlink/p/?LinkId=691348)를 참조하세요.

### <a name="prerequisites"></a>필수 조건
[자습서: Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 아직 완료하지 않은 경우 내용을 읽고 완료해야 합니다. 자습서를 완료했으면 이 문서로 돌아오세요.

자습서에서 다음 U-SQL 스크립트를 사용하여 Azure Data Lake Analytics 작업을 실행했습니다.

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

이 스크립트에는 변환 단계가 없습니다. SearchLog.tsv라는 원본 파일을 읽어와서 스키마를 만들고, 행 집합을 SearchLog-first-u-sql.csv 파일에 다시 씁니다.

**Duration** 필드의 데이터 형식 옆에 있는 물음표를 보세요. 이는 **Duration** 필드가 null이어도 된다는 것을 의미합니다.

스크립트에서 다음과 같은 개념 및 키워드를 찾을 수 있습니다.

* Rowset 변수: 행 집합을 생성하는 각 쿼리 식은 변수에 할당될 수 있습니다. U-SQL은 T-SQL 변수 이름 지정 패턴(예: 스크립트의 (@searchlog,)을 따릅니다.

 >[!NOTE]
 >할당은 실행을 강제 적용하지 않습니다. 더 복잡한 식을 작성할 수 있도록 식의 이름을 지정할 뿐입니다.
* EXTRACT: 이 키워드를 사용하면 읽기에 대한 스키마를 정의할 수 있습니다. 스키마는 열마다 열 이름 및 C# 형식 이름의 쌍으로 지정됩니다. 이 스키마는 추출기(예: Extractors.Tsv())라는 것을 사용하여 .tsv 파일을 추출합니다. 사용자 지정 추출기를 개발할 수 있습니다.
* OUTPUT: 이 키워드는 행 집합을 직렬화합니다. Outputters.Csv()는 콤마로 분리된 파일을 지정된 위치에 씁니다. 사용자 지정 출력기를 개발할 수도 있습니다.

 >[!NOTE]
 >두 경로는 상대 경로입니다. 절대 경로를 사용할 수도 있습니다. 예:    
 >     adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >연결된 저장소 계정의 파일에 액세스하려면 절대 경로를 사용해야 합니다.  연결된 Azure Storage 계정에 저장된 파일의 구문은 wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv입니다.

 >[!NOTE]
 >공용 Blob 또는 공용 컨테이너 액세스 권한이 있는 Azure Blob Storage 컨테이너는 현재 지원되지 않습니다.

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
        TO "/output/SearchLog-transform-datatime.csv"
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

U-SQL ORDER BY 절은 SELECT 식의 FETCH 절과 결합되어야 합니다.

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

## <a name="join-data"></a>데이터 조인
U-SQL은 테이블뿐만 아니라 행 집합(파일로부터 생성된 경우라도)도 조인할 수 있도록, INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN과 같은 일반적인 조인 연산자를 제공합니다.

다음 스크립트는 searchlog와 광고 느낌 로그를 조인하고 주어진 날짜에 쿼리 문자열의 광고를 제공합니다.

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


U-SQL은 ANSI 규격 조인 구문(Rowset1 JOIN Rowset2 ON 조건자)만 지원합니다. FROM Rowset1, Rowset2 WHERE 조건자의 이전 구문은 지원되지 _않습니다_.
JOIN에 포함된 조건자는 동등 조인이고 식이 없어야 합니다. 식을 사용하는 경우 이전 행 집합의 select 절에 추가합니다. 다른 비교를 하려면 식을 WHERE 절로 이동합니다.

## <a name="create-databases-table-valued-functions-views-and-tables"></a>데이터베이스, 테이블 반환 함수, 뷰, 테이블 만들기
U-SQL에서는 데이터베이스 및 스키마의 컨텍스트에서 데이터를 사용할 수 있으며, 항상 파일에 대한 읽기 또는 쓰기를 수행할 필요가 없습니다.

모든 U-SQL 스크립트는 기본 데이터베이스(master) 및 기본 스키마(DBO)를 기본 컨텍스트로 실행합니다 사용자 고유의 데이터베이스 또는 스키마를 만들 수 있습니다. 컨텍스트를 변경하려면 USE 문을 사용합니다.

### <a name="create-a-tvf"></a>TVF 만들기
이전 U-SQL 스크립트에서는 동일한 원본 파일에서 읽어 오는 EXTRACT 사용을 반복했습니다. U-SQL TVF(테이블 반환 함수)를 사용하면 데이터를 나중에 다시 사용할 수 있게 캡슐화할 수 있습니다.  

다음 스크립트는 기본 데이터베이스 및 스키마에 *Searchlog()* 라는 TVF를 만듭니다.

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
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
    RETURN;
    END;

다음 스크립트는 이전 스크립트에서 정의된 TVF를 사용하는 방법을 보여줍니다.

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>뷰 만들기
추상화하려는 쿼리 식이 하나뿐이고 식에서 매개 변수를 만들지 않으려면 테이블 반환 함수 대신 뷰를 만들 수 있습니다.

다음 스크립트는 기본 데이터베이스 및 스키마에 *SearchlogView* 라는 뷰를 만듭니다.

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

다음 스크립트는 정의된 뷰를 사용함을 나타냅니다.

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>테이블 만들기
관계형 데이터베이스 테이블과 마찬가지로, U-SQL을 사용하여 미리 정의된 스키마가 포함된 테이블을 만들거나 테이블을 채운 쿼리(CREATE TABLE AS SELECT 또는 CTAS)에서 스키마를 유추하는 테이블을 만들 수 있습니다.

다음 스크립트를 사용하여 데이터베이스 하나와 테이블 두 개를 만듭니다.

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>쿼리 테이블
데이터 파일을 쿼리하는 것과 같은 방식으로 이전 스크립트에서 만든 테이블을 쿼리할 수 있습니다. EXTRACT를 사용하여 행 집합을 만드는 대신 테이블 이름을 참조할 수 있습니다.

테이블에서 읽어 오려면 이전에 사용한 변환 스크립트를 수정합니다.

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >현재는 테이블을 만든 스크립트와 같은 스크립트의 테이블에 SELECT를 실행할 수 없습니다.

## <a name="conclusion"></a>결론
이 자습서에서는 U-SQL의 일부만을 다룹니다. 제한된 범위 때문에 이 자습서에서 설명하지 않은 U-SQL의 다른 장점이 많이 있습니다. 예를 들어 다음을 수행할 수 있습니다.

* CROSS APPLY를 사용하여 스트링, 배열, 맵의 일부를 행에 풉니다.
* 분할된 데이터 집합(파일 집합 및 분할된 테이블)을 작동합니다.
* 사용자가 C#로 정의하는 연산자(추출기, 출력기, 프로세서, 사용자 정의 집계기)를 개발합니다.
* U-SQL 창 함수를 사용합니다.
* 뷰, 테이블 반환 함수 및 저장 프로시저를 사용하여 U-SQL 코드를 관리합니다.
* 처리 노드에서 임의의 사용자 지정 코드를 실행합니다.
* Azure SQL Database에 연결하고 이 데이터베이스와 사용자의 U-SQL 및 Azure Data Lake 데이터에 대한 쿼리를 페더레이션합니다.

## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>의견 제시
* [기능 요청 제출](http://aka.ms/adlafeedback)
* [포럼에서 도움말 보기](http://aka.ms/adlaforums)
* [U-SQL에 대한 피드백 제공](http://aka.ms/usqldiscuss)



<!--HONumber=Dec16_HO4-->


