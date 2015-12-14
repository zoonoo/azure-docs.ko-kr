<properties 
   pageTitle="Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발 | Azure" 
   description="Visual Studio용 데이터 레이크 도구를 설치하는 방법과 U-SQL 스크립트를 개발하고 테스트하는 방법을 알아봅니다." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/30/2015"
   ms.author="jgao"/>

# 자습서: Azure 데이터 레이크 분석 U-SQL 언어 시작

U-SQL 스크립트를 개발하는 방법에 대해 알아봅니다.

U-SQL은 모든 규모의 데이터를 처리하도록 SQL의 장점을 사용자 코드의 표현력과 결합하는 언어입니다. U-SQL의 확장 가능한 분산 쿼리 기능을 통해 저장소 및 Azure SQL 데이터베이스와 같은 관계형 저장소의 데이터를 효율적으로 분석할 수 있습니다. 이를 통해, 읽기에 스키마를 적용하여 구조화되지 않은 데이터를 처리하고, 사용자 지정 논리 및 UDF를 삽입하고, 대규모 실행 방법을 정교하게 세분화하여 제어할 수 있도록 확장성을 포함할 수 있습니다. U-SQL의 디자인 원리에 대해 자세히 알아보려면 [Visual Studio 블로그 게시물](http://blogs.msdn.com/b/visualstudio/archive/2015/09/28/introducing-u-sql.aspx)을 참조하세요.

ANSI SQL 또는 T-SQL과 일부 다른 점이 있습니다. 예를 들어, 키워드(예: SELECT)는 대문자여야 합니다.

select 절 내부의 식 언어 및 형식 시스템이며 조건자 등이 C#으로 작성됩니다. 다시 말해, 데이터 형식은 C# 형식이고, 데이터 형식에 C# NULL 의미 체계가 사용되며, 조건부 안의 비교 연산은 C# 구문(예: a == "foo")을 따릅니다. 또한 값 전체가 .NET 개체이기 때문에, 개체(예: "f o o o".Split(' ') )에 작동하는 모든 메서드를 쉽게 사용할 수 있습니다.

자세한 내용은 [U-SQL 참조](http://go.microsoft.com/fwlink/p/?LinkId=691348)를 참조하세요.

**필수 구성 요소**

- **Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual C++가 설치된 Visual Studio 2012** 
- **.NET 버전 2.7 이상용 Microsoft Azure SDK**. [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
- **[Visual Studio용 데이터 레이크 도구](http://aka.ms/adltoolsvs)** 
	
	Visual Studio용 데이터 레이크 도구를 설치하면 Visual Studio에서 **데이터 레이크** 메뉴를 볼 수 있습니다.
	
	![U-SQL Visual Studio 메뉴](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **데이터 레이크 분석 및 Visual Studio용 데이터 레이크 도구에 대한 기본 지식**. 시작하려면 다음을 참조하십시오.
 
	- [Azure 포털을 사용하여 Azure 데이터 레이크 분석을 시작합니다](data-lake-analytics-get-started-portal.md).
	- [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md).

- **데이터 레이크 분석 계정**. [Azure 데이터 레이크(ADL) 분석 계정 만들기](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)를 참조하세요.
- **데이터 레이크 분석 계정에 샘플 데이터를 업로드합니다.** [기본 데이터 레이크 저장소 계정에 SearchLog.tsv를 업로드하기](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)를 참조하세요.

	데이터 레이크 도구는 데이터 레이크 분석 계정 만들기를 지원하지 않습니다. 따라서 Azure 포털, Azure PowerShell, .NET SDK 또는 Azure CLI를 사용하여 해당 계정을 만들어야 합니다. 데이터 레이크 분석 작업을 실행하려면 일부 데이터가 필요합니다. 데이터 레이크 도구가 데이터 업로드를 지원하지만 이 자습서를 더 쉽게 수행하기 위해 해당 포털을 사용하여 샘플 데이터를 업로드합니다.

## Visual Studio에서 Azure에 연결합니다.

U-SQL 스크립트를 빌드하거나 테스트하려면 그 전에 우선 Azure에 연결해야 합니다.

**데이터 레이크 분석에 연결하기**

1. Visual Studio를 엽니다.
2. **데이터 레이크** 메뉴에서 **옵션 및 설정**을 클릭합니다.
4. **로그인**을 클릭하거나, 다른 사람이 로그인한 경우에는 **사용자 변경**을 클릭하고 지침에 따라 로그인합니다.
5. **확인**을 클릭하여 옵션 및 설정 대화 상자를 닫습니다.

**데이터 레이크 분석 계정 찾아보기**

1. Visual Studio에서 **CTRL+ALT+S**를 눌러 **서버 탐색기**를 엽니다.
2. **서버 탐색기**에서 **Azure**를 확장한 후 **데이터 레이크 분석**을 확장합니다. 계정이 있을 경우 해당 데이터 레이크 분석 계정 목록이 표시됩니다. Studio에서 데이터 레이크 분석 계정을 만들 수 없습니다. 계정을 만들려면 [Azure 포털을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md) 또는 [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-get-started-powershell.md)을 참조하세요.


## 첫 번째 U-SQL 스크립트 개발 

이 섹션의 주요 목적은 Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 응용 프로그램을 작성하고 테스트하는 과정을 이해하는 것입니다. U-SQL 문은 다른 데이터 레이크 분석 자습서에서도 사용됩니다. 탭으로 구분된(tsv) 파일을 읽고 콤마로 구분된(csv) 파일을 출력합니다.
 
**데이터 레이크 분석 작업 만들기 및 제출하기**

1. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
2. U-SQL 프로젝트 형식을 선택합니다.

	![새 U-SQL Visual Studio 프로젝트](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
	
3. **확인**을 클릭합니다. Visual studio는 Script.usql 파일로 솔루션을 만듭니다.
4. Script.usql 파일에 다음 스크립트를 입력합니다.

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

		The next section in this article will explain the script in details.  You just need to focus on understanding the development and testing process in this section.
5. **제출** 단추 옆에서 분석 계정을 지정합니다.
5. **솔루션 탐색기**에서 **Script.usql**을 마우스 오른쪽 단추로 클릭하고 **스크립트 빌드**를 클릭합니다. 출력 창에서 결과를 확인합니다. 스크립트에 오류가 있으면 이 곳에 오류 출력이 표시됩니다. 
6. **솔루션 탐색기**에서 **Script.usql**을 마우스 오른쪽 단추로 클릭하고 **스크립트 제출**을 클릭합니다.
7. **분석 계정**을 확인하고 **제출**을 클릭합니다. 제출이 완료되면 Visual Studio용 데이터 레이크 도구 결과 창에서 제출 결과 및 작업 링크를 사용할 수 있습니다.
8. 최신 작업 상태를 보고 화면을 새로 고치려면 **새로 고침** 단추를 클릭합니다. 작업이 성공적으로 완료될 때까지 기다립니다. 작업이 실패한 경우 대부분 원본 파일이 손실되었을 가능성이 큽니다. 도구의 “오류” 탭을 사용하여 서비스로부터 반환된 오류를 볼 수 있습니다. 이 자습서의 필수 조건 섹션을 참조하세요. 추가적인 문제 해결 정보는 [Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)을 참조하세요.

	
**작업 출력 보기**

1. **서버 탐색기**에서 **Azure**, **데이터 레이크 분석**, 사용자의 데이터 레이크 분석 계정, **저장소 계정**을 차례로 확장하고 기본 데이터 레이크 저장소 계정을 마우스 오른쪽 단추로 클릭한 다음 **탐색기**를 클릭합니다. 
2.  **output**을 두 번 클릭하여 폴더를 엽니다.
3.  **SearchLog-frist-u-sql.csv**를 두 번 클릭합니다.
4.  출력 파일로 바로 이동하려면 작업 그래프 보기에서 출력 파일을 두 번 클릭합니다.

## 첫 번째 U-SQL 스크립트 이해

지난 섹션에서 작성한 스크립트를 자세히 살펴봅니다.

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
            TO "/output/SearchLog-first-usql.csv"
        USING Outputters.Csv();

이 스크립트에는 변환 단계가 없습니다. **SearchLog.tsv**라는 원본 파일을 읽어와서 스키마를 만들고, 행 집합을 출력하여 **SearchLog-from-adltools.csv** 파일에 넣습니다.

Duration 필드의 데이터 형식 옆에 있는 물음표에 유의합니다. Duration 필드가 null이어도 된다는 것을 의미합니다.

이 스크립트에 사용된 일부 개념 및 키워드:

- **Rowset 변수**: 행 집합을 생성하는 각 쿼리 식은 변수에 할당될 수 있습니다. U-SQL은 T-SQL 변수 이름 지정 패턴(예: 스크립트의 **@searchlog**)을 따릅니다. 할당은 실행을 강제 적용하지 않습니다. 다만 식의 이름을 지정하고 더 복잡한 식을 작성할 수 있도록 합니다.
- **EXTRACT**를 통해 읽기에 스키마를 정의할 수 있습니다. 스키마는 열마다 열 이름 및 C# 형식 이름의 쌍으로 지정됩니다. **Extractor**를 사용합니다. 예를 들어 tsv 파일을 추출하려면 **Extractors.Tsv()**를 사용합니다. 사용자 지정 추출기를 개발할 수 있습니다.
- **OUTPUT**은 행 집합을 직렬화합니다. Outputters.Csv()는 콤마로 분리된 파일을 지정된 위치에 출력합니다. 사용자 지정 출력기를 개발할 수도 있습니다.
- 두 경로는 상대 경로입니다. 절대 경로를 사용할 수도 있습니다. 예를 들면 다음과 같습니다. 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    연결된 저장소 계정의 파일에 액세스하려면 절대 경로를 사용해야 합니다. 연결된 Azure 저장소 계정에 저장된 파일에 대한 구문은 다음과 같습니다.
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]공용 Blob 또는 공용 컨테이너 액세스 권한이 있는 Azure Blob 컨테이너는 현재 지원되지 않습니다.

## 스칼라 변수 사용

스크립트 유지 관리를 간편하게 만들기 위해 스칼라 변수를 사용할 수 있습니다. 첫 번째 U-SQL 스크립트를 다음과 같이 작성할 수 있습니다.

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
      
##변환 행 집합

**SELECT**를 사용하여 행 집합을 변환합니다.

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

WHERE 절에는 [C# 부울 식](https://msdn.microsoft.com/library/6a71f45d.aspx)을 사용합니다. 자신의 식 및 함수에 C# 식 언어를 사용할 수 있습니다. 식 및 함수를 논리 결합(ands) 및 분리(ors)와 결합하여 더 복잡한 필터링을 수행할 수 있습니다.

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
        
두 번째 쿼리는 첫 번째 행 집합의 결과를 기반으로 작동하기 때문에 두 필터의 결합이 결과가 됩니다. 변수 이름은 재사용이 가능하며 이름에는 어휘 범위(정적 범위)가 적용됩니다.

##집계 행 집합

U-SQL에는 개발자에게 친숙한 **ORDER BY**, **GROUP BY** 및 집계가 제공됩니다.

다음 쿼리는 지역 당 총 기간을 알아내고 최장 기간 5개를 순서대로 출력합니다.

U-SQL 행 집합은 다음 쿼리를 위해 이 순서를 유지하지 않습니다. 따라서 출력 순서를 정하려면 아래와 같이 OUTPUT 문에 ORDER BY를 추가해야 합니다.

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

U-SQL HAVING 절은 HAVING 조건을 만족하는 그룹으로 출력을 제한하는데 사용될 수 있습니다.

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


## 데이터 조인

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


U-SQL은 ANSI 규격 조인 구문(Rowset1 JOIN Rowset2 ON 조건자)만 지원합니다. FROM Rowset1, Rowset2 WHERE 조건자의 이전 구문은 지원되지 않습니다. JOIN에 포함된 조건자는 동등 조인이고 식이 없어야 합니다. 식을 사용하는 경우 이전 행 집합의 select 절에 추가합니다. 다른 비교를 하려면 식을 WHERE 절로 이동합니다.

        
## 데이터베이스, 테이블 반환 함수, 뷰, 테이블 만들기

U-SQL은 데이터베이스 및 스키마 컨텍스트에서 데이터를 사용하도록 허용합니다. 따라서 항상 파일에서 읽어오거나 파일에 쓸 필요가 없습니다.

모든 U-SQL 스크립트는 기본 데이터베이스(master) 및 기본 스키마(dbo)를 기본 컨텍스트로 실행합니다 사용자 고유의 데이터베이스 및/또는 스키마를 만들 수 있습니다. 컨텍스트를 변경하려면 **USE** 문을 사용하여 컨텍스트를 변경합니다.


### 테이블 반환 함수(TVF) 만들기

이전 U-SQL 스크립트에서는 동일한 원본 파일에서 읽어 오는 EXTRACT 사용을 반복했습니다. U-SQL 테이블 반환 함수는 데이터를 나중에 다시 사용할 수 있게 캡슐화할 수 있도록 합니다.

다음 스크립트는 기본 데이터베이스 및 스키마에 *Searchlog()*라는 TVF를 만듭니다.

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
        
### 뷰 만들기

추상화하려는 쿼리 식이 하나뿐이고 식에 매개 변수를 사용지 않으려면, 테이블 반환 함수 대신 뷰를 만들 수 있습니다.

다음 스크립트는 기본 데이터베이스 및 스키마에 *SearchlogView*라는 뷰를 만듭니다.

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
    
다음 스크립트는 정의된 뷰를 사용하는 것을 나타냅니다.

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

### 테이블 만들기 

관계형 데이터베이스 테이블과 마찬가지로, U-SQL은 미리 정의된 스키마로 테이블을 만들거나 테이블을 만들고 테이블을 채운 쿼리(CREATE TABLE AS SELECT 또는 CTAS)에서 스키마를 유추하도록 허용합니다.

다음 스크립트는 데이터베이스 하나와 테이블 두 개를 만듭니다.

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
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
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### 쿼리 테이블

데이터 파일을 쿼리하는 것과 같은 방식으로 테이블(이전 스크립트에서 만든)을 쿼리할 수 있습니다. EXTRACT를 사용하여 행 집합을 만드는 대신 테이블 이름을 참조할 수 있습니다.

이전에 사용한 변환 스크립트가 테이블에서 읽어오도록 수정됩니다.

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

현재 테이블을 만든 스크립트와 같은 스크립트의 테이블에 SELECT를 실행할 수 있습니다.


##결론

이 자습서에서 다룬 내용은 U-SQL의 작은 부분에 불과합니다. 이 문서의 범위로 인해 다루지 못한 내용의 일부는 다음과 같습니다.

- CROSS APPLY를 사용하여 스트링, 배열, 맵의 일부를 행에 풉니다.
- 분할된 데이터 집합(파일 집합 및 분할된 테이블)을 작동합니다.
- 사용자가 C#으로 정의하는 연산자(추출기, 출력기, 프로세서, 사용자 정의 집계기)를 개발합니다.
- U-SQL 창 함수를 사용합니다.
- U-SQL 코드도 뷰, 테이블 반환 함수 및 저장 프로시저를 가능하게 합니다.
- 처리 노드에서 임의의 사용자 지정 코드를 실행합니다. 
- Azure SQL 데이터베이스에 연결하고 이 데이터베이스와 사용자의 U-SQL 및 Azure 데이터 레이크 데이터에 있는 쿼리를 페더레이션합니다.

## 참고 항목 

- [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## 의견 제시

- [새로운 설명서 백로그 제안](data-lake-analytics-documentation-backlog.md)
- [기능 요청 제출](http://aka.ms/adlafeedback)
- [포럼에서 도움말 보기](http://aka.ms/adlaforums)
- [U-SQL에 대한 피드백 제공](http://aka.ms/usqldiscuss)

<!---HONumber=AcomDC_1203_2015-->