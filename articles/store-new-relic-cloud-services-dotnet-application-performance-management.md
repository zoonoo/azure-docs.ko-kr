<properties 
	pageTitle="Azure에서 New Relic 사용 - Azure 기능 가이드" 
	description="New Relic 서비스를 사용하여 Azure 응용 프로그램을 관리 및 모니터링하는 방법에 대해 알아봅니다." 
	services="" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>



# Azure에서 New Relic 응용 프로그램 성능 관리

이 가이드에서는 Azure 호스티드 응용 프로그램에 세계 최고 수준의 New Relic 성능 모니터링을 추가하는 방법에 대해 설명합니다. 응용 프로그램에 New Relic을 빠르고 간단하게 추가하는 프로세스를 다루며 New Relic의 일부 기능을 소개합니다. New Relic을 사용하는 방법에 대한 자세한 내용은 [New Relic 사용](#using-new-relic)을 참조하십시오.

New Relic 정의
--

New Relic은 개발자에 초점을 맞춘 도구로 프로덕션 응용 프로그램을 모니터링하고 성능 및 안정성에 대한 깊은 통찰력을 제공하는 도구입니다. 성능 문제를 확인하고 진단할 때 시간을 절약할 수 있도록 설계되었으며, 성능 문제를 해결하는 데 필요한 정보를 즉시 제공합니다.

New Relic은 서버 및 사용자 브라우저의 웹 트랜잭션에 대한 부하 시간 및 처리량을 추적합니다. 그리고 데이터베이스 사용 시간을 보여 주고, 느린 쿼리 및 웹 요청을 분석하고, 가동 시간 모니터링 및 경고를 제공하고, 응용 프로그램 예외를 추적하는 등의 다양한 기능을 제공합니다.

Azure 스토어에서 제공하는 New Relic의 특별 가격 
--

New Relic Standard는 Azure 사용자에게 무료이며, New Relic Pro는 Azure 클라우드 서비스의 인스턴스 크기에 따라 제공됩니다.

가격 책정 정보는 [Azure 스토어의 New Relic 페이지](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic)(영문)를 참조하십시오.

> [AZURE.NOTE]최대 10개의 계산 인스턴스에 대해서만 가격이 책정됩니다. 인스턴스가 11개 이상인 경우에는 New Relic(sales@newrelic.com)에 볼륨 가격을 문의하십시오.

Azure 고객은 New Relic 에이전트를 배포할 때 New Relic Pro에 대해 2주의 평가판 구독을 받습니다.

Azure 스토어를 사용하여 New Relic에 등록 
--

New Relic은 Azure 웹 역할 및 작업자 역할과 원활하게 통합됩니다.

Azure 스토어에서 직접 New Relic에 등록하려면 다음과 같이 간단한 세 단계를 수행하십시오.

### 1단계. Azure 스토어를 통해 등록

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 관리 포털의 아래쪽 창에서 **새로 만들기**를 클릭합니다.
3. **스토어**를 클릭합니다.
4. **추가 기능 선택** 대화 상자에서 **New Relic**을 선택하고 **다음**을 클릭합니다.
5. **추가 기능 개인 설정** 대화 상자에서 원하는 New Relic 플랜을 선택합니다.
6. 해당되는 경우 프로모션 코드를 입력합니다.
7. Azure 설정에 New Relic 서비스를 표시할 이름을 입력하거나 기본값인 **NewRelic**을 사용합니다. 이 이름은 가입한 Azure 스토어 항목 목록에서 고유해야 합니다.
8. 지역 값(예: **미국 서부**)을 선택합니다.
9. **다음**을 클릭합니다.
10. **구입 검토** 대화 상자에서 플랜 및 가격 정보를 검토하고 약관을 확인합니다. 약관에 동의하는 경우 **구입**을 클릭합니다.
11. **구입**을 클릭하면 New Relic 계정을 만드는 프로세스가 시작됩니다. 이제 Azure 관리 포털에서 상태를 모니터링할 수 있습니다.
12. New Relic 라이선스 키를 검색하려면 **출력 값**을 클릭합니다. 
13. 표시되는 라이선스 키를 복사합니다. New Relic Nuget 패키지를 설치할 때 이 키를 입력해야 합니다.

### 2단계. Nuget 패키지 설치

1. Visual Studio 솔루션을 열거나 **파일 > 새로 만들기 > 프로젝트**를 선택하여 새로 만듭니다.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. 솔루션에 아직 Azure 클라우드 서비스 프로젝트가 없는 경우 솔루션 탐색기에서 앱을 마우스 오른쪽 단추로 클릭하고 **Azure 클라우드 서비스 프로젝트 추가**를 선택하여 추가합니다.

	![클라우드 서비스 만들기](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. **도구 > 라이브러리 패키지 관리자 > 패키지 관리자 콘솔**을 선택하여 패키지 관리자 콘솔을 엽니다. 프로젝트가 패키지 관리자 콘솔 창의 맨 위에서 기본 프로젝트가 되도록 설정합니다.

	![패키지 관리자 콘솔](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. 패키지 관리자 명령 프롬프트에서 `Install-Package
   NewRelicWindowsAzure`을(를) 입력한 후 **Enter** 키를 누릅니다.

	![패키지 관리자에 설치](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. 라이선스 키 프롬프트에 Azure 스토어에서 가져온 라이선스 키를 입력합니다.

	![라이선스 키 입력](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. 선택 사항: 응용 프로그램 이름 프롬프트에서 New Relic 대시보드에 표시할 앱 이름을 입력합니다. 또는 솔루션 이름을 기본값으로 사용합니다.

	![응용 프로그램 이름 입력](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. 솔루션 탐색기에서 Azure 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![클라우드 프로젝트 게시](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**참고:** Azure에 이 앱을 처음 배포하는 경우 Azure 자격 증명을 입력하라는 메시지가 표시됩니다. 자세한 내용은 <a href="/develop/net/tutorials/get-started/">Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포</a>를 참조하십시오.

![게시 설정](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### 3단계. New Relic에서 응용 프로그램 성능 확인

New Relic 대시보드를 보려면 다음을 수행합니다.

1. Azure 포털에서 **관리** 단추를 클릭합니다.
2. New Relic 계정 전자 메일 및 암호로 로그인합니다.
3. New Relic 메뉴 모음에서 **Applications > (응용 프로그램 이름)**을 선택합니다.

	**Monitoring > Overview** 대시보드가 자동으로 나타납니다.

	![New Relic 모니터링 대시보드](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	**Applications** 메뉴의 목록에서 앱을 선택하면 **Overview** 대시보드에서 현재 앱 서버 및 브라우저 정보를 표시합니다.

### <a id="using-new-relic"></a>New Relic 사용

Applications 메뉴의 목록에서 앱을 선택하면 Overview 대시보드에서 현재 앱 서버 및 브라우저 정보를 표시합니다. 두 보기 간에 화면을 전환하려면 **App server** 또는 **Browser** 단추를 클릭합니다.

Applications Overview 대시보드에는 <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">표준 New Relic UI</a> 및 <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">대시보드 드릴다운</a> 기능 외에도 추가 기능이 있습니다.

<table border="1">
  <thead>
    <tr>
      <th><b>수행하려는 작업</b></th>
      <th><b>방법</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>선택한 앱 서버 또는 브라우저에 대한 대시보드 정보 표시</td>
       <td><b>App Server</b> 또는 <b>Browser</b> 단추를 클릭합니다.</td>
    </tr>
     <tr>
       <td>앱의 <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> score에 대한 임계값 수준 보기</td>
       <td>Apdex score <b>?<b> 아이콘</b></b>을 가리킵니다.</td>
    </tr>
    <tr>
       <td>Worldwide Apdex 세부 정보 보기</td>
       <td>Overview의 <b>Browser</b> 보기에서 Global Apdex 맵의 아무 곳이나 마우스로 가리킵니다.<br /><b>팁:</b> 선택한 앱의 <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">Geography</a> 대시보드로 바로 이동하려면 <b>Global Apdex</b> 제목을 클릭하거나 Global Apdex 맵의 아무 곳이나 클릭합니다.</td>
    </tr>
    <tr>
       <td><a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a> 대시보드 보기</td>
       <td>Applications Overview 대시보드에서 Web Transactions 테이블을 클릭합니다. 또는 특정 웹 트랜잭션(<a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a> 포함)에 대한 세부 정보를 보려면 해당 이름을 클릭합니다.</td>
    </tr>
    <tr>
       <td><a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a> 대시보드 보기</td>
       <td>Applications Overview 대시보드에서 Error rate 차트 제목을 클릭합니다.<br /><b>팁:</b> <b>Applications</b> > (사용자의 앱) > Events > Errors를 클릭하여 Errors 대시보드를 볼 수도 있습니다.</td>
    </tr>
    <tr>
       <td>앱 서버 세부 정보 보기</td>
       <td><p>다음 중 하나를 수행합니다.<p>
        <ul>
          <li>호스트의 테이블 보기 또는 각 호스트의 브레이크아웃 메트릭 세부 정보 간에 전환합니다.</li>
          <li>개별 서버 이름을 클릭합니다.</li>
          <li>개별 서버의 Apdex score를 가리킵니다.</li>
          <li>개별 서버의 CPU usage 또는 Memory를 클릭합니다.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

아래 그림은 Browser 보기를 선택한 경우 Applications Overview 대시보드의 예제입니다.

![패키지 관리자 콘솔](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## 다음 단계

자세한 내용은 다음 추가 리소스에서 확인하십시오.

 * [Azure에서.NET 에이전트 설치](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): 새 Relic.NET 에이전트 설치 절차 
 * [New Relic 사용자 인터페이스](https://newrelic.com/docs/site/the-new-relic-ui): New Relic UI, 사용자 권한 및 프로필 설정, 표준 기능 및 대시보드 드릴다운 세부 정보 사용 등에 대한 개요
 * [응용 프로그램 개요](https://newrelic.com/docs/site/applications-overview): New Relic의 Applications Overview 대시보드 사용 시 특징 및 기능
 * [Apdex](https://newrelic.com/docs/site/apdex): Apdex에서 응용 프로그램에 대한 최종 사용자의 만족도를 측정하는 방법에 대한 개요
 * [실제 사용자 모니터링](https://newrelic.com/docs/features/real-user-monitoring): RUM에서 사용자의 브라우저가 웹 페이지를 로드하는 데 걸리는 시간, 사용자의 위치 및 사용하는 브라우저의 종류를 자세히 표시하는 방법에 대한 개요
 * [도움말 찾기](https://newrelic.com/docs/site/finding-help): New Relic의 온라인 도움말 센터를 통해 사용 가능한 리소스

<!--HONumber=54-->