<properties 
	pageTitle="New Relic 응용 프로그램 성능 관리를 사용하는 Azure 앱 서비스에서의 .NET 웹 앱" 
	description="Azure 응용 프로그램 서비스에서 실행 중인 ASP.NET 응용 프로그램에 New Relic 성능 모니터링을 사용하는 방법에 대해 알아봅니다." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="stepsic"/>



# New Relic 응용 프로그램 성능 관리를 사용하는 Azure 앱 서비스에서의 .NET 웹 앱

이 가이드에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에서 웹 앱에 세계 최고 수준의 New Relic 성능 모니터링을 추가하는 방법에 대해 설명합니다. 응용 프로그램에 New Relic을 빠르고 간단하게 추가하는 프로세스를 다루며 New Relic의 일부 기능을 소개합니다. New Relic을 사용하는 방법에 대한 자세한 내용은 [New Relic 사용](#using-new-relic)을 참조하십시오.

## New Relic 정의

New Relic은 개발자에 초점을 맞춘 도구로 프로덕션 응용 프로그램을 모니터링하고 성능 및 안정성에 대한 깊은 통찰력을 제공하는 도구입니다. 성능 문제를 확인하고 진단할 때 시간을 절약할 수 있도록 설계되었으며, 성능 문제를 해결하는 데 필요한 정보를 즉시 제공합니다.

New Relic은 서버 및 사용자 브라우저의 웹 트랜잭션에 대한 부하 시간 및 처리량을 추적합니다. 그리고 데이터베이스 사용 시간을 보여 주고, 느린 쿼리 및 웹 요청을 분석하고, 가동 시간 모니터링 및 경고를 제공하고, 응용 프로그램 예외를 추적하는 등의 다양한 기능을 제공합니다.

## Azure 스토어에서 제공하는 New Relic의 특별 가격

New Relic Standard는 Azure 사용자에게 무료입니다. New Relic Pro는 사용 중인 웹 사이트 모드에 따라 여러 패키지로 제공되며 reserved 모드에서 실행 중인 경우에는 인스턴스 크기에 따라 제공됩니다.

가격 책정 정보는 [Azure 마켓플레이스의 New Relic 페이지](/marketplace/partners/newrelic/newrelic)를 참조하십시오.

> [AZURE.NOTE] 최대 10개의 계산 인스턴스에 대해서만 가격이 책정됩니다. 인스턴스가 11개 이상인 경우에는 New Relic(sales@newrelic.com)에 볼륨 가격을 문의하십시오.

Azure 고객은 New Relic 에이전트를 배포할 때 New Relic Pro에 대해 2주의 평가판 구독을 받습니다.

Azure 마켓플레이스를 사용하여 New Relic에 등록 
--

New Relic은 Azure 웹 역할, 작업자 역할 및 Azure 앱 서비스와 원활하게 통합됩니다.

Azure 마켓플레이스에서 직접 New Relic에 등록하려면 다음과 같이 간단한 네 단계를 수행하십시오.

## 1단계. New Relic 계정 만들기

1. [Azure 포털](https://portal.azure.com/)에 로그인하여 모서리에 있는 **새로 만들기**를 클릭합니다.
3. **개발자 서비스** > **New Relic APM**을 클릭합니다.
4. 다음을 지정하여 New Relic 계정을 구성한 다음 **만들기**를 클릭합니다.
	- **Name**
	- **가격 책정 계층**
	- **리소스 그룹**
	- **구독**
	- **위치**
	- **약관**

11. **만들기**를 클릭하면 New Relic 계정을 만드는 프로세스가 시작됩니다. **알림** 단추를 클릭하여 상태를 모니터링할 수 있습니다. 만들고 나면 New Relic 계정 블레이드가 표시됩니다.

12. New Relic 라이선스 키를 검색하려면 블레이드 맨 위쪽에서 **Essentials** 패널을 살펴봅니다. 웹 앱 인스턴스에 New Relic 계정을 사용하여 웹 앱을 통합할 때 해당 앱 설정에서 이 라이선스 키가 자동으로 등록됩니다.

## 2단계: 웹 앱에 대해 New Relic 통합 구성

1. [Azure 포털](https://portal.azure.com/)에서 웹앱의 블레이드를 엽니다.
2. 블레이드 맨 위에 있는 "..." 메뉴에서 **타일 추가**를 클릭합니다.
3. **모니터링**탭에서 **응용 프로그램 요약**을 선택하고 타일이 웹 앱의 블레이드에 나타나도록 하려는 위치로 끕니다.
4. 완료를 클릭하여 타일 추가 작업을 마칩니다.
5. **응용 프로그램 모니터링** 타일을 클릭하고 **New Relic**을 선택합니다.
6. 이전 단계에서 만든 계정을 선택한 다음 **확인**을 클릭합니다. 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	저장 작업이 완료되면 웹 앱의 블레이드에서 **모든 설정**을 클릭한 다음 **응용 프로그램 설정**을 클릭합니다. New Relic 지원을 위해 블레이드의 **앱 설정** 섹션에 추가된 **NEWRELIC_LICENSEKEY** 설정이 보입니다.

	>[AZURE.NOTE] 새 앱 설정이 적용되기까지는 최대 30초 정도의 시간이 소요됩니다. 설정이 즉시 적용되도록 하려면 웹 앱을 다시 시작합니다.

## 3단계: ASP.NET 웹 앱 게시

Visual Studio를 사용하여 웹앱을 게시합니다. 이전에 웹 앱을 게시한 경우 다시 게시하여 웹 앱 인스턴스가 New Relic 모니터링을 사용하도록 필요한 New Relic NuGet 패키지를 추가합니다.

## 4단계. New Relic에서 응용 프로그램 성능 확인

New Relic 대시보드를 보려면 다음을 수행합니다.

2. [Azure 포털](https://portal.azure.com/)에서 웹앱의 블레이드를 엽니다.
3. **응용 프로그램 모니터링** > **응용 프로그램 이름** > **New Relic에서 보기**를 클릭합니다.

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. 계정 사용이 처음인 경우 계정 정보를 구성합니다.
3. New Relic 메뉴 모음에서 **Applications > (응용 프로그램 이름)**을 선택합니다.

	**Monitoring > Overview** 대시보드가 자동으로 나타납니다.

	![New Relic 모니터링 대시보드](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

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
       <td>Overview의 <b>Browser</b> 보기에서 Global Apdex 맵의 아무 곳이나 마우스로 가리킵니다.<br /><b>팁:</b> 선택한 앱의 <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> 대시보드로 바로 이동하려면 <b>Global Apdex</b> 제목을 클릭하거나 Global Apdex 맵의 아무 곳이나 클릭합니다.</td>
    </tr>
    <tr>
       <td><a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a> 대시보드 보기</td>
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

![패키지 관리자 콘솔](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## 다음 단계

자세한 내용은 다음 추가 리소스에서 확인하십시오.

 * [Azure 웹앱에서 .NET 에이전트 설치](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-preview-portal#install-new-relic-azure-webapps): 새 Relic.NET 에이전트 설치 절차 
 * [New Relic 사용자 인터페이스](https://newrelic.com/docs/site/the-new-relic-ui): New Relic UI, 사용자 권한 및 프로필 설정, 표준 기능 및 대시보드 드릴다운 세부 정보 사용 등에 대한 개요
 * [응용 프로그램 개요](https://newrelic.com/docs/site/applications-overview): New Relic의 Applications Overview 대시보드 사용 시 특징 및 기능
 * [Apdex](https://newrelic.com/docs/site/apdex): Apdex에서 응용 프로그램에 대한 최종 사용자의 만족도를 측정하는 방법에 대한 개요
 * [실제 사용자 모니터링](https://newrelic.com/docs/features/real-user-monitoring): RUM에서 사용자의 브라우저가 웹 페이지를 로드하는 데 걸리는 시간, 사용자의 위치 및 사용하는 브라우저의 종류를 자세히 표시하는 방법에 대한 개요
 * [도움말 찾기](https://newrelic.com/docs/site/finding-help): New Relic의 온라인 도움말 센터를 통해 사용 가능한 리소스

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.


[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!---HONumber=AcomDC_0128_2016-->