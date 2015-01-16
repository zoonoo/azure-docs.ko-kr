<properties title="Application Insights" pageTitle="Application Insights - 앱의 상태 및 사용 현황 모니터링 시작" description="Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights - 앱의 상태 및 사용 현황 모니터링 시작

*Application Insights는 미리 보기 상태입니다.*

Application Insights에서는 라이브 응용 프로그램에 대한 다음 항목을 모니터링할 수 있습니다.

* **가용성** - 전 세계 어디든지 사용자의 URL을 몇 분마다 테스트합니다.
* **성능**   - 성능 문제와 예외를 검색하고 진단합니다.
* **사용 현황** - 앱을 보다 효율적으로 개선하기 위해 사용자가 앱을 통해 수행하는 작업을 확인합니다.

대체 방법에 대해서는 [Application Insights 시작][start]을 읽어보세요.

## <a name="add"></a>프로젝트에 Application Insights 추가

[Visual Studio 2013 업데이트 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) 이상 및 [Microsoft Azure](http://azure.com) 계정이 필요합니다.

### 새 프로젝트의 경우

Visual Studio 2013에서 새 프로젝트를 만들 때 Application Insights를 선택합니다. 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

프로젝트를 처음 만드는 경우 Microsoft Azure 미리 보기에 로그인하거나 등록하라는 메시지가 표시됩니다. 해당 계정은 Visual Studio Online 계정과는 별개입니다.

Azure 리소스의 이름을 프로젝트의 이름과 다르게 지정하거나 리소스를 같은 그룹에서 다른 프로젝트로 표시하려면 **설정 구성**을 사용합니다. 

*Application Insights 옵션이 표시되지 않는 경우 Visual Studio 2013 업데이트 3을 사용 중이고, 확장 및 업데이트에서 Application Insights Tools를 사용하도록 설정되어 있으며, 웹/Windows 스토어 또는 Windows Phone 프로젝트를 만들고 있는지 확인합니다.*

### ...기존 프로젝트의 경우

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*웹 사용 현황 분석을 설정하려면 한 단계를 추가로 수행해야 합니다. 여기서는 먼저 데이터부터 확인하겠습니다.*


### <a name="run"></a>2. 프로젝트 실행

F5 키를 눌러 응용 프로그램을 실행하고 여러 페이지를 열어 봅니다.

Visual Studio에 수신된 이벤트의 수가 표시됩니다.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. 모니터 데이터 확인

프로젝트에서 Application Insights를 엽니다.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


**응용 프로그램 상태** 타일에서 데이터를 찾습니다. 처음에는 요소가 1~2개만 표시됩니다. 예를 들면 다음과 같습니다.

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

보다 자세한 정보를 확인하려면 원하는 타일을 클릭합니다. 보고서에 표시되는 항목을 변경할 수 있습니다. [응용 프로그램 상태 보고서 구성 방법에 대해 자세히 알아보세요.][perf]


### <a name="deploy"></a>4. 응용 프로그램 배포

응용 프로그램을 배포하고 누적되는 데이터를 관찰합니다.



라이브 응용 프로그램을 만든 후에는 [웹 테스트를 설정][availability]하여 응용 프로그램이 라이브 상태로 유지되는지 확인합니다. 

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### 포털에서 응용 프로그램의 이름 변경

프로젝트에서 원격 분석을 보내는 리소스를 변경할 수 있습니다. 

여기서 '리소스'는 결과가 표시되는 명명된 Application Insights 블레이드입니다. 단일 비즈니스 응용 프로그램 부분을 구성하는 프로젝트가 여러 개인 경우 등에는 그룹 하나에 여러 리소스를 포함할 수 있습니다. 

솔루션 탐색기에서 ApplicationInsights.config를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. 그러면 열리는 마법사에서 다른 기존 리소스를 선택하거나 새 리소스를 만들 수 있습니다.

나중에 포털로 돌아가서 이전 리소스를 삭제합니다.

## <a name="video"></a>비디오

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>다음 단계

[웹 앱 사용 현황 추적][usage]

[웹 응용 프로그램의 성능 모니터링][perf]

[진단 로그 캡처 및 검색][diagnostic]

[문제 해결][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


