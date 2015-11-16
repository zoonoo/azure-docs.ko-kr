<properties
	pageTitle="SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용 | Microsoft Azure"
	description="Azure Active Directory는 SCIM 프로토콜 사양에서 정의된 인터페이스를 가진 웹 서비스가 향하는 응용 프로그램 또는 ID 저장소에 사용자 및 그룹을 자동으로 프로비전합니다. "
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="asmalser-msft"/>

#SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용

##개요

Azure Active Directory는 [SCIM 프로토콜 사양](https://tools.ietf.org/html/draft-ietf-scim-api-19)에서 정의된 인터페이스를 가진 웹 서비스가 향하는 응용 프로그램 또는 ID 저장소에 사용자 및 그룹을 자동으로 프로비전합니다. Azure Active Directory는 이 웹 서비스에 할당된 사용자 및 그룹을 만들고 수정하며 삭제하는 요청을 보내며 이는 대상 ID 저장소의 작업으로 해당 요청을 번역할 수 있습니다.

![][1] *그림: 웹 서비스를 통해 Azure Active Directory에서 임의의 ID 저장소에 프로비전*

이 기능은 Azure AD에서 "고유한 앱 가져오기" 기능과 함께 사용하여 사용자 프로비전 API를 제공하는 거의 모든 응용 프로그램에 대해 Single Sign-On 및 자동 사용자 프로비전을 사용할 수 있습니다

방법은 다음과 같습니다.

1.	Azure AD는 [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)로 명명된 공용 언어 인프라 라이브러리를 제공합니다. 시스템 통합 업체 및 개발자는 이 라이브러리를 사용하여 응용 프로그램 ID 저장소에 Azure AD를 연결할 수 있는 SCIM 기반 웹 서비스 끝점을 만들고 배포할 수 있습니다.
2.	매핑은 웹 서비스에서 구현되어 스키마에 응용 프로그램에서 필요한 사용자 스키마 및 프로토콜에 표준화된 사용자 스키마를 매핑합니다.
3.	끝점 URL은 응용 프로그램 갤러리에서 사용자 지정 응용 프로그램의 일부로 Azure AD에 등록됩니다.
4.	사용자 및 그룹은 Azure AD에서 이 응용 프로그램에 할당됩니다. 할당 시 큐에 저장하여 대상 응용 프로그램에 동기화됩니다. 큐를 처리하는 동기화 프로세스는 5분 마다 실행됩니다.

##코드 샘플

이 과정을 보다 쉽게 하려면 [코드 샘플](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)의 집합이 SCIM 웹 서비스 끝점을 만들고 자동 프로비전을 보여주도록 제공됩니다. 한 가지 샘플은 사용자 및 그룹을 나타내는 쉼표로 구분된 값의 행이 있는 파일을 유지 관리하는 공급자입니다. 다른 샘플은 Amazon 웹 서비스 ID 및 액세스 관리 서비스에서 작동하는 공급자입니다.


###필수 조건
* Visual Studio 2013 이상
* [Azure SDK for .NET](https://azure.microsoft.com/ko-KR/downloads/)
* ASP.NET framework 4.5를 SCIM 끝점으로 사용하도록 지원하는 Windows 컴퓨터입니다. 이 컴퓨터는 클라우드에서 액세스할 수 있어야 합니다.
* [Azure AD Premium의 평가판 또는 사용이 허가된 버전을 사용하여 Azure 구독](https://azure.microsoft.com/ko-KR/services/active-directory/)
* Amazon AWS 샘플은 [Visual Studio용 AWS Toolkit](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html)에서 라이브러리가 필요합니다. 추가 세부 정보는 샘플에 포함된 추가 정보 파일을 참조하세요.

##시작하기

Azure AD에서 프로비전 요청을 수락할 수 있는 SCIM 끝점을 구현하는 가장 쉬운 방법은 쉼표로 구분된 값(CSV) 파일에 프로비전된 사용자를 출력하는 코드 샘플을 빌드하고 배포하는 것입니다.

**샘플 SCIM 끝점을 만들려면:**

1.	[https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)에서 코드 샘플 패키지를 다운로드합니다.
2.	패키지의 압축을 풀고 C:\\AzureAD-BYOA-Provisioning-Samples와 같은 위치에서 Windows 컴퓨터에 넣습니다.
3.	이 폴더에 Visual Studio에 있는 FileProvisioningAgent 솔루션을 시작합니다.
4.	**도구 > 라이브러리 패키지 관리자 > 패키지 관리자 콘솔**을 선택하고 FileProvisioningAgent 프로젝트에 다음과 같은 명령을 실행하여 솔루션 참조를 확인합니다.

    Install-Package Microsoft.SystemForCrossDomainIdentityManagement Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory Install-Package Microsoft.Owin.Diagnostics Install-Package Microsoft.Owin.Host.SystemWeb

5.	FileProvisioningAgent 프로젝트를 빌드합니다.
6.	Windows에서 명령 프롬프트 응용 프로그램을 시작하고(관리자로) **cd** 명령을 사용하여 디렉터리를 **\\AzureAD-BYOA-Provisioning-Samples\\ProvisioningAgent\\bin\\Debug** 폴더로 변경합니다.
7.	아래의 명령을 실행하여 <ip-address>을 Windows 컴퓨터의 IP 또는 도메인 이름으로 바꿉니다.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.	Windows의 **Windows 설정 > 네트워크 및 인터넷 설정**에서 **Windows 방화벽 > 고급 설정**을 선택하고 포트 9000에 인바운드 액세스를 허용하는 **인바운드 규칙**을 만듭니다.
9.	Windows 컴퓨터가 라우터 뒤에 있는 경우 라우터는 인터넷에 노출되는 포트 9000과 Windows 컴퓨터에 있는 포트 9000 사이의 네트워크 액세스 변환을 수행하도록 구성되어야 합니다. Azure AD의 경우 클라우드에서 이 끝점에 액세스할 수 있으려면 이것이 필요합니다.


**Azure AD에서 샘플 SCIM 끝점을 등록하려면:**

1.	웹 브라우저에서 https://manage.windowsazure.com에 Azure 관리 포털을 시작합니다.
2.	**Active Directory > 디렉터리 > [사용자의 디렉터리] > 응용 프로그램**으로 이동하고 **추가 > 갤러리에서 응용 프로그램 추가**를 선택합니다.
3.	왼쪽에서 **사용자 지정** 탭을 선택하고 "SCIM 테스트 앱"과 같은 이름을 입력한 다음 확인 표시 아이콘을 클릭하여 앱 개체를 만듭니다. 만든 응용 프로그램 개체는 SCIM 끝점 뿐만 아니라 Single Sign-On을 프로비전하고 구현하는 대상 앱을 나타내도록 합니다.

![][2]

4.	결과 화면에서 두 번째 구성 계정 프로비전 단추를 선택합니다.
5.	대화 상자에서 인터넷에 노출된 URL 및 프로그램 SCIM 끝점의 포트를 입력합니다. <ip-address>이 인터넷 노출된 IP 주소인 경우 http://testmachine.contoso.com:9000 또는 http://<ip-address>:9000/와 같은 것입니다.  
6.	**다음**을 클릭하고 **테스트 시작** 단추를 클릭하여 SCI 끝점에 연결하는 데 Azure Active Directory를 시도합니다. 시도가 실패하는 경우 진단 정보가 표시됩니다.  
7.	웹 서비스에 연결하려는 시도가 성공하면 남아 있는 화면에서 **다음**을 클릭한 다음 **완료**를 클릭하여 대화 상자를 종료합니다.
8.	결과 화면에서 세 번째 **계정 할당** 단추를 선택합니다. 사용자 및 그룹 결과 섹션에서 응용 프로그램에 프로비전하려는 사용자 또는 그룹을 할당합니다.
9.	사용자 및 그룹이 할당되면 화면 위쪽의 **구성** 탭을 클릭합니다.
10.	**계정 프로비전**에서 상태가 켜기로 설정되었는지 확인합니다. 
11.	**도구**에서 **계정 프로비전 다시시작**을 클릭하여 프로비전 프로세스를 시작합니다.

프로비전 프로세스가 SCIM 끝점에 요청을 보내려고 시작하기 전에 5-10분이 경과될 수 있습니다. 연결 시도의 요약은 응용 프로그램의 대시보드 탭에서 제공되고 프로비전 활동에 대한 보고서 및 프로비전 오류는 모두 디렉터리의 보고서 탭에서 다운로드할 수 있습니다.

이 샘플을 확인하는 마지막 단계는 Windows 컴퓨터에서 \\AzureAD-BYOA-Provisioning-Samples\\ProvisioningAgent\\bin\\Debug 폴더에 TargetFile.csv 파일을 여는 것입니다. 프로비전 프로세스가 실행되면 이 파일은 할당되고 프로비전된 모든 사용자 및 그룹의 세부 사항을 표시합니다.

##개발 라이브러리

SCIM 사양을 준수하는 웹 서비스를 개발하려면 먼저 개발 프로세스를 가속화하기 위해 Microsoft에서 제공하는 다음 라이브러리를 숙지합니다.

**1:** 공용 언어 인프라 라이브러리는 C#과 같은 해당 인프라에 따라 언어와 함께 사용하기 위해 제공됩니다. 이러한 라이브러리 중 하나인 [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)는 아래 그림에 표시된 인터페이스 Microsoft.SystemForCrossDomainIdentityManagement.IProvider를 선언합니다. 라이브러리를 사용하는 개발자는 일반적으로 공급자로 참조될 수 있는 클래스를 사용하여 해당 인터페이스를 구현합니다. 라이브러리를 사용하면 개발자는 SCIM 사양을 준수하는 웹 서비스를 쉽게 배포할 수 있으며 이는 인터넷 정보 서비스 또는 실행 가능한 공용 언어 인프라 어셈블리 내에서 호스팅됩니다. 해당 웹 서비스에 대한 요청은 공급자의 메서드에 호출로 해석되며 이는 개발자에 의해 일부 ID 저장소에서 작동하도록 프로그래밍됩니다.

![][3]

**2:** [Express 경로 처리기](http://expressjs.com/guide/routing.html)는 (SCIM 사양에 정의된) 호출을 나타내는 node.js 요청 개체를 구문 분석하는 데 사용할 수 있는습니다.

##사용자 지정 SCIM 끝점 빌드

위에서 설명한 라이브러리를 사용하여 해당 라이브러리를 사용하는 개발자는 실행 가능한 공용 언어 인프라 어셈블리 또는 인터넷 정보 서비스 내에서 해당 서비스를 호스팅할 수 있습니다. 다음은 http://localhost:9000 주소에 실행 가능한 어셈블리 내에서 서비스를 호스팅하기 위한 샘플 코드입니다.

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

이 서비스는 다음 중 하나의 루트 인증 기관에 속한 HTTP 주소 및 서버 인증 인증서를 가지고 있어야 한다는 점이 중요합니다.

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

서버 인증 인증서는 네트워크 셸 유틸리티를 사용하여 다음과 같이 Windows 호스트의 포트에 바인딩될 수 있습니다.

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
여기에서 appid 인수에 제공된 값이 임의의 전역 고유 식별자인 반면 certhash 인수에 제공된 값은 인증서의 지문입니다.

인터넷 정보 서비스 내에서 서비스를 호스팅하려면 개발자는 어셈블리의 기본 네임스페이스의 설치로 명명된 클래스를 사용하여 공용 언어 인프라 코드 라이브러리 어셈블리를 빌드합니다. 이러한 클래스의 샘플은 다음과 같습니다.

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

##끝점 인증 처리

Azure Active Directory에서 요청은 OAuth 2.0 전달자 토큰을 포함합니다. 요청을 받는 모든 서비스는 예상된 Azure Active Directory 테넌트 대신 Azure Active Directory의 Graph 웹 서비스에 액세스하는 데 대해 발급자를 Azure Active Directory로 인증해야 합니다. 토큰에서 발급자는 "iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"와 같은 iss 클레임으로 식별됩니다. 이 예제에서 상대 주소 세그먼트인 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422가 토큰이 발급된 대신 Azure Active Directory 테넌트의 고유한 발급자인 반면 클레임 값의 기본 주소인 https://sts.windows.net는 Azure Active Directory를 발급자로 식별합니다. 토큰이 Azure Active Directory의 Graph 웹 서비스에 액세스하기 위해 발급되었다면 해당 서비스의 실별자인 00000002-0000-0000-c000-000000000000는 토큰의 aud 클레임의 값에 있어야 합니다.

SCIM 서비스 구축을 위해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하는 개발자는 Microsoft.Owin.Security.ActiveDirectory 패키지로 다음 단계를 수행하여 Azure Active Directory에서 요청을 인증할 수 있습니다.

**1:** 공급자에서 서비스가 시작할 때 마다 호출되는 메서드를 반환함으로써 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 속성을 구현합니다.

    public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:** 해당 메서드에 다음 코드를 추가하여 지정된 테넌트 대신 인증된 서비스의 끝점 중 하나가 Azure Active Directory의 Graph 웹 서비스에 액세스하는 데 대해 Azure Active Directory에서 발급한 토큰을 갖도록 요청합니다.

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##사용자 및 그룹 스키마

Azure Active Directory는 두 형식의 리소스를 SCIM 웹 서비스에 프로비전할 수 있습니다. 이러한 형식의 리소스는 사용자 및 그룹입니다.

사용자 리소스는 스키마 식별자인 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User로 식별되고 이는 이 프로토콜 사양에 포함됩니다. http://tools.ietf.org/html/draft-ietf-scim-core-schema urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 리소스의 특성에 Azure Active Directory에서 사용자의 특성을 기본 매핑한 작업은 아래 테이블 1에서 제공됩니다.

그룹 리소스는 스키마 식별자로 식별됩니다. http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group 아래 테이블 2에서는 Azure Active Directory에서 그룹의 특성을 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group 리소스의 특성에 기본 매핑한 작업을 보여줍니다.

###테이블 1: 기본 사용자 특성 매핑

| Azure Active Directory 사용자 | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | 활성 |
| displayName | displayName |
| Facsimile-TelephoneNumber | phoneNumbers[type eq "fax"].value |
| givenName | name.givenName |
| jobTitle | title |
| mail | emails[type eq "work"].value |
| mailNickname | externalId |
| manager | manager |
| mobile | phoneNumbers[type eq "mobile"].value |
| objectId | id |
| postalCode | addresses[type eq "work"].postalCode |
| proxy-Addresses | emails[type eq "other"].Value |
| physical-Delivery-OfficeName | addresses[type eq "other"].Formatted |
| streetAddress | addresses[type eq "work"].streetAddress |
| surname | name.familyName |
| telephone-Number | phoneNumbers[type eq "work"].value |
| user-PrincipalName | userName |


###테이블 2: 기본 그룹 특성 매핑

| Azure Active Directory 그룹 | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | externalId |
| mail | emails[type eq "work"].value |
| mailNickname | displayName |
| members | members |
| objectId | id |
| proxyAddresses | emails[type eq "other"].Value |


##사용자 프로비전 및 프로비전 해제

아래 그림은 다른 ID 저장소에 사용자의 수명 주기를 관리하도록 Azure Active Directory가 SCIM 서비스를 보낸다는 메세지를 보여줍니다. 또한 다이어그램은 이러한 서비스 구축을 위해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 구현된 SCIM 서비스가 이러한 요청을 어떻게 공급자의 메서드에 호출로 번역하는지를 보여줍니다.

![][4] 그림: 사용자 프로비전 및 시퀀스 프로비전 해제

**1:** Azure Active Directory는 externalId 특성 값이 Azure Active Directory에 있는 사용자의 mailNickname 특성 값과 일치하는 사용자를 위해 서비스를 쿼리합니다. jyoung이 Azure Active Directory에서 사용자의 mailNickname의 샘플인 것처럼 쿼리는 Hypertext Transfer Protocol 요청으로 표현됩니다.

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 쿼리 메서드 호출로 번역됩니다. 해당 메서드의 서명은 다음과 같습니다.

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 인터페이스의 정의는 다음과 같습니다.

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
	{
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
	  { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

앞서 언급한 externalId 특성에 대해 특정된 값을 사용하는 사용자에 대한 쿼리의 샘플의 경우 쿼리 메서드에 전달된 인수의 값은 다음과 같습니다.

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

**2:** Azure Active Directory에서 사용자의 mailNickname 특성 값과 일치하는 externalId 특성 값을 사용하는 사용자에 대한 서비스의 쿼리에 응답할 때 사용자를 반환하지 않는 경우 Azure Active Directory는 서비스가 Azure Active Directory의 사용자에 해당하는 사용자를 프로비전하도록 요청합니다. 다음은 그러한 요청의 예제입니다.

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리는 요청이 서비스 공급자의 만들기 메서드에 호출을 요청하도록 번역됩니다. 만들기 매서드에는 다음 서명이 있습니다.

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

사용자를 프로비전할 요청의 경우 리소스 인수의 값은 Microsoft.SystemForCrossDomainIdentityManagement의 인스턴스입니다. Microsoft.SystemForCrossDomainIdentityManagement.Schemas 라이브러리에 정의된 Core2EnterpriseUser 클래스입니다. 사용자를 프로비전하는 요청인 성공하는 경우 메서드의 구현은 Microsoft.SystemForCrossDomainIdentityManagement의 인스턴스를 반환할 것으로 예상됩니다. 새로 프로비전된 사용자의 고유 식별자에 설정된 식별자 속성의 값을 가진 Core2EnterpriseUser 클래스입니다.

**3:** SCIM가 향하는 ID 저장소에 있는 것으로 알려진 사용자를 업데이트하려면 Azure Active Directory는 서비스에서 이 이와 같은 요청으로 해당 사용자의 현재 상태를 요청하여 진행합니다.

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성된 서비스에서 요청은 서비스 공급자의 검색 메서드에 호출하도록 번역됩니다. 해당 검색 메서드의 서명은 다음과 같습니다.

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

앞서 언급한 사용자의 현재 상태를 검색하는 요청의 예에서 매개 변수 인수 값으로 제공되는 개체의 속성 값은 다음과 같습니다.

* 식별자: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:** 참조 특성이 업데이트될 경우 Azure Active Directory는 서비스를 쿼리하여 서비스에서 이미 향하는 ID 저장소의 참조 특성의 현재 값이 Azure Active Directory의 해당 특성 값과 일치하는지를 확인합니다. 사용자의 경우 현재 값이 이 방식으로 쿼리된 유일한 특성은 관리자 특성입니다. 특정 사용자 개체의 관리자 특성 값에 현재 특정 값이 있는지 여부를 결정하는 요청의 예는 다음과 같습니다.

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

특성 쿼리 매개 변수인 ID의 값은 필터 쿼리 매개 변수의 값으로 제공되는 식을 만족하는 사용자 개체가 있다는 것을 의미하고 서비스는 해당 리소스 ID 특성의 값을 포함하는 urn:ietf:params:scim:schemas:core:2.0:User or urn:ietf:params:scim:schemas:extension:enterprise:2.0:User 리소스를 사용하여 응답할 것으로 예상됩니다. 물론 ID 특성의 값은 필터 쿼리 매개 변수 값에 포함되기 때문에 요청자에게 알려집니다—이 값을 묻는 목적은 실제로 이러한 개체의 존재 여부를 확인하는 필터 식을 만족하는 리소스의 최소 표현을 요청하기 위해서 입니다.

서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 쿼리 메서드 호출로 번역됩니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다.

* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "id"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

여기에서 필터 쿼리 매개 변수 식의 순서에 따라 인덱스 x의 값은 0이고 인덱스 y의 값은 1일 수 있습니다. 또는 x 값이 1이고 y 값이 0일 수 있습니다.

**5:** 다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다.

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

SCIM 서비스 구현에 대한 Microsoft 공용 언어 인프라 라이브러리는 요청이 서비스 공급자의 업데이트 메서드에 호출을 요청하도록 번역됩니다. 해당 메서드의 서명은 다음과 같습니다.

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



앞서 언급한 사용자를 업데이트하는 요청의 예에서 패치 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다.

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

**6:** SCIM 서비스가 향하는 ID 저장소에서 사용자의 프로비전을 해제하려면 Azure Active Directory가 이와 같은 요청을 보냅니다.

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
	
서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 삭제 메서드 호출로 번역됩니다. 해당 매서드에는 다음 서명이 있습니다.

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
앞서 언급한 사용자의 프로비전을 해제하는 요청의 예에서 resourceIdentifier 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다.

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

##그룹 프로비전 및 프로비전 해제

아래 그림은 다른 ID 저장소에 그룹의 수명 주기를 관리하도록 Azure Active Directory가 SCIM 서비스를 보낸다는 메세지를 보여줍니다. 이러한 메시지는 세 가지 부분에서 사용자에 관련된 메시지가 다릅니다.

* 그룹 리소스의 스키마는 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group로 식별됩니다.  
* 그룹을 검색하는 요청은 멤버 특성이 요청에 대한 응답에서 제공된 리소스로부터 제외된다고 규정합니다.  
* 참조 특성에 특정 값에 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![][5] 그림: 사용자 프로비전 및 시퀀스 프로비전 해제

	
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG

<!---HONumber=Nov15_HO2-->