<properties 
	pageTitle="C++ 앱용 Application Insights" 
	description="Application Insights를 사용하여 C++ 앱의 사용 현황 및 성능을 분석합니다." 
	services="application-insights" 
	documentationCenter="cpp" 
	authors="crystk" 
	manager="jakubo"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# C++ 앱용 Application Insights

Visual Studio Application Insights를 사용하여 모바일 응용 프로그램의 사용 현황, 이벤트 및 충돌을 모니터링할 수 있습니다.

## 요구 사항

필요한 사항:

* [Microsoft Azure](http://azure.com) 구독. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다.
* Visual Studio 2015 이상.

## Application Insights 리소스 만들기

[Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. Windows Phone 또는 Windows 저장소 옵션을 선택합니다.

![새로 만들기, 개발자 서비스, Application Insights를 차례대로 클릭](./media/app-insights-windows-cpp/01-universal.png)

열리는 블레이드에서 앱의 성능 및 사용 데이터를 볼 수 있습니다. 다음에 Azure에 로그인할 때 다시 이 블레이드로 돌아가려면 시작 화면에서 해당 타일을 찾아야 합니다. 또는 찾아보기를 클릭하여 찾아야 합니다.

####  계측 키를 복사합니다.

키는 리소스를 식별하며, 데이터를 리소스로 보내기 위해 SDK에서 곧 설치합니다.

![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a>응용 프로그램에 SDK 설치


1. Visual Studio에서 데스크톱 앱 프로젝트의 NuGet 패키지를 편집합니다.

    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/app-insights-windows-cpp/03-nuget.png)

2. C++ 앱용 Application Insights SDK를 설치합니다.

    ![**온라인**, **시험판 포함** 선택 및 "Application Insights" 검색](./media/app-insights-windows-cpp/04-ai-nuget.png)

3. 릴리스 및 디버그용 프로젝트 설정에:
  - 프로젝트 속성에 $(SolutionDir)packages\ApplicationInsights-CPP.1.0.0-Beta\src\inc 추가 -> VC++ 디렉터리 -> 디렉터리 포함
  - 프로젝트 속성에 $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP 추가 -> VC++ 디렉터리 -> 라이브러리 디렉터리

4. $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\ApplicationInsights에서 프로젝트에 ApplicationInsights.winmd를 참조로 추가
5. AppInsights_Win10-UAP.dll from $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP을 추가합니다. 속성으로 이동하여 콘텐츠를 '예'로 설정합니다. 이렇게 dll을 빌드 디렉터리에 복사합니다.

## SDK 사용

SDK를 초기화하고 원격 분석 추적을 시작합니다.

1. App.xaml.h에서: 
  - 추가: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. App.xaml.cpp에서:
  - 추가: `using namespace ApplicationInsights::CX;`

  - App:App()에서
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - 일단 루트 프레임을 만들어 내면 (일반적으로 App::OnLaunched 끝에) m_session을 초기화합니다:
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. 응용 프로그램에서 다른 곳을 추적하려면 원격 분석 클라이언트의 인스턴스를 선언할 수 있습니다.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> 프로젝트 실행

원격 분석을 생성하는 응용 프로그램을 실행합니다. 이것을 개발 컴퓨터의 디버그 상태에서 더 많이 실행하거나 사용자가 실행할 수 있도록 게시할 수 있습니다.

## Application Insights에서 데이터 보기

http://portal.azure.com으로 돌아가서 Application Insights 리소스를 찾습니다.

검색을 클릭하여 [진단 검색][diagnostic]을 엽니다. 여기에 첫 번째 이벤트가 표시됩니다. 아무 것도 표시되지 않으면 1-2분 정도 기다렸다가 새로 고침을 클릭합니다.

![진단 검색 클릭](./media/app-insights-windows-cpp/21-search.png)

앱을 사용하면 데이터 개요 블레이드에 데이터가 표시됩니다.

![개요 블레이드](./media/app-insights-windows-cpp/22-oview.png)

원하는 차트를 클릭하여 자세한 내용을 봅니다. 예를 들어 충돌 차트를 클릭하면 다음 내용이 표시됩니다.

![충돌 차트 클릭](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>다음 단계

[앱 사용량 추적][track]

[사용자 지정 이벤트 및 메트릭을 전송하기 위한 API 사용][api]

[진단 검색][diagnostic]

[메트릭 탐색기][metrics]

[문제 해결][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->