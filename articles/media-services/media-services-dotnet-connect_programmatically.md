<properties 
	pageTitle=".NET을 사용하여 Media Services 계정에 연결하기" 
	description="이 항목에서는 .NET을 사용하여 Media Services에 연결하는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


# .NET용 Media Services SDK을 사용하여 미디어 서비스 계정에 연결하기

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 및 [미디어 서비스 라이브 스트리밍 워크플로](media-services-live-streaming-workflow.md) 시리즈의 일부입니다.

이 항목에서는.NET용 Media Services SDK를 프로그래밍할 때 Microsoft Azure Media Services에 프로그래밍 방식의 연결을 가져오는 방법을 설명합니다.


## Media Services에 연결하기

Media Services에 프로그래밍 방식으로 연결하려면 이전에 Azure 계정을 설정하고, 해당 계정에 Media Services를 구성한 다음, .NET용 Media Services SDK를 사용하여 개발을 위한 Visual Studio 프로젝트를 설정해야 합니다. 자세한 내용은 .NET용 Media Services SDK를 사용하여 개발을 위한 설정을 참조하세요.

Media Services 계정 설정 과정이 끝나면 다음과 같은 필수 연결 값을 얻게 됩니다. 이를 사용하여 Media Services에 프로그래밍 방식으로 연결하기

- Media Services 계정 이름.

- Media Services 계정 키.

이러한 값을 찾으려면 Azure 관리 포털을 방문하여 Media Services 계정을 선택하고 포털 창의 하단에 있는 "**키 관리**" 아이콘을 클릭합니다. 각 텍스트 상자 옆에 있는 아이콘을 클릭하면 값을 시스템 클립보드에 복사합니다.


## CloudMediaContext 인스턴스 만들기

서버 컨텍스트를 나타내는 **CloudMediaContext** 인스턴스를 만드는 데 필요한 Media Services에 대한 프로그래밍을 시작합니다. **CloudMediaContext**에는 작업, 자산, 파일, 액세스 정책 및 로케이터를 비롯하여 중요한 컬렉션에 대한 참조가 포함됩니다.

>[AZURE.NOTE]**CloudMediaContext** 클래스는 스레드로부터 안전하게 보호되지 않습니다. 스레드마다 또는 작업 집합마다 새 CloudMediaContext를 만들어야 합니다.


CloudMediaContext에는 5개의 생성자 오버로드가 있습니다. **MediaServicesCredentials**를 매개 변수로 사용하는 생성자를 사용하는 것이 좋습니다. 자세한 내용은 이어지는 **액세스 제어 서비스 토큰 다시 사용**을 참조하세요.

다음 예제에서는 공용 CloudMediaContext(MediaServicesCredentials 자격 증명) 생성자를 사용합니다.

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## 액세스 제어 서비스 토큰 다시 사용

이 섹션에서는 MediaServicesCredentials을 매개 변수로 사용하는 CloudMediaContext 생성자를 사용하여 액세스 제어 서비스 토큰을 다시 사용하는 방법을 보여 줍니다.


[Azure Active Directory 액세스 제어](https://msdn.microsoft.com/library/hh147631.aspx)(액세스 제어 서비스 또는 ACS)는 웹 응용 프로그램에 대한 액세스 권한을 얻기 위해 인증하고 권한을 부여하는 쉬운 방법을 제공하는 클라우드 기반 서비스입니다. Microsoft Azure Media Services는 ACS 토큰을 필요로 하는 OAuth 프로토콜을 통해 서비스에 대한 액세스 권한을 제어합니다. Media Services는 권한 부여 서버에서 ACS 토큰을 받습니다.

Media Services SDK를 사용하여 개발할 때 SDK 코드가 이를 관리하기 때문에 토큰을 처리하지 않도록 선택할 수 있습니다. 하지만 SDK가 불필요한 토큰 요청으로 이어지는 ACS 토큰을 완벽하게 관리하도록 할 수 있습니다. 토큰 요청은 시간이 걸리며 클라이언트 및 서버 리소스를 소모합니다. 뿐만 아니라, 속도가 너무 높은 경우 ACS 서버가 요청을 제한합니다. 초당 30개로 제한하며 [ACS 서비스 제한](https://msdn.microsoft.com/library/gg185909.aspx)을 참조하세요.

Media Services SDK 버전 3.0.0.0부터 ACS 토큰을 다시 사용할 수 있습니다. **MediaServicesCredentials**를 매개 변수로 사용하는 **CloudMediaContext** 생성자는 여러 컨텍스트 사이에서 ACS 토큰을 공유할 수 있습니다. MediaServicesCredentials 클래스는 Media Services 자격 증명을 캡슐화합니다. ACS 토큰을 사용할 수 있으며 해당 만료 시간을 알고 있는 경우, 토큰으로 새 MediaServicesCredentials 인스턴스를 만들고 CloudMediaContext 생성자에 전달할 수 있습니다. 만료될 때마다 Media Services SDK가 토큰을 자동으로 새로 고칩니다. 아래 예에서 보이는 것처럼 ACS 토큰을 다시 사용한느 두 가지 방법이 있습니다.

- 메모리에서 **MediaServicesCredentials** 개체를 캐시할 수 있습니다(예: 정적 클래스 변수). 그런 다음 CloudMediaContext 생성자에 캐시된 개체를 전달합니다. MediaServicesCredentials 개체는 여전히 유효한 경우에 다시 사용할 수 있는 ACS 토큰을 포함합니다. 토큰이 유효하지 않은 경우, MediaServicesCredentials 생성자에 주어진 자격 증명을 사용하여 Media Services SDK를 통해 새로 고침됩니다.

	**MediaServicesCredentials** 개체가 RefreshToken 호출 후 유요한 토큰을 가져옵니다. **CloudMediaContext**는 생성자에서 **RefreshToken** 메서드를 호출합니다. 외부 저장소에 토큰 값을 저장하려는 경우 토큰 데이터를 저장하기 전에 TokenExpiration 값이 유효한지 확인해야 합니다. 유효하지 않으면 캐싱 전에 RefreshToken을 호출 합니다.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- AccessToken 문자열 및 TokenExpiration 값도 캐시할 수 있습니다. 값은 캐시된 토큰 데이터를 새 MediaServicesCredentials 개체로 만들기 위해 나중에 사용할 수 있습니다. 여러 프로세스 또는 컴퓨터 사이에서 토큰을 안전하게 공유할 수 있는 시나리오에 특히 유용합니다.

	다음 코드 조각은 이 예에서 정의되지 않은 SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage 및 UpdateTokenDataInExternalStorageIfNeeded 메서드를 호출합니다. 외부 저장소에서 토큰 데이터를 저장, 검색 및 업데이트하기 위해 이러한 메서드를 정의할 수 있습니다.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	저장된 토큰 값을 사용하여 MediaServicesCredentials를 만듭니다.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	토큰이 미디어 서비스 SDK에서 업데이트된 경우 토큰 복사본을 업데이트합니다.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- 여러 Media Services 계정이 있는 경우(예: 로드 공유 목적 또는 지역 배포) System.Collections.Concurrent.ConcurrentDictionary 컬렉션을 사용하여 MediaServicesCredentials 개체를 캐시할 수 있습니다(ConcurrentDictionary 컬렉션은 여러 스레드에서 동시에 액세스할 수 있는 키/값 쌍의 스레드로부터 안전하게 보호되는 컬렉션을 나타냄). 그런 다음 캐시된 자격 증명을 가져오는 GetOrAdd 메서드를 사용할 수 있습니다.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## 중국 북부 지역에 있는 Media Services 계정에 연결하기

계정이 중국 북부 지역에 있으면 다음 생성자를 사용합니다.

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

예:


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## 구성에서 연결 값 저장

구성에서 연결 값, 사용자 계정 이름 및 암호와 같은 특히 중요한 값을 저장하는 것이 좋습니다. 중요한 구성 데이터를 암호화하는 것도 좋습니다. Windows EFS(파일 시스템 암호화)를 사용하여 전체 구성 파일을 암호화할 수 있습니다. 파일에서 EFS를 사용하려면 파일을 마우스 오른쪽 단추로 클릭하고, **속성**을 선택하여 **고급** 설정 탭에서 암호화를 사용하도록 설정합니다. 또는 보호되는 구성을 사용하여 구성 파일에서 선택한 부분을 암호화하기 위해 사용자 지정 솔루션을 만들 수 있습니다. [보호되는 구성을 사용하여 구성 정보 암호화](https://msdn.microsoft.com/library/53tyfkaw.aspx)를 참조하세요.

다음 App.config 파일에는 필수 연결 값이 포함됩니다. <appSettings> 요소의 값은 Media Services 게정 설정 과정에서 가져온 필수 값입니다.


<pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>

구성에서 연결 값을 검색하려면 **ConfigurationManager** 클래스를 사용한 다음 코드에서 필드에 값을 할당합니다.
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];


<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=July15_HO1-->