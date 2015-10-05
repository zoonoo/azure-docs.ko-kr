<properties 
	pageTitle="Power BI에서 Application Insights 데이터를 참조하세요." 
	description="Power BI를 사용하여 응용 프로그램의 성능 및 사용을 모니터링할 수 있습니다." 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>
 
# Application Insights 데이터의 Power BI 보기

[Microsoft Power BI](https://powerbi.microsoft.com/)는 여러 소스의 정보를 같이 가져올 수 있는 기능과 함께 풍부하고 다양한 시각적 개체로 데이터를 표시합니다. Application Insights에서 Power BI까지 웹 또는 장치 앱의 성능 및 사용에 대한 원격 분석 데이터를 스트리밍할 수 있습니다.

![Application Insights 사용 데이터의 Power BI 보기의 샘플](./media/app-insights-export-power-bi/010.png)

이 문서에서는 Application Insights에서 데이터를 내보내고 스트림 분석을 사용하여 Power BI로 데이터를 이동하는 방법을 보여줍니다. [스트림 분석](http://azure.microsoft.com/services/stream-analytics/)은 어댑터로 사용할 Azure 서비스입니다.

![Application Insights 사용 데이터의 Power BI 보기의 샘플](./media/app-insights-export-power-bi/020.png)


> [AZURE.NOTE]스트림 분석에서 Power BI로 데이터를 보내려면 회사 또는 학교 계정(MSDN 조직 계정)이 필요합니다.

## 비디오

Noam Ben Zeev는 이 기사에서 설명한 내용을 보여줍니다.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Application Insights를 사용한 앱 모니터링

아직 시도하지 않은 경우 지금이 시작 시간입니다. Application Insights는 Windows, iOS, Android, J2EE 등과 같은 광범위한 플랫폼에서 모든 장치 또는 웹앱을 모니터링할 수 있습니다. [시작](app-insights-get-started.md)

## Azure에서 저장소 만들기

연속 내보내기는 항상 Azure 저장소 계정에 데이터를 출력하므로 저장소를 먼저 만들어야 합니다.

1. [Azure 포털](https://portal.azure.com)에서 구독에 “클래식" 저장소 계정을 만듭니다.

    ![Azure 포털에서 새로 만들기, 데이터, 저장소 선택](./media/app-insights-export-power-bi/030.png)

2. 컨테이너 만들기

    ![새 저장소에서 컨테이너를 선택하고 컨테이너 타일, 추가를 차례로 클릭합니다.](./media/app-insights-export-power-bi/040.png)

3. 저장소 액세스 키 복사

    스트림 분석 서비스에 대한 입력을 설정하려면 곧 이 키가 필요합니다.

    ![저장소에서 설정, 키를 열고 기본 액세스 키 복사](./media/app-insights-export-power-bi/045.png)

## Azure 저장소로 연속 내보내기 시작

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

## Azure 스트림 분석 인스턴스 만들기

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


날짜 형식을 YYYY-MM-DD(파선 포함)로 설정해야 합니다.

전위 패턴은 스트림 분석이 저장소에서 입력 파일을 찾는 위치를 지정합니다. 연속 내보내기에서 데이터를 저장하는 방법과 일치하도록 설정해야 합니다. 다음과 같이 설정합니다.

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

이 예제에서:

* `webapplication27`은 Application Insights 리소스의 이름으로, **모두 소문자**입니다.
* `1234...`는 **대시를 생략한** Application Insights 리소스의 계측 키입니다. 
* `PageViews`는 분석하려는 데이터의 형식입니다. 사용 가능한 형식은 연속 내보내기에 설정한 필터에 따라 다릅니다. 내보낸 데이터를 검사하여 사용 가능한 다른 형식을 확인하고 [데이터 모델 내보내기](app-insights-export-data-model.md)를 참조합니다.
* `/{date}/{time}`은 문자로 기록된 패턴입니다.

> [AZURE.NOTE]저장소를 검사하여 올바른 경로를 가져오는지 확인합니다.

#### 초기 설치 완료

직렬화 형식을 확인합니다.

![마법사 확인 후 닫기](./media/app-insights-export-power-bi/150.png)

마법사를 닫고 설치가 완료될 때까지 기다립니다.

> [AZURE.TIP]샘플 명령을 사용하여 일부 데이터를 다운로드합니다. 쿼리를 디버그할 테스트 샘플로 보관합니다.

## 출력 설정

이제 사용자의 작업을 선택하고 출력을 설정합니다.

![새 채널을 선택하고, 출력, 추가, Power BI를 클릭합니다.](./media/app-insights-export-power-bi/160.png)

**회사 또는 학교 계정**을 제공하여 스트림 분석에 Power BI 리소스에 대한 액세스 권한을 부여합니다. 그런 다음 출력 및 대상 Power BI 데이터 집합과 테이블의 이름을 지정합니다.

![이름 3개를 생성합니다.](./media/app-insights-export-power-bi/170.png)

## 쿼리 설정

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
* 이벤트 이름은 중첩된 JSON 배열에 있으므로 [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx)를 사용합니다. 그런 다음 Select는 기간의 해당 이름이 있는 인스턴스의 수의 개수와 함께 이벤트 이름을 선택합니다. [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 절은 요소를 1분 기간으로 그룹화합니다.


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



## 작업 실행

작업을 시작할 과거의 날짜를 선택할 수 있습니다.

![작업을 선택하고 쿼리를 클릭합니다. 아래 샘플을 붙여넣습니다.](./media/app-insights-export-power-bi/190.png)

작업이 실행 중인 동안 기다립니다.

## Power BI에 결과를 참조하세요.

회사 또는 학교 계정을 사용하여 Power BI를 열고 스트림 분석 작업의 출력으로 정의된 데이터 집합 및 테이블을 선택합니다.

![Power BI에서 데이터 집합과 필드를 선택합니다.](./media/app-insights-export-power-bi/200.png)

이제 [Power BI](https://powerbi.microsoft.com)의 보고서 및 대시보드에서 이 데이터 집합을 사용할 수 있습니다.


![Power BI에서 데이터 집합과 필드를 선택합니다.](./media/app-insights-export-power-bi/210.png)

## 비디오

Noam Ben Zeev는 Power BI를 내보내는 방법을 보여줍니다.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## 관련된 자료

* [연속 내보내기](app-insights-export-telemetry.md)
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [추가 샘플 및 연습](app-insights-code-samples.md)

<!---HONumber=Sept15_HO4-->