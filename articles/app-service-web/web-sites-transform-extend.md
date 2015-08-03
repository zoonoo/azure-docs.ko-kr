<properties
	pageTitle="Azure 앱 서비스 웹 앱 고급 구성 및 확장"
	description="XDT(XML 문서 변환) 선언을 사용하여 Azure 앱 서비스 웹 앱에서 ApplicationHost.config 파일을 변환하고 개인 확장을 추가하여 사용자 지정 관리 작업을 사용하도록 설정할 수 있습니다."
	authors="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015"
	ms.author="cephalin"/>

# Azure 앱 서비스 웹 앱 고급 구성 및 확장

[XDT(XML 문서 변환)](http://msdn.microsoft.com/library/dd465326.aspx) 선언을 사용하여 Azure 앱 서비스의 웹앱에서 [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) 파일을 변환할 수 있습니다. XDT 선언을 사용하여 사용자 지정 웹 앱 관리 작업을 가능하게 하는 개인 확장을 추가할 수도 있습니다. 이 문서에는 웹 인터페이스를 통해 PHP 설정을 관리할 수 있는 샘플 PHP Manager 웹 앱 확장이 포함되어 있습니다.

##<a id="transform"></a>ApplicationHost.config를 통한 고급 구성
앱 서비스 플랫폼에서는 융통성 있게 웹 앱 구성을 제어할 수 있습니다. 앱 서비스에서 직접적인 편집에 표준 IIS ApplicationHost.config 구성 파일을 사용할 수 없지만, 이 플랫폼은 XDT(XML 문서 변환)를 기반으로 하여 선언적인 ApplicationHost.config 변환 모델을 지원합니다.

이 변환 기능을 활용하려면 XDT 콘텐츠를 사용하여 ApplicationHost.xdt 파일을 만들어 웹 앱 루트에 배치합니다. 변경 내용을 적용하려면 웹 앱을 다시 시작해야 할 수 있습니다.

다음 applicationHost.xdt 샘플은 PHP 5.4를 사용하는 웹 앱에 새로운 사용자 지정 환경 변수를 추가하는 방법을 보여 줍니다.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


변환 상태가 포함된 로그 파일 및 자세한 정보는 FTP 루트의 LogFiles\Transform 아래에서 사용할 수 있습니다.

추가 샘플은 [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)(영문)를 참조하십시오.

**참고**<br /> `system.webServer` 아래에 있는 모듈 목록의 요소를 제거하거나 요소의 순서를 바꿀 수 없지만 목록에 추가할 수는 있습니다.


##<a id="extend"></a> 웹앱 확장

###<a id="overview"></a> 개인 웹앱 확장 개요

앱 서비스는 관리 작업에 대한 확장성 지점으로 웹 앱 확장을 지원합니다. 실제로 일부 앱 서비스 플랫폼 기능은 사전 설치된 확장으로 구현됩니다. 사전 설치된 플랫폼 확장은 수정할 수 없는 반면, 고유 웹 앱의 개인 확장은 만들고 구성할 수 있습니다. 또한 이 기능은 XDT 선언이 있어야 사용할 수 있습니다. 개인 웹 앱 확장을 만드는 주요 단계는 다음과 같습니다.

1. 웹앱 확장 **콘텐츠**: 앱 서비스에서 지원되는 웹 응용 프로그램 만들기
2. 웹앱 확장 **선언**: ApplicationHost.xdt 파일 만들기
3. 웹앱 확장 **배포**: SiteExtensions 폴더의 `root` 아래에 콘텐츠 넣기

웹 앱의 내부 링크는 ApplicationHost.xdt 파일에 지정된 응용 프로그램 경로의 상대 경로를 가리켜야 합니다. ApplicationHost.xdt 파일이 변경되면 웹 앱을 재순환해야 합니다.

**참고**: 이 주요 요소에 대한 자세한 내용은 [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)를 참조하세요.

개인 웹 앱 확장을 만들고 사용하도록 설정하는 단계를 설명하는 자세한 예제가 포함되어 있습니다. 다음에 나오는 PHP Manager 예제의 소스 코드는 [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager)(영문)에서 다운로드할 수 있습니다.

###<a id="SiteSample"></a> 웹앱 확장 예제: PHP Manager

PHP Manager는 웹 앱 관리자가 PHP .ini 파일을 직접 수정할 필요 없이 웹 인터페이스에서 쉽게 PHP 설정을 보고 구성할 수 있게 하는 웹 앱 확장입니다. PHP의 일반적인 구성 파일에는 프로그램 파일 아래에 있는 php.ini 파일 및 웹 앱의 루트 폴더에 있는 .user.ini 파일이 포함됩니다. php.ini 파일은 앱 서비스 플랫폼에서 직접 편집할 수 없기 때문에 PHP Manager 확장에서 .user.ini 파일을 사용하여 설정 변경을 적용합니다.

####<a id="PHPwebapp"></a> PHP Manager 웹 응용 프로그램

다음은 PHP Manager 배포의 홈페이지입니다.

![TransformSitePHPUI][TransformSitePHPUI]

웹 앱 확장은 사이트의 루트 폴더에 있는 추가 ApplicationHost.xdt 파일 외에는 일반적인 웹 앱과 같습니다. ApplicationHost.xdt 파일에 대한 자세한 내용은 이 문서의 다음 섹션에서 확인할 수 있습니다.

PHP Manager 확장은 Visual Studio ASP.NET MVC 4 웹 응용 프로그램 템플릿을 사용하여 만들어졌습니다. 다음은 솔루션 탐색기의 보기로, PHP Manager 확장의 구조를 보여 줍니다.

![TransformSiteSolEx][TransformSiteSolEx]

파일 I/O에 필요한 유일한 특수 논리는 웹 앱의 wwwroot 디렉터리가 있는 위치를 나타내는 것입니다. 다음 코드 예제에서 환경 변수 "HOME"은 웹 앱의 루트 경로를 나타내며 wwwroot 경로는 "site\wwwroot"를 추가하여 구성할 수 있음을 알 수 있습니다.

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot.user.ini");
    		return userSettingsFile;
	}


디렉터리 경로를 설정한 후에는 일반적인 파일 I/O 작업을 사용하여 파일을 읽고 파일에 쓸 수 있습니다.

웹 앱 확장에서 주의할 점은 내부 링크 처리와 관련이 있습니다. HTML 파일에 웹 앱 내부 링크에 대한 절대 경로를 제공하는 링크가 있는 경우에는 이 링크 앞에 루트로 확장 이름을 추가해야 합니다. 확장의 루트가 이제 단순히 "/"만이 아니라 "/`[your-extension-name]`/"이기 때문에 이 과정을 통해 내부 링크를 함께 업데이트해야 합니다. 예를 들어 코드에 다음에 대한 링크가 포함되어 있다고 가정하겠습니다.

`"<a href="/Home/Settings">PHP Settings</a>"`

링크가 웹 앱 확장의 일부인 경우 이 링크는 다음과 같은 형태여야 합니다.

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

이 요구 사항을 따르지 않으려면 웹 응용 프로그램 내에서 상대 경로만 사용하거나 ASP.NET 응용 프로그램의 경우 적절한 링크를 만드는 `@Html.ActionLink` 메서드를 사용하면 됩니다.

####<a id="XDT"></a> applicationHost.xdt 파일

웹앱 확장용 코드는 %HOME%\SiteExtensions[확장 이름] 아래에 위치합니다. 이를 확장 루트라고 합니다.

applicationHost.config 파일에 웹 앱 확장을 등록하려면 확장 루트에 ApplicationHost.xdt라는 파일을 배치해야 합니다. ApplicationHost.xdt 파일의 내용은 다음과 같습니다.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

확장 이름으로 선택하는 이름은 확장 루트 폴더와 이름이 같아야 합니다.

이렇게 하면 `system.applicationHost` 사이트 목록에서 SCM 사이트 아래에 새 응용 프로그램 경로를 추가하는 효과가 있습니다. SCM 사이트는 특정 액세스 자격 증명이 사용되는 사이트 관리 끝점입니다. 여기에는 URL `https://[your-site-name].scm.azurewebsites.net`이 포함됩니다.

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> 웹앱 확장 배포

웹앱 확장을 설치하려면 확장을 설치할 웹앱의 `\SiteExtensions[your-extension-name]` 폴더에 웹 응용 프로그램의 모든 파일을 FTP를 사용하여 복사할 수 있습니다. 이 위치에 ApplicationHost.xdt 파일도 복사해야 합니다. 확장을 사용하려면 웹 앱을 다시 시작합니다.

다음 주소에서 웹 앱 확장을 볼 수 있습니다.

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

이 URL은 HTTPS가 사용되며 ".scm"이 포함된 것을 제외하고는 웹 앱의 URL과 같습니다.

키 `WEBSITE_PRIVATE_EXTENSIONS` 및 `0` 값으로 앱 설정을 추가하여 배포 및 확인 중에 웹앱의 모든 개인(사전 설치 아님) 확장을 사용하지 않도록 설정할 수 있습니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

<!---HONumber=July15_HO4-->