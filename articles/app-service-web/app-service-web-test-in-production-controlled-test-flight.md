<properties
	pageTitle="Azure 앱 서비스에서 Flighting 배포(베타 테스트)"
	description="종단간 자습서에서 업데이트된 앱 또는 베타 테스트의 새로운 기능을 사용하는 방법을 알아봅니다. 연속 게시, 슬롯, 트래픽 라우팅 및 Application Insights 통합과 같은 앱 서비스 기능을 함께 소개합니다."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cephalin"/>
# Azure 앱 서비스에서 Flighting 배포(베타 테스트)

이 자습서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 및 [Azure Application Insights](/services/application-insights/)의 다양한 기능을 통합하여 *Flighting 배포*를 수행하는 방법을 보여줍니다.

*Flighting*은 제한된 수의 실제 고객으로 새로운 기능 또는 변경의 유효성을 검사하는 배포 프로세스이며 프로덕션 시나리오의 주요 테스트입니다. 베타 테스트와 유사하며 때로 "통제된 테스트 비행"이라고도 합니다. 웹 서비스를 제공하는 많은 대기업이 이 방법을 사용하여 [민첩한 개발](https://en.wikipedia.org/wiki/Agile_software_development)이라는 해당 연습에서 앱 업데이트의 초기 유효성 검사를 가져옵니다. Azure 앱 서비스를 사용하면 동일한 DevOps 시나리오를 구현하기 위해 연속 게시 및 Application Insights를 사용하여 프로덕션에서 테스트를 통합할 수 있습니다. 이 방법의 이점은 다음과 같습니다.

- **업데이트가 프로덕션에 릴리스되기 _전에_ 실제 피드백 얻기** - 릴리스하는 즉시 피드백을 얻는 것보다 릴리스하기 전에 피드백을 얻는 것이 좋습니다. 제품 수명 주기에서 원하는 만큼 빠르게 실제 사용자 트래픽 및 동작을 사용하여 업데이트를 테스트할 수 있습니다.
- **[연속 테스트 기반 개발(CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development) 향상** - Application Insights를 사용하여 연속 통합 및 계측으로 프로덕션에서 테스트를 통합하여 사용자 유효성 검사가 제품 수명 주기에서 초기에 자동으로 발생합니다. 수동 테스트 실행에 시간 투자를 줄일 수 있습니다.
- **테스트 워크플로 최적화** -연속 모니터링 계측으로 프로덕션에서 테스트를 자동화하여 단일 프로세스에서 [통합](https://en.wikipedia.org/wiki/Integration_testing), [회귀](https://en.wikipedia.org/wiki/Regression_testing), [유용성](https://en.wikipedia.org/wiki/Usability_testing), 접근성, 지역화, [성능](https://en.wikipedia.org/wiki/Software_performance_testing), [보안](https://en.wikipedia.org/wiki/Security_testing) 및 [수용](https://en.wikipedia.org/wiki/Acceptance_testing)과 같은 다양한 테스트의 목표를 잠재적으로 수행할 수 있습니다.

Flighting 배포는 라이브 트래픽의 라우팅에 국한되지 않습니다. 이러한 배포에서 예기치 않은 버그, 성능 저하, 사용자 경험 문제를 가리지 않고 최대한 신속하게 통찰력을 얻고자 합니다. 실제 고객을 상대한다는 점을 기억하십시오. 따라서 바르게 수행하려면 Flighting 배포를 설치하여 다음 단계에 대한 합리적 결정을 내리기 위해 필요한 모든 데이터를 수집하도록 합니다. 이 자습서는 Application Insights를 사용하여 데이터를 수집하는 방법을 보여줍니다. 하지만 New Relic 또는 시나리오에 적합한 다른 기술을 사용할 수 있습니다.

## 수행할 사항

이 자습서에서는 프로덕션에서 앱 서비스 앱을 테스트하기 위해 다음의 시나리오를 가져오는 방법을 알아봅니다.

- 베타 앱에 [프로덕션 트래픽 라우팅](app-service-web-test-in-production-get-start.md)
- 유용한 메트릭을 가져오기 위해 [앱 계측](../application-insights/app-insights-web-track-usage.md)
- 연속 베타 앱 배포 및 라이브 앱 메트릭 추적
- 프로덕션 앱 및 베타 앱 간에 메트릭을 비교하여 코드 변경이 어떻게 결과로 변환되는지 확인

## 필요한 사항

-	Azure 계정.
-	[GitHub](https://github.com/) 계정
- Visual Studio 2015 - [Community Edition](https://www.visualstudio.com/ko-KR/products/visual-studio-express-vs.aspx)을 다운로드할 수 있습니다.
-	Git 셸(설치된 [Windows용 GitHub](https://windows.github.com/)) - 이것은 동일한 세션에서 Git와 PowerShell 명령을 실행할 수 있도록 합니다.
-	최신 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) 비트
-	다음의 기본적인 이해:
	-	[Azure 리소스 관리자](../resource-group-overview.md) 템플릿 배포([Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-deploy-complex-application-predictably.md) 참조)
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 있어야 합니다.
> + [Azure 계정을 무료로 개설](/pricing/free-trial/)할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 웹앱과 같은 무료 Azure 서비스를 사용할 수 있습니다.
> + [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)할 수 있음 - Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
>
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 프로덕션 웹앱 설치

>[AZURE.NOTE] 이 자습서에서 사용되는 스크립트는 사용자의 GitHub 저장소에서 자동적으로 구성되어 지속적으로 게시됩니다. 이것은 Azure에 이미 저장되어있는 GitHub 자격 증명이 요구되며, 그렇지 않으면 웹앱에 대한 소스 제어 설정 구성을 시도할 때 스크립트 배포는 실패합니다.
>
>Azure에서 GitHub 자격 증명을 저장하려면 [Azure 포털](https://portal.azure.com/)에서 웹앱을 생성하고 [GitHub 배포를 구성](app-service-continuous-deployment.md#Step7)하세요. 이 작업은 한 번만 필요합니다.

일반적인 DevOps 시나리오에서, 사용자는 Azure에서 실시간으로 실행되는 응용 프로그램을 갖게 되며, 지속적인 게시를 통해 원하는 부분을 변경할 수 있습니다. 이 시나리오에서는 개발하고 테스트한 템플릿을 프로덕션에 배포합니다.

1.	[ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 리포지토리의 고유한 분기를 만듭니다. 분기를 만드는 방법에 대한 자세한 내용은 [리포지토리 분기](https://help.github.com/articles/fork-a-repo/)를 참조하세요. 사용자의 포크를 생성하면, 브라우저에서 볼 수 있습니다.

	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Git 셸 세션을 엽니다. Git 셸이 없는 경우 [Windows용 GitHub](https://windows.github.com/)를 설치합니다.
3.	다음 명령을 실행하여 사용자 포크의 로컬 클론을 만듭니다.

        git clone https://github.com/<your_fork>/ToDoApp.git

4.	로컬 클론을 만든 후 *&lt;repository\_root>*\\ARMTemplates로 이동하여 아래 보이는 대로 고유한 접미사가 있는 deploy.ps1 스크립트를 실행합니다.

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.	메시지가 표시되면 데이터베이스 액세스에 대한 원하는 사용자이름과 암호를 입력합니다. 리소스 그룹을 업데이트할 때 다시 지정해야 하기 때문에 데이터베이스 자격 증명을 기억하십시오.

	다양한 Azure 리소스의 프로비전 진행 상태가 표시되어야 합니다. 배포가 완료되면, 스크립트가 브라우저에서 응용 프로그램을 시작되고, 친숙한 소리가 납니다. ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.	Git 셸 세션으로 돌아가서 다음을 실행합니다.

        .\swap –Name ToDoApp<your_suffix>

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.	스크립트가 완료되면 프런트 엔드의 주소(http://ToDoApp*&lt;your_suffix>*.azurewebsites.net/)로 돌아가 프로덕션에서 응용 프로그램이 실행되는 것을 확인합니다.
5.	[Azure 포털](https://portal.azure.com/)에 로그인하고 생성된 것을 살펴보세요.

	같은 리소스 그룹에서 두 개의 웹앱이 나타나야 하며, 하나는 이름에 `Api` 접미사가 있습니다. 리소스 그룹 보기를 보면 SQL 데이터베이스 및 서버, 앱 서비스 계획 및 웹앱에 대한 스테이징 슬롯도 표시 됩니다. *&lt;repository\_root>*\\ARMTemplates\\ProdAndStage.json을 사용하여 다른 리소스를 통해 검색 및 비교하여 템플릿에서 어떻게 구성되는지 볼 수 있습니다.

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

프로덕션 앱을 설치했습니다. 이제 앱에 대한 유용성이 떨어진다는 피드백을 받았다고 가정해 보겠습니다. 따라서 조사하도록 결정합니다. 앱을 계측하여 피드백을 제공하려고 합니다.

## 조사: 모니터링/메트릭에 대한 클라이언트 앱 계측

5. Visual Studio에서 *&lt;repository\_root>*\\src\\MultiChannelToDo.sln을 엽니다.
6. 솔루션 > **솔루션에 대한 NuGet 패키지 관리** > **복원**을 마우스 오른쪽 단추로 클릭하여 모든 NuGet 패키지를 복원합니다.
6. 마우스 오른쪽 단추로 **MultiChannelToDo.Web** > **Application Insights 원격 분석 추가** > **설정 구성** > ToDoApp*&lt;your\_suffix>*에 리소스 그룹 변경 > **프로젝트에 Application Insights 추가**를 클릭합니다.
7. Azure 포털에서 **MultiChannelToDo.Web** Application Insight 리소스에 대한 블레이드를 엽니다. 그런 다음 **응용 프로그램 상태** 파트에서 **브라우저 페이지 로드 데이터를 수집하는 방법을 알아봅니다** > 코드 복사를 클릭합니다.
7. `<heading>`태그를 닫기 전에 복사한 JS 계측 코드를 *&lt;repository\_root>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml에 추가합니다. Application Insight 리소스의 고유 계측 키를 포함해야 합니다.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. 본문의 아래쪽에 다음 코드를 추가하여 사용자 지정 이벤트를 Application Insights에 보냅니다.

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    웹앱에서 사용자가 어디든 클릭할 때마다 이 JavaScript 코드 조각이 Application Insights에 사용자 지정 이벤트를 보냅니다.

12. Git 셸에서 커밋하고 GitHub의 분기에 변경 내용을 푸시합니다. 그런 다음 클라이언트가 브라우저를 새로 고칠 때까지 기다립니다.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.	프로덕션에 배포된 앱 변경 내용을 교환합니다.

        .\swap –Name ToDoApp<your_suffix>

13. 구성된 Application Insights 리소스로 이동합니다. 사용자 지정 이벤트를 클릭합니다.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    사용자 지정 이벤트에 대한 메트릭이 보이지 않으면 몇 분 기다렸다가 **새로 고침**을 클릭합니다.

아래와 같은 차트를 본다고 가정합니다.

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

그 아래의 이벤트 그리드도 보입니다.

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

ToDoApp 응용 프로그램 코드에 따라 **단추** 이벤트는 전송 단추에 해당하고 **입력** 이벤트는 텍스트 상자에 해당합니다. 지금까지는 합리적입니다. 그러나 할 일 항목에 적절한 클릭이 필요한 것처럼 보입니다.(**LI** 이벤트)

이를 기반으로 일부 사용자가 UI의 어느 부분을 클릭할 수 있는지 혼동하며 이는 커서를 목록 항목 및 해당 아이콘에 가져가면 텍스트 선택에 커서의 스타일이 지정되어 있기 때문이라고 가정하게 됩니다.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

부자연스러운 예제일 수 있습니다. 그럼에도 불구하고 앱을 향상시키고 Flighting 배포를 수행하여 라이브 고객의 유용성 피드백을 얻을 것입니다.

### 모니터링/메트릭에 대한 서버 앱 계측
이 자습서에서 설명된 시나리오가 클라이언트 앱만 다루기 때문에 탄젠트입니다. 그러나 완전성을 위해 서버 측 앱을 설정합니다.

6. 마우스 오른쪽 단추로 **MultiChannelToDo** > **Application Insights 원격 분석 추가** > **설정 구성** > ToDoApp*&lt;your\_suffix>*에 리소스 그룹 변경 > **프로젝트에 Application Insights 추가**를 클릭합니다.
12. Git 셸에서 커밋하고 GitHub의 분기에 변경 내용을 푸시합니다. 그런 다음 클라이언트가 브라우저를 새로 고칠 때까지 기다립니다.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.	프로덕션에 배포된 앱 변경 내용을 교환합니다.

        .\swap –Name ToDoApp<your_suffix>

이것으로 끝입니다.

## 조사: 클라이언트 앱 메트릭에 슬롯 특정 태그 추가

이 섹션에서는 다양한 배포 슬롯을 구성하여 동일한 Application Insights 리소스에 슬롯 특정 원격 분석을 보냅니다. 이러한 방식으로 다른 슬롯(배포 환경)에서 트래픽 간에 원격 분석 데이터를 비교하여 앱 변경의 효과를 쉽게 파악할 수 있습니다. 같은 시간에 나머지 부분에서 프로덕션 트래픽을 구분할 수 있으므로 필요에 따라 프로덕션 앱을 계속 모니터링할 수 있습니다.

클라이언트 동작에 데이터를 수집하므로 index.cshtml에서 [JavaScript 코드에 원격 분석 이니셜라이저를 추가](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer)합니다. 예를 들어 서버 측 성능을 테스트하려는 경우 비슷하게 서버 코드에서 수행할 수 있습니다.([사용자 지정 이벤트 및 메트릭에 대한 Application Insights API](../application-insights/app-insights-api-custom-events-metrics.md)를 참조하세요.

1. 먼저 이전에 `<heading>` 태그에 추가한 JavaScript 블록 아래에서 두 개의 `//` 주석 간에 코드를 추가합니다.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    이 이니셜라이저 코드는 `appInsights` 개체를 발생시켜 전송하는 모든 원격 분석 조각에 `Environment`라는 사용자 지정 속성을 추가합니다.

2. 다음으로 Azure에서 웹앱에 대해 이 사용자 지정 속성을 [슬롯 설정](web-sites-staged-publishing.md#AboutConfiguration)에 추가합니다. 이렇게 하려면 Git 셸 섹션에서 다음 명령을 실행합니다.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    프로젝트의 Web.config은 이미 `environment` 앱 설정을 정의합니다. 이 설정으로 로컬에서 앱을 테스트할 때 메트릭은 `VS Debugger`를 사용하여 태그 처리됩니다. 그러나 Azure에 변경 내용을 푸시할 때 Azure는 대신 웹앱의 구성에서 `environment` 앱 설정을 찾고 설정하며 매트릭은 `Production`를 사용하여 태그 처리됩니다.

3. GitHub의 분기에 코드 변경 내용을 커밋하고 푸시한 다음 사용자가 새 앱을 사용하도록 기다립니다.(브라우저를 새로 고쳐야 함) 새 속성이 Application Insights `MultiChannelToDo.Web` 리소스에 표시되려면 약 15분이 걸립니다.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. 이제 **사용자 지정 이벤트** 블레이드로 다시 이동하여 `Environment=Production`에 메트릭을 필터링합니다. 이제 이 필터를 사용하여 프로덕션 슬롯에서 새 사용자 지정 이벤트를 모두 볼 수 있어야 합니다.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. **즐겨찾기** 단추를 클릭하여 현재 메트릭 탐색기 설정을 **사용자 지정 이벤트: 프로덕션** 같은 곳에 저장합니다. 나중에 이 뷰 및 배포 슬롯 뷰를 쉽게 전환할 수 있습니다.

    > [AZURE.TIP] 더욱 강력한 분석의 경우 [Power BI로 Application Insights 리소스 통합](../application-insights/app-insights-export-power-bi.md)을 고려합니다.

### 서버 앱 메트릭에 슬롯 특정 태그 추가
다시 완전성을 위해 서버 측 앱을 설정합니다. JavaScript에서 계측되는 클라이언트 앱과 달리 서버 앱에 대한 슬롯 특정 태그는 .NET 코드를 사용하여 계측됩니다.

1. *&lt;repository\_root>*\\src\\MultiChannelToDo\\Global.asax.cs를 엽니다. 네임스페이스 중괄호를 닫기 전에 아래의 코드 블록을 추가합니다.

		namespace MultiChannelToDo
		{
				...

				// Begin new code
		    public class ConfigInitializer
		    : ITelemetryInitializer
		    {
		        void ITelemetryInitializer.Initialize(ITelemetry telemetry)
		        {
		            telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
		        }
		    }
				// End new code
		}

2. 파일의 시작 부분에 아래 `using` 문을 추가하여 이름 확인 오류를 수정합니다.

		using Microsoft.ApplicationInsights.Channel;
		using Microsoft.ApplicationInsights.Extensibility;

3. `Application_Start()` 메서드의 시작 부분에 아래 코드를 추가합니다.

		TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. GitHub의 분기에 코드 변경 내용을 커밋하고 푸시한 다음 사용자가 새 앱을 사용하도록 기다립니다.(브라우저를 새로 고쳐야 함) 새 속성이 Application Insights `MultiChannelToDo` 리소스에 표시되려면 약 15분이 걸립니다.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## 업데이트: 베타 분기 설정

2. *&lt;repository\_root>*\\ARMTemplates\\ProdAndStagetest.json을 열고 `appsettings` 리소스를 찾습니다.(`"name": "appsettings"` 검색) 각 슬롯에 하나 씩 4개가 있습니다.

2. 각 `appsettings` 리소스의 경우 `properties` 배열의 끝에 `"environment": "[parameters('slotName')]"` 앱 설정을 추가합니다. 이전 줄을 쉼표로 끝내는 것을 잊지 마세요.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    템플릿의 모든 슬롯에 `environment` 앱 설정을 추가했습니다.
    
2. 동일한 파일에서 `slotconfignames` 리소스를 찾습니다.(`"name": "slotconfignames"` 검색) 각 앱에 하나 씩 2개가 있습니다.

2. 각 `slotconfignames` 리소스의 경우 `appSettingNames` 배열의 끝에 `"environment"`를 추가합니다. 이전 줄을 쉼표로 끝내는 것을 잊지 마세요.

    앱 모두에 대해 해당 배포 슬롯에 `environment` 앱 설정 스틱을 만들었습니다.

3. Git 셸 세션에서 이전에 사용한 동일한 리소스 그룹 접미사가 있는 다음 명령을 실행합니다.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. 메시지가 표시되면 이전처럼 동일한 SQL 데이터베이스 자격 증명을 지정합니다. 리소스 그룹을 업데이트하려는 요청을 받는 경우 `Y`과 `ENTER`를 차례로 입력합니다.

    스크립트가 종료되면 원래 리소스 그룹의 모든 리소스는 유지되지만 "베타"라는 새 슬롯은 후에 시작 부분에서 만든 "스테이징" 슬롯과 동일한 구성을 사용하여 만들어 집니다.

    >[AZURE.NOTE] 다양한 배포 환경을 만드는 이 메서드는 [Azure 앱 서비스로 민첩한 소프트웨어 개발](app-service-agile-software-development.md)의 메서드와 다릅니다 여기에서 배포 슬롯을 사용하여 배포 환경을 만든 반면 저기서는 리소스 그룹을 사용하여 배포 환경을 만듭니다. 리소스 그룹으로 배포 환경을 관리하면 개발자에게 제한 없이 프로덕션 환경을 유지할 수 있지만 슬롯을 쉽게 수행할 수 있는 프로덕션 환경에서 테스트가 쉽지 않습니다.

또한 원하는 경우 실행하여 알파 앱을 만들 수 있습니다.

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

이 자습서에서는 베타 앱을 계속 사용합니다.

## 업데이트: 베타 앱에 업데이트 푸시

다시 개선하려는 앱으로 돌아갑니다.

1. 베타 분기에서 지금 여러분이 있는지 확인합니다.

        git checkout beta

2. *&lt;repository\_root>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml에서 아래와 같이 `<li>` 태그를 찾고 `style="cursor:pointer"` 특성을 추가합니다.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. Azure에 커밋하고 푸시합니다.

4. http://todoapp*&lt;your_suffix>*-beta.azurewebsites.net/로 이동하여 변경 내용을 베타 슬롯에 반영하는지 확인합니다. 변경 내용을 아직 표시되지 않으면 브라우저를 새로 고쳐서 새 javascript 코드를 가져옵니다.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

이제 베타 슬롯에서 실행한 변경 사양이 있으므로 Flighting 배포를 수행할 준비가 되었습니다.

## 유효성 검사: 베타 앱에 트래픽 라우팅

이 섹션에서는 베타 앱에 트래픽을 라우팅합니다. 데모의 명확성을 위해 베타 앱에 사용자 트래픽의 상당 부분을 라우팅합니다. 실제로 라우팅하려는 트래픽 양은 특정 상황에 따라 달라집니다. 예를 들어 사이트가 microsoft.com의 규모인 경우 유용한 데이터를 얻으려면 총 트래픽이 1% 미만이어야 합니다.

1. Git 셸 세션에서 다음 명령을 실행하여 베타 슬롯에 프로덕션 트래픽의 절반을 라우팅합니다.

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  `ReroutePercentage=50` 속성은 프로덕션 트래픽의 50%가 베타 앱의 URL로 라우팅되도록 지정합니다.(`ActionHostName` 속성에서 지정)

2. 이제 http://ToDoApp*&lt;your_suffix>*.azurewebsites.net으로 이동합니다. 이제는 트래픽의 50%는 베타 슬롯으로 리디렉션되어야 합니다.

3. Application Insights 리소스에서 환경="베타"를 사용하여 메트릭을 필터링합니다.

    > [AZURE.NOTE] 다른 즐겨찾기로 이 필터링된 보기를 저장하는 경우 프로덕션 및 베타 뷰 사이에서 메트릭 탐색기 뷰를 쉽게 전환할 수 있습니다.

Application Insights에서 다음과 유사한 내용이 표시된다고 가정합니다.

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

`<li>` 태그를 많이 클릭해야 한다는 점을 표시할 뿐만 아니라 `<li>` 태그도 클릭 수가 많아 보입니다. 그런 다음 사용자는 사람들이 `<li>` 태그를 클릭할 수 있고 이제 앱에서 이전에 완료된 모든 작업을 취소한다는 결론을 얻을 수 있습니다.

Flighting 배포의 데이터에 따라 새 UI가 프로덕션 환경에 대해 준비되도록 결정합니다.

## 바로 사용: 새 코드를 프로덕션으로 이동

이제 업데이트를 프로덕션 환경으로 이동할 준비가 되었습니다. 이제 업데이트는 프로덕션에 푸쉬되기 _전에_ 이미 확인되었다는 점을 알 수 있습니다. 이제 안심하고 배포할 수 있습니다. AngularJS 클라이언트 앱에 업데이트를 수행했기 때문에 클라이언트 측 코드만 확인했습니다. 백 엔드 웹 API 앱을 변경하려면 유사하고 쉽게 변경 내용을 확인할 수 있습니다.

1. Git 셸에서 다음 명령을 실행하여 트래픽 라우팅 규칙을 제거합니다.

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Git 명령을 실행합니다.

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. 스테이징 슬롯에 배포하는 새 코드를 몇 분 기다린 다음 http://ToDoApp*&lt;your_suffix>*-staging.azurewebsites.net을 시작하여 스테이징 슬롯에서 새로운 업데이트가 준비된 상태인지 확인합니다. 분기의 마스터 분기는 앱의 스테이징 슬롯에 연결된다는 점을 기억합니다.

3. 이제 스테이징 슬롯을 프로덕션으로 교환합니다.

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## 요약 ##

Azure 앱 서비스는 일반적으로 큰 기업에서 수행한 바와 같이 중소 규모의 기업이 프로덕션에서 고객 지향 앱을 쉽게 테스트할 수 있도록 합니다. 다행히 이 자습서는 앱 서비스 및 Application Insights를 가져오는 데 필요한 지식을 제공하여 DevOps 세상에서 Flighting 배포 및 다른 테스트 중인 프로덕션 시나리오를 실행할 수 있게 합니다.

## 추가 리소스 ##

-   [Azure 앱 서비스를 사용하여 Agile 소프트웨어 개발](app-service-agile-software-development.md)
-   [Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정](web-sites-staged-publishing.md)
-	[Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-deploy-complex-application-predictably.md)
-	[Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)
-	[JSONLint-JSON 유효성 검사기](http://jsonlint.com/)
-	[Git 분기-기본 분기 및 병합](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Azure PowerShell](../powershell-install-configure.md)
-	[프로젝트 Kudu Wiki](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_0803_2016-->