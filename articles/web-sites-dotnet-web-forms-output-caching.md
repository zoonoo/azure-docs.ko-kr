<properties urlDisplayName="details" pageTitle="비디오 센터 세부 정보" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="sdanie" />



# Azure 웹 사이트에서 ASP.NET 웹 양식 출력 캐싱을 사용하는 방법

이 항목에서는 Azure 캐시 서비스(Preview)를 사용하여 ASP.NET Web Forms에 ASP.NET 페이지 출력 캐싱을 지원하는 방법을 설명합니다. 페이지 출력 캐싱은 캐시된 렌더링 페이지를 특정 기간 동안 직접 반환하는 최적화입니다. 이 기능은 페이지의 일반적인 변경 빈도보다 페이지에 액세스하는 빈도가 높은 상황에서 유용합니다. ASP.NET MVC 응용 프로그램에는 페이지 출력 캐싱이 지원되지 않습니다.

캐시 서비스(Preview)는 웹 사이트 외부의 분산 캐싱 서비스를 제공합니다. 이에 따라 웹 사이트의 모든 인스턴스가 동일한 캐시된 페이지 렌더링에 액세스할 수 있습니다.

페이지 출력 캐싱에 캐시 서비스(사전 검토)를 사용하는 기본 단계는 다음과 같습니다.

* [캐시를 만듭니다.](#createcache)
* [Azure 캐시를 사용하도록 ASP.NET 프로젝트를 구성합니다.](#configureproject)
* [web.config 파일을 수정합니다.](#configurewebconfig)
* [출력 캐싱을 사용하여 캐시된 버전의 페이지를 일시적으로 반환합니다.](#useoutputcaching)

<h2><a id="createcache"></a>캐시 만들기</h2>
관리된 캐시 서비스의 캐시 인스턴스는 PowerShell cmdlet을 사용하여 만듭니다. 

>PowerShell cmdlet을 사용하여 만든 관리된 캐시 서비스 인스턴스는 [Azure 관리 포털][]에서 보고 구성할 수 있습니다.

관리된 캐시 서비스 인스턴스를 만들려면 Azure PowerShell 명령 창을 엽니다.

>Azure PowerShell 설치 및 사용에 대한 지침은 [Azure PowerShell을 설치하고 구성하는 방법][]을 참조하세요.

[Add-AzureAccount][] cmdlet을 호출하고 계정과 연결된 메일 주소 및 암호를 입력합니다. [Add-AzureAccount][] cmdlet을 호출하면 구독이 기본적으로 선택되고 표시됩니다. 구독을 변경하려면 [Select-AzureSubscription][] cmdlet을 호출합니다.

>계정의 인증서를 사용하여 Azure PowerShell을 구성한 경우에는 이 단계를 건너뛸 수 있습니다. Azure PowerShell을 Azure 계정과 연결하는 방법에 대한 자세한 내용은 [Azure PowerShell을 설치하고 구성하는 방법][]을 참조하세요.

구독은 기본적으로 선택되고 표시됩니다. 구독을 변경하려면 [Select-AzureSubscription][] cmdlet을 호출합니다.

[New-AzureManagedCache][] cmdlet을 호출하고 캐시의 이름, 지역, 캐시 기능 및 크기를 지정합니다.

**이름**에 캐시 끝점에 사용할 하위 도메인 이름을 입력합니다. 끝점은 6~20자 사이의 문자열이어야 하며, 소문자와 숫자만 포함할 수 있고, 문자로 시작해야 합니다.

**위치**에서 캐시 지역을 지정합니다. 성능을 최적화하려면 캐시 클라이언트 응용 프로그램과 동일한 지역에 캐시를 만드세요.

**Sku**와 **메모리**는 함께 작동하여 캐시 크기를 결정합니다. 관리된 캐시 서비스는 다음 세 가지 계층으로 제공됩니다.

-	기본 - 캐시 크기는 128MB ~ 1GB이고 128MB 단위로 증가하며, 기본 명명된 캐시는 하나입니다.
-	표준 - 캐시 크기는 1GB ~ 10GB이고 1GB 단위로 증가하며, 알림이 지원되고 명명된 캐시는 최대 10개입니다.
-	프리미엄 - 캐시 크기는 5GB ~ 150GB이고 5GB 단위로 증가하며, 알림과 고가용성이 지원되고 명명된 캐시는 최대 10개입니다.

**Sku** 및 응용 프로그램의 요구를 충족하는 **메모리**를 선택합니다. 알림과 고가용성 등 일부 캐시 기능은 특정 캐시 옵션에서만 사용할 수 있습니다. 응용 프로그램에 가장 적합한 캐시 옵션과 크기를 선택하는 방법에 대한 자세한 내용은 [캐시 기능][]을 참조하세요.

 다음 예에서는 미국 남중부 지역에 contosocache라는 기본 128MB 캐시를 만듭니다.

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>캐시를 만들 때 사용할 수 있는 매개 변수 및 값의 전체 목록은 [New-AzureManagedCache][] cmdlet 설명서를 참조하세요.

PowerShell cmdlet을 호출한 후 캐시가 만들어지는 데 몇 분 정도 걸릴 수 있습니다. 캐시가 만들어진 후에는 새 캐시가 `Running` 상태가 되고 기본 설정과 함께 사용할 준비가 완료되며, [Azure 관리 포털][]에서 보고 구성할 수 있습니다.

Azure PowerShell 창에서 만들기 진행률을 모니터링할 수 있습니다. 캐시의 사용 준비가 완료되면 [New-AzureManagedCache][] cmdlet에서 다음 예에 표시된 대로 캐시 정보를 표시합니다.

	PS C:\> Add-AzureAccount
	VERBOSE: Account "user@domain.com" has been added.
	VERBOSE: Subscription "MySubscription" is selected as the default subscription.
	VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
	VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
	PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
	VERBOSE: Intializing parameters...
	VERBOSE: Creating prerequisites...
	VERBOSE: Verify cache service name...
	VERBOSE: Creating cache service...
	VERBOSE: Waiting for cache service to be in ready state...


	Name     : contosocache
	Location : South Central US
	State    : Active
	Sku      : Basic
	Memory   : 128MB



	PS C:\>





다음 섹션에서는 **대시보드** 탭의 설정을 사용하여 ASP.NET 프로젝트의 캐싱을 구성합니다.

<h2><a id="configureproject"></a>ASP.NET 프로젝트 구성</h2>
1. 먼저, [최신][] **Azure SDK for .NET**이 설치되어 있어야 합니다.

2. Visual Studio의 **솔루션 탐색기**에서 ASP.NET 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. WebMatrix를 사용하는 경우 도구 모음의 **NuGet** 단추를 대신 클릭합니다.

3. **온라인 검색** 편집 상자에 **WindowsAzure.Caching**을 입력합니다.

	![NuGetDialog][NuGetDialog]

4. **Azure 캐싱** 패키지를 선택한 후 **설치** 단추를 클릭합니다.

<h2><a id="configurewebconfig"></a>Web.Config 파일 수정</h2>
NuGet 패키지는 캐시에 대한 어셈블리 참조를 만들 뿐 아니라 web.config 파일에 스텁 항목을 추가합니다. ASP.NET 페이지 출력 캐싱에 캐시를 사용하려면 web.config에서 여러 항목을 수정해야 합니다.

1. ASP.NET 프로젝트에 대한 **web.config** 파일을 엽니다.

2. 기존 **caching** 및 **outputCache** 요소가 있는 경우 이 요소를 주석으로 처리(또는 제거)합니다.

3. 그런 다음 Azure 캐싱 NuGet 패키지로 추가되었던 **caching** 요소에 달린 주석을 제거합니다. 최종 결과는 다음 스크린샷과 유사해야 합니다.

	![OutputConfig][OutputConfig]

4. 다음으로 **dataCacheClients** 섹션을 찾습니다. **securityProperties** 하위 요소에 달린 주석을 제거합니다.

	![CacheConfig][CacheConfig]

5. **autoDiscover** 요소에서 캐시의 끝점 URL에 **identifier** 특성을 설정합니다. 끝점 URL을 찾으려면 Azure 관리 포털에서 캐시 속성으로 이동합니다. **대시보드** 탭에서 **간략 상태** 섹션에 **끝점 URL** 값을 복사합니다.

	![EndpointURL][EndpointURL]

6. **messageSecurity** 요소에서 캐시의 액세스 키에 **authorizationInfo** 특성을 설정합니다. 액세스 키를 찾으려면 Azure 관리 포털에서 캐시를 선택합니다. 그런 다음 맨 아래 표시줄에서 **키 관리** 아이콘을 클릭합니다. **기본 액세스 키** 입력란 옆에 복사 단추를 클릭합니다.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>출력 캐싱 사용</h2>
마지막 단계는 ASP.NET Web Forms 응용 프로그램에서 출력 캐싱을 사용하도록 페이지를 구성하는 것입니다. .ASPX 소스의 시작 부분에 **OutputCache** 특성을 추가하면 됩니다. 예를 들면 다음과 같습니다.

	<%@ OutputCache Duration="45" VaryByParam="*" %>

위의 예제는 45초 동안 페이지를 캐시합니다. **VaryByParam**이 "*"로 설정되었으므로 이 캐시된 페이지 출력은 다른 쿼리 매개 변수가 전달되더라도 변경되지 않습니다. 하지만 다음 예는 "UserId" 매개 변수의 각 값에 대해 다른 버전의 페이지를 캐시합니다.

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[installed the latest]: http://www.windowsazure.com/ko-kr/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure 관리 캐시 Cmdlet]: http://go.microsoft.com/fwlink/?LinkID=398555
[Azure PowerShell을 설치 및 구성하는 방법(영문)]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/ko-kr/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/ko-kr/library/dn495203.aspx
[Azure 관리 포털]: https://manage.windowsazure.com/
  
  
  
