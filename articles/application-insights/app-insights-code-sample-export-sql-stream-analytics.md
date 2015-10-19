<properties 
	pageTitle="연습: Application Insights에서 SQL 데이터베이스로 원격 분석 내보내기" 
	description="연속 내보내기 기능을 사용하여 Application Insights에서 원격 분석에 대한 자체 분석을 코딩합니다." 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="awills"/>
 
# 연습: 스트림 분석을 사용하여 Application Insights에서 SQL로 내보내기

이 문서에서는 [연속 내보내기][export] 및 [Azure 스트림 분석](http://azure.microsoft.com/services/stream-analytics/)을 사용하여 [Visual Studio Application Insights][start]에서 Azure SQL 데이터베이스로 원격 분석 데이터를 이동하는 방법을 보여줍니다.

연속 내보내기는 원격 분석 데이터를 JSON 형식으로 Azure 저장소로 이동합니다. Azure 스트림 분석을 사용하여 JSON 개체를 구문 분석하고 데이터베이스 테이블에 행을 만들 것입니다.

보다 일반적으로 말하자면, 연속 내보내기는 앱에서 Application Insights에 보내는 원격 분석을 자체적으로 분석하는 방법입니다. 내보낸 원격 분석을 사용하여 이 코드 샘플이 데이터 집계 및 PowerBI 가상화 그룹에 해당 데이터 게시 등의 기타 작업을 수행하도록 적용할 수 있습니다.

모니터링하려는 앱이 이미 있다고 가정하고 시작합니다.


이 예제에서는 페이지 보기 데이터를 사용하지만, 동일한 패턴을 사용자 지정 이벤트 및 예외와 같은 다른 데이터 형식으로 쉽게 확장할 수 있습니다.


## Application Insights SDK 추가

응용 프로그램을 모니터링하려면 응용 프로그램에 [Application Insights SDK를 추가][start]합니다. 다양한 플랫폼, IDE, 언어를 위한 다양한 SDK와 도우미 도구가 있습니다. 웹 페이지, Java 또는 ASP.NET 웹 서버 및 여러 종류의 모바일 장치를 모니터링할 수 있습니다. 모든 SDK는 [Application Insights 포털][portal]로 원격 분석을 보내며 여기서 강력한 분석 및 진단 도구를 사용하고 저장소로 데이터를 내보낼 수 있습니다.

시작하기:

1. [Microsoft Azure에서 계정](http://azure.microsoft.com/pricing/)을 만듭니다.
2. [Azure 포털][portal]에서 앱에 대한 새 Application Insights 리소스를 추가합니다.

    ![새로 만들기, 개발자 서비스, Application Insights 선택 후 응용 프로그램의 유형 선택](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (사용자의 앱 유형 및 구독이 다를 수 있습니다.)
3. 해당 앱 유형의 SDK를 설정하는 방법을 찾으려면 빠른 시작을 엽니다.

    ![빠른 시작을 선택하고 지침 따르기](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    앱 유형이 나열되어 있지 않으면 [시작][start] 페이지를 살펴봅니다.

4. 이 예제에서는 웹앱을 모니터링하므로 Visual Studio에서 Azure 도구를 사용하여 SDK를 설치할 수 있습니다. 여기에 Application Insights 리소스의 이름을 알려줍니다.

    ![Visual Studio 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다. 송신 원격 분석에서 새 리소스를 만들거나 기존 리소스를 사용합니다.](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. 앱을 게시하고 Application Insights 리소스에 표시되는 원격 분석 데이터를 확인합니다.


## Azure에서 저장소 만들기

연속 내보내기는 항상 Azure 저장소 계정에 데이터를 출력하므로 저장소를 먼저 만들어야 합니다.

1. [Azure 포털][portal]에서 구독에 “클래식" 저장소 계정을 만듭니다.

    ![Azure 포털에서 새로 만들기, 데이터, 저장소를 선택합니다. 클래식을 선택하고 만들기를 선택합니다. 저장소 이름을 제공합니다.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. 컨테이너 만들기

    ![새 저장소에서 컨테이너를 선택하고 컨테이너 타일, 추가를 차례로 클릭합니다.](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. 저장소 액세스 키 복사

    스트림 분석 서비스에 대한 입력을 설정하려면 곧 이 키가 필요합니다.

    ![저장소에서 설정, 키를 열고 기본 액세스 키 복사](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Azure 저장소로 연속 내보내기 시작

1. Azure 포털에서 응용 프로그램에 대해 만든 Application Insights 리소스를 찾습니다.

    ![찾아보기, Application Insights, 응용 프로그램 선택](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. 연속 내보내기를 만듭니다.

    ![설정, 연속 내보내기, 추가를 차례로 선택](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    이전에 만든 저장소 계정을 선택합니다.

    ![내보내기 대상 설정](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    보려는 이벤트 유형을 설정합니다.

    ![이벤트 유형 선택](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


3. 일부 데이터가 누적되도록 합니다. 한동안 사용자가 응용 프로그램을 사용하도록 놓아둡니다. 원격 분석이 제공되어 [메트릭 탐색기](app-insights-metrics-explorer.md)에는 통계 차트가 표시되고 [진단 검색](app-insights-diagnostic-search.md)에는 개별 이벤트가 표시됩니다.

    또한 데이터를 저장소로 내보냅니다.

4. 포털 또는 Visual Studio에서 내보낸 데이터를 검사합니다. 포털에서 **찾아보기**, 저장소 계정을 선택한 다음 **컨테이너**를 선택합니다. Visual Studio에서 **보기/클라우드 탐색기**를 선택하고 Azure/저장소를 엽니다. 이 메뉴 옵션이 없는 경우 Azure SDK를 설치해야 합니다. 새 프로젝트 대화 상자를 열고 Visual C#/클라우드/Microsoft Azure SDK for .NET 가져오기를 엽니다.

    ![Visual Studio에서 서버 브라우저, Azure, 저장소 열기](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

    응용 프로그램 이름 및 계측 키에서 파생된 경로 이름의 공통 부분을 적어 둡니다.

이벤트는 JSON 형식으로 blob 파일에 기록됩니다. 각 파일에는 하나 이상의 이벤트가 있을 수 있습니다. 따라서 이벤트 데이터를 읽고 원하는 필드를 필터링하려고 합니다. 데이터로 온갖 종류의 작업을 수행할 수 있지만, 지금은 스트림 분석을 사용하여 데이터를 SQL 데이터베이스로 이동하려고 합니다. 이렇게 하면 흥미로운 많은 쿼리를 쉽게 실행할 수 있습니다.

## Azure SQL 데이터베이스 만들기

다시 한 번 [Azure 포털][portal]의 구독에서 시작하여 데이터를 쓸 데이터베이스(그리고 아직 없는 경우 새 서버도 포함)를 만듭니다.

![새로 만들기, 데이터, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


데이터베이스 서버에서 Azure 서비스에 액세스할 수 있는지 확인합니다.


![찾아보기, 서버, 사용자 서버, 설정, 방화벽, Azure에 대한 액세스 허용](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Azure SQL DB에 테이블 만들기

기본 관리 도구로 이전 섹션에서 만든 데이터베이스에 연결합니다. 이 연습에서는 SSMS([SQL Server 관리 도구](https://msdn.microsoft.com/ms174173.aspx))를 사용합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

새 쿼리를 만들고 다음 T-SQL을 실행합니다.

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

이 샘플에서 페이지 보기에서 데이터를 사용합니다. 사용 가능한 다른 데이터를 보려면 JSON 출력을 검사하고 [데이터 모델 내보내기](app-insights-export-data-model.md)를 참조합니다.

## Azure 스트림 분석 인스턴스 만들기

[클래식 Azure 포털](https://manage.windowsazure.com/)에서 Azure 스트림 분석 서비스를 선택하고 새 스트림 분석 작업을 만듭니다.


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

새 작업이 만들어지면 해당 세부 정보를 확장합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Blob 위치 설정

연속 내보내기 Blob에서 입력을 가져오도록 설정합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

이제 앞에서 기록해 둔 저장소 계정의 기본 액세스 키가 필요합니다. 이 키를 저장소 계정 키로 설정합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### 경로 접두사 패턴 설정 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

날짜 형식을 **YYYY-MM-DD**(**대시** 포함)로 설정해야 합니다.

경로 접두사 패턴은 스트림 분석이 저장소에서 입력 파일을 찾는 방법을 지정합니다. 연속 내보내기에서 데이터를 저장하는 방법과 일치하도록 설정해야 합니다. 다음과 같이 설정합니다.

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

이 예제에서:

* `webapplication27`은 Application Insights 리소스의 이름으로, **모두 소문자**입니다. 
* `1234...`은 **대시를 제거한** Application Insights 리소스의 계측 키입니다. 
* `PageViews`는 분석하려는 데이터의 형식입니다. 사용 가능한 형식은 연속 내보내기에 설정한 필터에 따라 다릅니다. 내보낸 데이터를 검사하여 사용 가능한 다른 형식을 확인하고 [데이터 모델 내보내기](app-insights-export-data-model.md)를 참조합니다.
* `/{date}/{time}`은 문자로 기록된 패턴입니다.

Application Insights 리소스의 이름 및 iKey를 가져오려면 해당 개요 페이지에서 필수 항목을 열거나 설정을 엽니다.

#### 초기 설치 완료

직렬화 형식을 확인합니다.

![마법사 확인 후 닫기](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

마법사를 닫고 설치가 완료될 때까지 기다립니다.

>[AZURE.TIP]샘플 함수를 사용하여 입력 경로가 올바르게 설정되었는지 확인합니다. 실패한 경우 선택한 샘플 시간 범위에 대한 저장소에 데이터가 있는지 확인합니다. 입력 정의를 편집하고 저장소 계정, 경로 접두사 및 날짜 형식이 올바르게 설정되었는지 확인합니다.

## 쿼리 설정

쿼리 섹션을 엽니다.

![스트림 분석에서 쿼리 선택](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

기본 쿼리를 다음으로 바꿉니다.

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

처음 몇 가지 속성은 페이지 보기 데이터에만 해당됩니다. 다른 원격 분석 유형 내보내기에 다른 속성이 있습니다. [속성 형식 및 값에 대한 자세한 데이터 모델 참조](app-insights-export-data-model.md)를 참조하세요.

## 데이터베이스에 출력 설정

SQL을 출력으로 선택합니다.

![스트림 분석에서 출력 선택](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

SQL 데이터베이스를 지정합니다.


![데이터베이스의 세부 정보 채우기](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

마법사를 닫고 출력이 설정되었다는 알림이 표시될 때까지 기다립니다.

## 처리 시작

작업 모음에서 작업을 시작합니다.

![스트림 분석에서 시작 클릭](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

지금부터의 데이터를 처리할 것인지 아니면 이전 데이터부터 처리할 것인지를 선택할 수 있습니다. 후자는 연속 내보내기를 이미 한동안 실행한 경우 유용합니다.


![스트림 분석에서 시작 클릭](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

몇 분 후에 SQL Server 관리 도구로 돌아가서 데이터 흐름을 확인합니다. 예를 들어 다음과 같은 쿼리를 사용합니다.

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## 관련된 문서

* [작업자 역할을 사용하여 SQL로 내보내기](app-insights-code-sample-export-telemetry-sql-database.md)
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](app-insights-export-data-model.md)
* [Application Insights에서 연속 내보내기](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=Oct15_HO2-->