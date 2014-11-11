<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="dwrede" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new="" relic" />

# Azuren 웹 사이트에서 New Relic 응용 프로그램 성능 관리

이 가이드에서는 Azure 웹 사이트에 세계 최고 수준의 New Relic 성능
모니터링을 추가하는 방법에 대해 설명합니다. 응용 프로그램에 New Relic을
빠르고 간단하게 추가하는 프로세스를 다루며 New Relic의 일부 기능을 소개합니다.
 New Relic을 사용하는 방법에 대한 자세한 내용은 [New Relic 사용][New Relic 사용]을 참조하십시오.

## New Relic 정의

New Relic은 개발자에 초점을 맞춘 도구로 프로덕션 응용 프로그램을 모니터링하고
성능 및 안정성에 대한 깊은 통찰력을 제공하는 도구입니다. 성능
문제를 확인하고 진단할 때 시간을 절약할 수 있도록 설계되었으며, 성능 문제를 해결하는 데
필요한 정보를 즉시 제공합니다.

New Relic은 서버 및 사용자 브라우저의 웹 트랜잭션에 대한 부하 시간 및 처리량을
추적합니다. 그리고 데이터베이스 사용 시간을 보여 주고,
느린 쿼리 및 웹 요청을 분석하고, 가동 시간 모니터링 및 경고를 제공하고, 응용 프로그램
예외를 추적하는 등의 다양한 기능을 제공합니다.

## Azure 스토어에서 제공하는 New Relic의 특별 가격

New Relic Standard는 Azure 사용자에게 무료입니다.
New Relic Pro는 사용 중인 웹 사이트 모드에 따라 여러 패키지로 제공되며 reserved 모드에서 실행 중인 경우에는 인스턴스 크기에 따라 제공됩니다.

가격 책정 정보는 [Azure 스토어의 New Relic 페이지][Azure 스토어의 New Relic 페이지](영문)를 참조하십시오.

<div class="dev-callout">

**참고:**
최대 10개의 계산 인스턴스에 대해서만 가격이 책정됩니다. 인스턴스가 11개 이상인 경우에는 New Relic(sales@newrelic.com)에 볼륨 가격을 문의하십시오.

</div>

Azure 고객은 New Relic 에이전트를 배포할 때 New Relic Pro에 대해 2주의 평가판 구독을 받습니다.

## Azure 스토어를 사용하여 New Relic에 등록

New Relic은 Azure 웹 역할, 작업자 역할 및 웹 사이트와 원활하게 통합됩니다.

Azure 스토어에서 직접 New Relic에 등록하려면 다음과 같이 간단한 네 단계를 수행하십시오.

### 1단계. Azure 스토어를 통해 등록

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  관리 포털의 아래쪽 창에서 **새로 만들기**를 클릭합니다.
3.  **스토어**를 클릭합니다.
4.  **추가 기능 선택** 대화 상자에서 **New Relic**을 선택하고 **다음**을 클릭합니다.
5.  **추가 기능 개인 설정** 대화 상자에서 원하는 New Relic 플랜을 선택합니다.
6.  Azure 설정에 New Relic 서비스를 표시할 이름을 입력하거나 기본값인
    **NewRelic**을 사용합니다. 이 이름은 가입한 Azure 스토어
    항목 목록에서 고유해야 합니다.
7.  지역 값(예: **미국 서부**)을 선택합니다.
8.  **다음**을 클릭합니다.
9.  **구입 검토** 대화 상자에서 플랜 및 가격 정보를 검토하고 약관을 확인합니다.
     약관에 동의하는 경우 **구입**을 클릭합니다.
10. **구입**을 클릭하면 New Relic 계정을 만드는 프로세스가 시작됩니다. 이제 Azure 관리 포털에서 상태를 모니터링할 수 있습니다.
11. New Relic 라이선스 키를 가져오려면 방금 만든 추가 기능을 클릭한 다음 **연결 정보**를 클릭합니다.
12. 표시되는 라이선스 키를 복사합니다. New Relic Nuget 패키지를 설치할 때 이 키를 입력해야 합니다.

### 2단계. New Relic 패키지 설치

New Relic Websites Agent는 NuGet 패키지로 분산되고 Visual Studio나 WebMatrix를 사용하여 웹 사이트에 추가될 수 있습니다. Azure 웹 사이트로 Visual Studio나 WebMatrix를 사용하는 데 익숙지 않으면 [Visual Studio를 사용하여 Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포][Visual Studio를 사용하여 Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포] 또는 [Microsoft WebMatrix로 웹 사이트 개발 및 배포][Microsoft WebMatrix로 웹 사이트 개발 및 배포]를 참조하십시오.

사용 중인 특정 개발 환경에 맞게 다음 단계를 수행합니다.

**Visual Studio**

1.  Visual Studio 웹 사이트 솔루션을 엽니다.

2.  **도구 \> 라이브러리 패키지 관리자 \> 패키지 관리자 콘솔**을 선택하여 패키지 관리자
    콘솔을 엽니다. 프로젝트가 패키지 관리자 콘솔 창의 맨 위에서
    기본 프로젝트가 되도록 설정합니다.

    ![패키지 관리자 콘솔][패키지 관리자 콘솔]

3.  패키지 관리자 명령 프롬프트에서 다음 명령을 사용하여 패키지를 설치합니다.

        Install-Package NewRelic.Azure.WebSites

4.  라이선스 키 프롬프트에 Azure 스토어에서 가져온 라이선스 키를 입력합니다.

    ![라이선스 키 입력][라이선스 키 입력]

<!--5. Optional: At the application name prompt, enter your app's name as it will    appear in New Relic's dashboard. Or, use your solution name as the default.      ![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1.  WebMatrix를 사용하여 웹 사이트를 엽니다.

2.  리본의 **Home** 탭에서 **NuGet**을 선택합니다.

    ![home 탭의 nuget 단추][home 탭의 nuget 단추]

3.  NuGet Gallery에서 원본을 **NuGet Official Package Source**로 설정한 다음 NewRelic.Azure.WebSites를 검색합니다.

    ![NewRelic.Azure.WebSites를 검색 중인 nuget gallery][NewRelic.Azure.WebSites를 검색 중인 nuget gallery]

4.  **New Relic for Azure Websites** 항목을 선택한 다음 **Install**을 클릭합니다.

5.  패키지를 설치하고 나면 사이트에 이름이 **newrelic**이라는 폴더가 포함됩니다. 이 폴더를 확장하고 **newrelic.config** 파일을 엽니다. 이 파일에서 **REPLACE\_WITH\_LICENSE\_KEY** 값을 Azure 스토어에서 가져온 라이선스 키로 바꿉니다.

    ![newrelic.conf가 선택된 채 확장된 newrelic 폴더][newrelic.conf가 선택된 채 확장된 newrelic 폴더]

    라이선스 키 정보를 추가한 후 변경 내용을 **newrelic.config** 파일에 저장합니다.

### 3단계. 웹 사이트 구성 및 응용 프로그램 게시

이전 단계에서 응용 프로그램에 추가된 New Relic 패키지는 Azure 웹 사이트에 추가된 **앱 설정**에서 구성합니다. 다음 단계에 따라 이러한 설정을 추가합니다.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인하고 웹 사이트로 이동합니다.

2.  웹 사이트에서 **구성**을 선택합니다. **개발자 분석** 섹션에서 **추가 기능**이나 **사용자 지정** 중 하나를 선택합니다. 두 메서드는 출력은 동일하지만 입력은 약간 다릅니다. **추가 기능**에서는 현재 New-Relic 라이선스가 표시되어 그중 하나를 선택할 수 있으며, **사용자 지정**에서는 수동으로 라이선스 키를 지정해야 합니다.

    **추가 기능**을 선택했으면 **추가 기능 선택** 필드를 사용하여 New-Relic 라이선스를 선택합니다.

    ![추가 기능 필드 이미지][추가 기능 필드 이미지]

    **사용자 지정**을 선택했으면 New-Relic을 **공급자**로 선택한 다음 **공급자 키** 필드에 라이선스를 입력합니다.

    ![사용자 지정 필드 이미지][사용자 지정 필드 이미지]

3.  **개발자 분석**에서 라이선스를 지정한 후 **저장**을 클릭합니다. 저장 작업이 완료되면 페이지의 **앱 설정** 섹션에 다음 값이 추가되어 New-Relic을 지원합니다.

    <table border="1">
    <thead>
    <tr>
    <td>
    키

    </td>
    <td>
    값

    </td>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>
    COR\\\_ENABLE\\\_PROFILING

    </td>
    <td>
    1

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER

    </td>
    <td>
    {71DA0A04-7777-4EC6-9643-7D28B46A8A41}

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER\\\_PATH

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic\\NewRelic.Profiler.dll

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_HOME

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_LICENSEKEY

    </td>
    <td>
    자신의 라이선스 키

    </td>
    </tr>
    </tbody>
    </table>

    <div class="dev-callout">

    **참고**
    새 **앱 설정**이 적용되기까지는 최대 30초 정도의 시간이 소요됩니다. 설정이 즉시 적용되도록 하려면 웹 사이트를 다시 시작합니다.

    </div>

4.  Visual Studio 또는 WebMatrix를 사용하여 응용 프로그램을 게시합니다.

### 4단계. New Relic에서 응용 프로그램 성능 확인

New Relic 대시보드를 보려면 다음을 수행합니다.

1.  Azure 포털에서 **관리** 단추를 클릭합니다.
2.  New Relic 계정 전자 메일 및 암호로 로그인합니다.
3.  New Relic 메뉴 모음에서 **Applications \> (응용 프로그램 이름)**을 선택합니다.

    **Monitoring \> Overview** 대시보드가 자동으로 나타납니다.

    ![New Relic 모니터링 대시보드][New Relic 모니터링 대시보드]

    **Applications** 메뉴의 목록에서 앱을 선택하면 **Overview** 대시보드에서 현재 앱 서버 및 브라우저 정보를 표시합니다.

### <span id="using-new-relic"></span></a>New Relic 사용

Applications 메뉴의 목록에서 앱을 선택하면 Overview 대시보드에서 현재 앱 서버 및 브라우저 정보를 표시합니다. 두 보기 간에 화면을 전환하려면 **App server** 또는 **Browser** 단추를 클릭합니다.

Applications Overview 대시보드에는 [표준 New Relic UI][표준 New Relic UI](영문) 및 [대시보드 드릴다운][대시보드 드릴다운](영문) 기능 외에도 추가 기능이 있습니다.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>수행하려는 작업</strong></th>
<th align="left"><strong>방법</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">선택한 앱 서버 또는 브라우저에 대한 대시보드 정보 표시</td>
<td align="left"><strong>App Server</strong> 또는 <strong>Browser</strong> 단추를 클릭합니다.</td>
</tr>
<tr class="even">
<td align="left">앱의 <a href="https://newrelic.com/docs/site/apdex">Apdex</a> score에 대한 임계값 수준 보기</td>
<td align="left">Apdex score <strong>? <strong>아이콘</strong></strong>을 가리킵니다.</td>
</tr>
<tr class="odd">
<td align="left">Worldwide Apdex 세부 정보 보기</td>
<td align="left">Overview의 <strong>Browser</strong> 보기에서 Global Apdex 맵의 아무 곳이나 마우스로 가리킵니다.<br /><strong>팁:</strong> 선택한 앱의 <a href="https://newrelic.com/docs/site/geography">Geography</a> 대시보드로 바로 이동하려면 <strong>Global Apdex</strong> 제목을 클릭하거나 Global Apdex 맵의 아무 곳이나 클릭합니다.</td>
</tr>
<tr class="even">
<td align="left"><a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard">Web Transactions</a> 대시보드 보기</td>
<td align="left">Applications Overview 대시보드에서 Web Transactions 테이블을 클릭합니다. 또는 특정 웹 트랜잭션(<a href="https://newrelic.com/docs/site/key-transactions">Key Transactions</a> 포함)에 대한 세부 정보를 보려면 해당 이름을 클릭합니다.</td>
</tr>
<tr class="odd">
<td align="left"><a href="https://newrelic.com/docs/site/errors">Errors</a> 대시보드 보기</td>
<td align="left">Applications Overview 대시보드에서 Error rate 차트 제목을 클릭합니다.<br /><strong>팁:</strong> <strong>Applications</strong> &gt; (사용자의 앱) &gt; Events &gt; Errors를 클릭하여 Errors 대시보드를 볼 수도 있습니다.</td>
</tr>
<tr class="even">
<td align="left">앱 서버 세부 정보 보기</td>
<td align="left"><p>다음 중 하나를 수행합니다.</p>
<p></p>
<ul>
<li>호스트의 테이블 보기 또는 각 호스트의 브레이크아웃 메트릭 세부 정보 간에 전환합니다.</li>
<li>개별 서버 이름을 클릭합니다.</li>
<li>개별 서버의 Apdex score를 가리킵니다.</li>
<li>개별 서버의 CPU usage 또는 Memory를 클릭합니다.</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

아래 그림은 Browser 보기를 선택한 경우 Applications Overview 대시보드의 예제입니다.

![패키지 관리자 콘솔][1]

## 다음 단계

자세한 내용은 다음 추가 리소스에서 확인하십시오.

-   [Azure 웹 사이트를 위해 .NET 에이전트 설치][Azure 웹 사이트를 위해 .NET 에이전트 설치](영문): New Relic .NET 에이전트 설치 절차
-   [New Relic 사용자 인터페이스][New Relic 사용자 인터페이스](영문):
    New Relic UI, 사용자 권한 및 프로필 설정, 표준 기능 및 대시보드 드릴다운 세부 정보 사용 등에 대한 개요
-   [Applications Overview][Applications Overview](영문): New Relic의 Applications Overview 대시보드에서 사용할 수 있는 기능
-   [Apdex][Apdex](영문): Apdex에서 응용 프로그램에 대한 최종 사용자의 만족도를 측정하는 방법에 대한 개요
-   [실제 사용자 모니터링][실제 사용자 모니터링](영문): RUM에서 사용자의 브라우저가 웹 페이지를 로드하는 데 걸리는 시간,
     사용자의 위치 및 사용하는 브라우저의 종류를 자세히 표시하는 방법에 대한 개요
-   [도움말 찾기][도움말 찾기](영문): New Relic의 온라인 도움말 센터를 통해 사용 가능한 리소스

  [New Relic 사용]: #using-new-relic
  [Azure 스토어의 New Relic 페이지]: http://www.windowsazure.com/ko-kr/gallery/store/new-relic/new-relic/
  [Azure 관리 포털]: https://manage.windowsazure.com
  [Visual Studio를 사용하여 Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포]: http://www.windowsazure.com/ko-kr/develop/net/tutorials/get-started/
  [Microsoft WebMatrix로 웹 사이트 개발 및 배포]: http://www.windowsazure.com/ko-kr/develop/net/tutorials/website-with-webmatrix/
  [패키지 관리자 콘솔]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [라이선스 키 입력]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
  [home 탭의 nuget 단추]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
  [NewRelic.Azure.WebSites를 검색 중인 nuget gallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png
  [newrelic.conf가 선택된 채 확장된 newrelic 폴더]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
  [추가 기능 필드 이미지]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
  [사용자 지정 필드 이미지]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
  [New Relic 모니터링 대시보드]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png
  [표준 New Relic UI]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [대시보드 드릴다운]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [1]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png
  [Azure 웹 사이트를 위해 .NET 에이전트 설치]: https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install
  [New Relic 사용자 인터페이스]: https://newrelic.com/docs/site/the-new-relic-ui
  [Applications Overview]: https://newrelic.com/docs/site/applications-overview
  [실제 사용자 모니터링]: https://newrelic.com/docs/features/real-user-monitoring
  [도움말 찾기]: https://newrelic.com/docs/site/finding-help
