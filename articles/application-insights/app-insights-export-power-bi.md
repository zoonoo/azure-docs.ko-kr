<properties 
	pageTitle="Application Insights에서 Power BI로 내보내기" 
	description="문서" 
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
	ms.date="04/05/2016" 
	ms.author="awills"/>

# Application Insights에서 Power BI 공급

[Power BI](http://www.powerbi.com/)는 데이터를 분석하고 통찰력을 공유하는 비즈니스 분석 도구 제품군입니다. 모든 장치에서 풍부한 대시보드를 사용할 수 있습니다. [Visual Studio Application Insights](app-insights-overview.md)를 포함하여 다양한 원본의 데이터를 포함할 수 있습니다.

시작하려면 [Power BI에서 Application Insights 데이터 표시](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)를 참조하세요.

Application Insights 차트를 다른 원본의 차트와 결합하여 사용자 지정 가능한 초기 대시보드를 가져올 수 있습니다. 추가 차트를 가져올 수 있는 시각화 갤러리가 있으며, 각 차트에는 설정할 수 있는 매개 변수가 있습니다.

![](./media/app-insights-export-power-bi/010.png)


초기 가져오기 후에는 대시보드와 보고서가 지속적으로 매일 업데이트됩니다. 데이터 집합에 대한 새로 고침 일정을 제어할 수 있습니다.


**샘플링** 응용 프로그램이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에 대해 Application Insights SDK를 사용하는 경우 적응 샘플링 기능이 작동하여 원격 분석의 백분율만 보낼 수 있습니다. 이는 SDK 또는 수집에서 샘플링을 수동으로 설정한 경우에도 마찬가지입니다. [샘플링에 대해 자세히 알아봅니다.](app-insights-sampling.md)

## Application Insights 데이터를 확인하는 다른 방법

* Azure가 아닌 데이터를 표시할 필요가 없는 경우 [Application Insights 차트가 포함된 Azure 대시보드](app-insights-dashboards.md)가 더 적합할 수 있습니다. 예를 들어 일부 Azure 서비스 모니터와 함께 시스템의 여러 구성 요소를 모니터링하는 Application Insights 차트의 대시보드를 설정하려는 경우 Azure 대시보드를 사용하는 것이 가장 좋습니다. 이 대시보드는 기본적으로 더 자주 업데이트됩니다. 
* [연속 내보내기](app-insights-export-telemetry.md)는 들어오는 데이터를 Azure 저장소에 복사합니다. 여기서 원하는 데이터를 이동하고 처리할 수 있습니다.
* [분석](app-insights-analytics.md)을 통해 Application Insights에 의해 보존된 원시 데이터에서 복잡한 쿼리를 수행할 수 있습니다.


## 스트림 분석을 사용하여 사용자 고유의 Power BI 어댑터 만들기

Application Insights용 Power BI 콘텐츠 팩은 사용자 요구 사항에 충분한 앱 원격 분석의 유용한 하위 집합을 표시합니다. 그러나 제공되는 것보다 더 광범위한 원격 분석이 필요하거나 원시 원격 분석에서 일부 데이터를 사용하려는 경우 Azure 스트림 분석 서비스를 사용하여 사용자 고유의 어댑터를 만들 수 있습니다.

이 스키마에서는 Application Insights에서 Azure 저장소로 데이터를 내보냅니다. [스트림 분석](https://azure.microsoft.com/services/stream-analytics/)은 여기에서 데이터를 가져와 일부 필드의 이름을 바꾸고 처리하며 이를 Power BI에 파이프합니다. 스트림 분석은 데이터의 연속 스트림을 필터링, 집계 및 계산할 수 있는 서비스입니다.

![SA를 통해 PBI로 내보내기에 대한 블록 다이어그램](./media/app-insights-export-power-bi/020.png)


>[AZURE.TIP] Power BI에서 Application Insights 데이터를 확인하기 위해 **이 문서의 나머지 부분에 있는 절차(스트림 분석 사용)를 따르지 않아도 됩니다**. 훨씬 더 쉬운 방법이 있습니다! 대신 [무료 어댑터를 사용](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)할 수 있습니다. 해당 어댑터에서 원하는 모든 데이터를 제공하지 않거나, 데이터에 대한 사용자 고유의 집계 또는 함수를 정의하려는 경우에만 이 문서의 나머지 부분을 따르세요.

### Azure에서 저장소 만들기

연속 내보내기는 항상 Azure 저장소 계정에 데이터를 출력하므로 저장소를 먼저 만들어야 합니다.

1. [Application Insights용 Power BI PowerPack을 사용해 보셨나요](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)? 요구 사항에 충분한 경우 이 문서의 나머지 부분은 필요 없습니다.

2.  [Azure 포털](https://portal.azure.com)에서 구독에 "클래식" 저장소 계정을 만듭니다.

    ![Azure 포털에서 새로 만들기, 데이터, 저장소 선택](./media/app-insights-export-power-bi/030.png)

2. 컨테이너 만들기

    ![새 저장소에서 컨테이너를 선택하고 컨테이너 타일, 추가를 차례로 클릭합니다.](./media/app-insights-export-power-bi/040.png)

3. 저장소 액세스 키 복사

    스트림 분석 서비스에 대한 입력을 설정하려면 곧 이 키가 필요합니다.

    ![저장소에서 설정, 키를 열고 기본 액세스 키 복사](./media/app-insights-export-power-bi/045.png)

### Azure 저장소로 연속 내보내기 시작

[연속 내보내기](app-insights-export-telemetry.md)는 Application Insights에서 Azure 저장소로 데이터를 이동합니다.

1. Azure 포털에서 응용 프로그램에 대해 만든 Application Insights 리소스를 찾습니다.

    ![찾아보기, Application Insights, 응용 프로그램 선택](./media/app-insights-export-power-bi/050.png)

2. 연속 내보내기를 만듭니다.

    ![설정, 연속 내보내기, 추가를 차례로 선택](./media/app-insights-export-power-bi/060.png)


    이전에 만든 저장소 계정을 선택합니다.

    ![내보내기 대상 설정](./media/app-insights-export-power-bi/070.png)
    
    보려는 이벤트 유형을 설정합니다.

    ![이벤트 유형 선택](./media/app-insights-export-power-bi/080.png)

3. 일부 데이터가 누적되도록 합니다. 한동안 사용자가 응용 프로그램을 사용하도록 놓아둡니다. 원격 분석이 제공되어 [메트릭 탐색기](app-insights-metrics-explorer.md)에는 통계 차트가 표시되고 [진단 검색](app-insights-diagnostic-search.md)에는 개별 이벤트가 표시됩니다.

    또한 데이터를 저장소로 내보냅니다.

4. 내보낸 데이터를 검사합니다. Visual Studio에서 **보기/클라우드 탐색기**를 선택하고 Azure/저장소를 엽니다. 이 메뉴 옵션이 없는 경우 Azure SDK를 설치해야 합니다. 새 프로젝트 대화 상자를 열고 Visual C#/클라우드/Microsoft Azure SDK for .NET 가져오기를 엽니다.

    ![](./media/app-insights-export-power-bi/04-data.png)

    응용 프로그램 이름 및 계측 키에서 파생된 경로 이름의 공통 부분을 적어 둡니다.

이벤트는 JSON 형식으로 blob 파일에 기록됩니다. 각 파일에는 하나 이상의 이벤트가 있을 수 있습니다. 따라서 이벤트 데이터를 읽고 원하는 필드를 필터링하려고 합니다. 데이터로 온갖 종류의 작업을 수행할 수 있지만, 지금은 스트림 분석을 사용하여 데이터를 Power BI로 파이프하려고 합니다.

### Azure 스트림 분석 인스턴스 만들기

[클래식 Azure 포털](https://manage.windowsazure.com/)에서 Azure 스트림 분석 서비스를 선택하고 새 스트림 분석 작업을 만듭니다.


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

새 작업이 만들어지면 해당 세부 정보를 확장합니다.

![](./media/app-insights-export-power-bi/110.png)


#### Blob 위치 설정

연속 내보내기 Blob에서 입력을 가져오도록 설정합니다.

![](./media/app-insights-export-power-bi/120.png)

이제 앞에서 기록해 둔 저장소 계정의 기본 액세스 키가 필요합니다. 이 키를 저장소 계정 키로 설정합니다.

![](./media/app-insights-export-power-bi/130.png)

#### 경로 접두사 패턴 설정 

![](./media/app-insights-export-power-bi/140.png)


**날짜 형식을 YYYY-MM-DD(파선 포함)로 설정해야 합니다.**

전위 패턴은 스트림 분석이 저장소에서 입력 파일을 찾는 위치를 지정합니다. 연속 내보내기에서 데이터를 저장하는 방법과 일치하도록 설정해야 합니다. 다음과 같이 설정합니다.

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

이 예제에서:

* `webapplication27`은 Application Insights 리소스의 이름으로, **모두 소문자**입니다.
* `1234...`는 **대시를 생략한** Application Insights 리소스의 계측 키입니다. 
* `PageViews`는 분석하려는 데이터의 형식입니다. 사용 가능한 형식은 연속 내보내기에 설정한 필터에 따라 다릅니다. 내보낸 데이터를 검사하여 사용 가능한 다른 형식을 확인하고 [데이터 모델 내보내기](app-insights-export-data-model.md)를 참조합니다.
* `/{date}/{time}`은 문자로 기록된 패턴입니다.

> [AZURE.NOTE] 저장소를 검사하여 올바른 경로를 가져오는지 확인합니다.

#### 초기 설치 완료

직렬화 형식을 확인합니다.

![마법사 확인 후 닫기](./media/app-insights-export-power-bi/150.png)

마법사를 닫고 설치가 완료될 때까지 기다립니다.

> [AZURE.TIP] 샘플 명령을 사용하여 일부 데이터를 다운로드합니다. 쿼리를 디버그할 테스트 샘플로 보관합니다.

### 출력 설정

이제 사용자의 작업을 선택하고 출력을 설정합니다.

![새 채널을 선택하고, 출력, 추가, Power BI를 클릭합니다.](./media/app-insights-export-power-bi/160.png)

**회사 또는 학교 계정**을 제공하여 스트림 분석에 Power BI 리소스에 대한 액세스 권한을 부여합니다. 그런 다음 출력 및 대상 Power BI 데이터 집합과 테이블의 이름을 지정합니다.

![이름 3개를 생성합니다.](./media/app-insights-export-power-bi/170.png)

### 쿼리 설정

쿼리는 입력에서 출력으로 번역을 제어합니다.

![작업을 선택하고 쿼리를 클릭합니다. 아래 샘플을 붙여넣습니다.](./media/app-insights-export-power-bi/180.png)


Test 함수를 사용하여 올바른 출력이 표시되는지 확인합니다. 입력 페이지에서 얻은 샘플 데이터를 제공합니다.

#### 이벤트 수를 표시하는 쿼리

이 쿼리 붙여넣기:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* 내보내기 입력은 스트림 입력에 제공된 별칭입니다.
* pbi 출력은 정의한 출력 별칭입니다.
* 이벤트 이름은 중첩된 JSON 배열에 있으므로 [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx)를 사용합니다. 그런 다음 Select는 기간의 해당 이름이 있는 인스턴스의 수의 개수와 함께 이벤트 이름을 선택합니다. [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 절은 요소를 1분의 기간으로 그룹화합니다.


#### 메트릭 값을 표시하는 쿼리


```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* 이 쿼리는 이벤트 시간과 메트릭 값을 가져오기 위해 메트릭 원격 분석을 드릴합니다. 메트릭 값은 배열 내부에 있으므로 OUTER APPLY GetElements 패턴을 사용하여 행을 추출합니다. 이 경우 "myMetric"은 메트릭 이름입니다. 

#### 차원 속성의 값을 포함하는 쿼리

```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* 이 쿼리는 차원 배열에 고정된 인덱스의 특정 차원에 상관없이 차원 속성의 값을 포함합니다.

### 작업 실행

작업을 시작할 과거의 날짜를 선택할 수 있습니다.

![작업을 선택하고 쿼리를 클릭합니다. 아래 샘플을 붙여넣습니다.](./media/app-insights-export-power-bi/190.png)

작업이 실행 중인 동안 기다립니다.

### Power BI에 결과를 참조하세요.

회사 또는 학교 계정을 사용하여 Power BI를 열고 스트림 분석 작업의 출력으로 정의된 데이터 집합 및 테이블을 선택합니다.

![Power BI에서 데이터 집합과 필드를 선택합니다.](./media/app-insights-export-power-bi/200.png)

이제 [Power BI](https://powerbi.microsoft.com)의 보고서 및 대시보드에서 이 데이터 집합을 사용할 수 있습니다.


![Power BI에서 데이터 집합과 필드를 선택합니다.](./media/app-insights-export-power-bi/210.png)


### 데이터가 없나요?

* [날짜 형식](#set-path-prefix-pattern)을 YYYY-MM-DD(대시 사용)로 정확하게 설정했는지 확인합니다.


### 비디오

Noam Ben Zeev는 Power BI를 내보내는 방법을 보여줍니다.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## 관련된 자료

* [연속 내보내기](app-insights-export-telemetry.md)
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [추가 샘플 및 연습](app-insights-code-samples.md)
 

<!---HONumber=AcomDC_0420_2016-->