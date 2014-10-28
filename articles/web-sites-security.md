<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Azure 웹 사이트에서 웹 응용 프로그램 보안 유지

웹 응용 프로그램을 개발할 때 한 가지 해결 과제는 고객에게 보안이 유지되는 안전한 서비스를 제공하는 방법입니다. 이 문서에서는 웹 응용 프로그램 보안을 유지할 수 있는 Azure 웹 사이트의 기능에 대해 알아봅니다.

> [WACOM.NOTE] 이 문서에서는 웹 기반 응용 프로그램의 보안 고려 사항을 모두 다루지는 않습니다. 웹 응용 프로그램 보안 유지를 위한 기본적인 추가 지침을 보려면 [OWASP(Open Web Application Security Project)][OWASP(Open Web Application Security Project)](영문)에서 OWASP 구성원이 결정하는 최신 상위 10가지 중대 웹 응용 프로그램 보안 결함을 나열하는 [상위 10대 프로젝트][상위 10대 프로젝트]를 참조하세요.

### 목차

-   [보안 통신][보안 통신]
-   [보안 개발][보안 개발]
-   [다음 단계][다음 단계]

## <a name="https"></a>보안 통신

웹 사이트에 대해 만들어진 ***.azurewebsites.net** 도메인 이름을 사용하는 경우 모든* ***.azurewebsites.net** 도메인 이름에 대해 SSL 인증서가 제공되므로 HTTPS를 즉시 사용할 수 있습니다. 사이트에서 [사용자 지정 도메인 이름][사용자 지정 도메인 이름]을 사용하는 경우 SSL 인증서를 업로드하여 해당 사용자 지정 도메인에 대해 HTTPS를 사용하도록 설정할 수 있습니다.

자세한 내용은 [Azure 웹 사이트에 HTTPS 사용][Azure 웹 사이트에 HTTPS 사용]을 참조하세요.

### HTTPS 적용

Azure 웹 사이트는 HTTPS를 적용하지 *않습니다*. 따라서 방문자는 HTTP를 사용하여 사이트를 계속 액세스할 수 있으며 이로 인해 중요한 정보가 노출될 수 있습니다. HTTPS를 적용하려면 **URL 다시 쓰기** 모듈을 사용하세요. URL 다시 쓰기 모듈은 Azure 웹 사이트에 포함되어 있으며 응용 프로그램으로 요청을 전달하기 전에 들어오는 요청에 적용되는 규칙을 정의할 수 있도록 합니다. Azure 웹 사이트에서 지원하는 모든 프로그래밍 언어로 작성한 응용 프로그램에 이 모듈을 사용할 수 있습니다.

> [WACOM.NOTE] .NET MVC 응용 프로그램은 URL 다시 쓰기 대신 [RequireHttps][RequireHttps] 필터를 사용해야 합니다. RequireHttps 필터 사용에 대한 자세한 내용은 [Azure 웹 사이트에 보안 ASP.NET MVC 5 앱 배포][Azure 웹 사이트에 보안 ASP.NET MVC 5 앱 배포]를 참조하세요.
>
> 다른 프로그래밍 언어 및 프레임워크를 사용한 요청의 프로그래밍 방식 리디렉션에 대한 자세한 내용은 해당 기술에 대한 설명서를 참조하세요.

URL 다시 쓰기 규칙은 응용 프로그램 루트에 저장된 **web.config** 파일에 정의되어 있습니다. 다음 예에는 들어오는 모든 트래픽이 강제로 HTTPS를 사용하도록 하는 기본 URL 다시 쓰기 규칙이 포함되어 있습니다.

<a name="example"></a>**URL 다시 쓰기 예제 Web.Config**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

이 규칙은 사용자가 HTTP를 사용하여 페이지를 요청할 때 HTTP 상태 코드 of 301(영구 리디렉션)을 반환하여 작동합니다. 301은 요청을 방문자가 요청한 것과 같은 URL로 리디렉션하지만 요청의 HTTP 부분을 HTTPS로 바꿉니다. 예를 들어 <HTTP://contoso.com>은 <HTTPS://contoso.com>으로 리디렉션됩니다.

> [WACOM.NOTE] 응용 프로그램이 **Node.js**, **PHP**, **Python Django** 또는 **Java**로 작성되면 web.config 파일이 포함되지 않을 확률이 높습니다. 그렇지만 **Node.js**, **Python Django** 및 **Java**는 Azure 웹 사이트에 호스트될 때 실제 web.config를 사용합니다. Azure는 배포 중에 이 파일을 자동으로 만들기 때문에 사용자가 이 파일을 직접 확인할 수는 없습니다. 응용 프로그램의 일부로 이 파일을 하나 포함할 경우 Azure가 자동으로 생성하는 파일이 재정의됩니다.

### .NET

.NET 응용 프로그램의 경우 응용 프로그램에 대한 web.config 파일을 수정하고 [example][example]의 **\<rewrite\>** 섹션을 **\<system.WebServer\>** 섹션에 추가합니다.

web.config 파일에 이미 **\<rewrite\>** 섹션이 포함되어 있으면 [example][example]의 the **\<rule\>**을 **\<rules\>** 섹션의 첫 번째 항목으로 추가합니다.

### PHP

PHP 응용 프로그램의 경우 [example][example]을 응용 프로그램 루트에 web.config 파일로 저장한 다음, 응용 프로그램을 Azure 웹 사이트로 다시 배포하기만 하면 됩니다.

### Node.js, Python Django 및 Java

Node.js, Python Django 및 Java 앱이 아직 web.config 파일을 제공하지 않으면 이러한 앱에 대해 이 파일이 자동으로 만들어지지만 배포 중에 만들어지므로 서버에만 존재합니다. 자동으로 생성된 이 파일에는 Azure에 응용 프로그램 호스트 방법을 알려주는 설정이 포함되어 있습니다.

웹 사이트에서 자동으로 생성된 파일을 검색하고 수정하려면 다음 단계를 사용하세요.

1.  FTP를 사용하여 파일을 다운로드합니다. [FTP를 통해 파일 업로드/다운로드 및 진단 로그 수집][FTP를 통해 파일 업로드/다운로드 및 진단 로그 수집](영문)을 참조하세요.

2.  응용 프로그램 루트에 파일을 추가합니다.

3.  다음 정보를 사용하여 다시 쓰기 규칙을 추가합니다.

    -   **Node.js 및 Python Django**

        Node.js 및 Python Django 응용 프로그램용으로 생성된 web.config 파일에는 사이트의 적절한 작동에 필요한 **\<rule\>** 항목이 포함된 **\<rewrite\>** 섹션이 이미 있습니다. 강제로 이 사이트에서 HTTPS를 사용하게 하려면 예제의 **\<rule\>**을 **\<rules\>** 섹션의 첫 번째 항목으로 추가합니다. 이렇게 하면 나머지 규칙은 그대로 유지되면서 HTTPS가 강제로 적용됩니다.

    -   **Java**

        Apache Tomcat을 사용하는 Java 응용 프로그램용 web.config 파일에는 **\<rewrite\>** 섹션이 포함되어 있지 않으므로 예제의 **\<rewrite\>** 섹션을 **\<system.webServer\>** 섹션에 추가해야 합니다.

4.  Azure에 프로젝트(업데이트된 web.config 포함) 다시 배포

HTTPS를 강제로 적용하기 위한 다시 쓰기 규칙을 사용하여 web.config를 배포하면 즉시 효과를 발휘하여 모든 요청이 HTTPS로 리디렉션됩니다.

IIS URL 다시 쓰기 모듈에 대한 자세한 내용은 [URL 다시 쓰기][URL 다시 쓰기](영문) 설명서를 참조하세요.

## <a name="develop"></a>보안 개발

### 게시 프로필 및 게시 설정

**Visual Studio**, **Web Matrix**, **Azure PowerShell** 또는 Azure **플랫폼 간 명령줄 인터페이스**와 같은 유틸리티를 사용하여 응용 프로그램을 개발하거나, 관리 작업을 수행하거나, 작업을 자동화할 경우 *게시 설정* 파일 또는 *게시 프로필*을 사용할 수 있습니다. 두 항목 모두 Azure에서 사용자를 인증하고 무단 액세스를 방지하도록 보호합니다.

-   **게시 설정** 파일에 포함된 내용

    -   Azure 구독 ID

    -   *계정 이름이나 암호를 제공할 필요 없이* 구독에 대해 관리 작업을 수행할 수 있도록 하는 관리 인증서

-   **게시 프로필** 파일에 포함된 내용

    -   Azure 웹 사이트에 게시하기 위한 정보

게시 설정 또는 게시 프로필을 사용하는 유틸리티를 사용하는 경우 게시 설정이나 프로필을 포함하는 파일을 해당 유틸리티로 가져온 다음 파일을 **삭제**합니다. 프로젝트에서 함께 작업하는 다른 사람들과 공유하기 위해 이 파일을 유지해야 하는 경우 **암호화된** 디렉터리와 같은 보안 위치에 엄격한 사용 권한을 지정하여 저장합니다.

또한 가져온 자격 증명의 보안이 유지되도록 해야 합니다. 예를 들어 **Azure PowerShell** 및 the **Azure 플랫폼 간 명령줄 인터페이스**는 둘 다 **홈 디렉터리**(Linux 또는 OS X 시스템의 경우 *~*, Windows 시스템의 경우 */users/yourusername*)에 저장합니다. 추가 보안을 위해 운영 체제에서 사용할 수 있는 암호화 도구를 사용하여 이러한 위치를 **암호화**할 수 있습니다.

### 구성 설정 및 연결 문자열

연결 문자열, 인증 자격 증명 및 기타 민감한 정보를 구성 파일에 저장하는 것이 일반적인 방식입니다. 불행히도 이러한 파일은 웹 사이트에 노출되거나 공용 저장소에 체크 인되어 이 정보가 노출될 수 있습니다.

Azure 웹 사이트에서 구성 정보를 **앱 설정** 및 **연결 문자열**과 같은 웹 사이트 런타임 환경의 일부로 저장할 수 있습니다. 값은 대부분의 프로그래밍 언어에서 *환경 변수*를 통해 런타임에 응용 프로그램에 노출됩니다. .NET 응용 프로그램의 경우에는 이러한 값이 런타임에 .NET 구성에 주입됩니다.

**앱 설정** 및 **연결 문자열**은 Azure 관리 포털 또는 PowerShell이나 Azure 플랫폼 간 명령줄 인터페이스와 같은 유틸리티를 사용하여 구성할 수 있습니다.

앱 설정 및 연결 문자열에 대한 자세한 내용은 [웹 사이트 구성][웹 사이트 구성]을 참조하세요.

### FTPS

Azure는 **FTPS**를 통해 웹 사이트용 파일 시스템에 대한 보안 FTP 액세스를 제공합니다. 따라서 진단 로그 뿐만 아니라 웹 사이트의 응용 프로그램 코드에도 안전하게 액세스할 수 있습니다. 웹 사이트에 대한 FTPS 링크는 [Azure 관리 포털][Azure 관리 포털]의 **대시보드** 사이트에서 찾을 수 있습니다.

FTPS에 대한 자세한 내용은 [File Transfer Protocol][File Transfer Protocol](영문)을 참조하세요.

## 다음 단계

Azure 플랫폼 보안에 대한 자세한 내용, **보안 인시던트 또는 남용** 보고 또는 사이트에 대한 **침입 테스트** 수행 계획을 Microsoft에 알리는 방법에 대한 자세한 내용은 [Microsoft Azure 보안 센터][Microsoft Azure 보안 센터]의 보안 섹션을 참조하세요.

Azure 웹 사이트의 **web.config** 또는 **applicationhost.config** 파일에 대한 자세한 내용은 [Azure 웹 사이트에서 잠금 해제된 구성 옵션][Azure 웹 사이트에서 잠금 해제된 구성 옵션](영문)을 참조하세요.

공격을 감지하는 데 유용할 수 있는 Azure 웹 사이트의 로깅 정보에 대한 자세한 내용은 [진단 로깅 사용][진단 로깅 사용]을 참조하세요.

  [OWASP(Open Web Application Security Project)]: https://www.owasp.org/index.php/Main_Page
  [상위 10대 프로젝트]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [보안 통신]: #https
  [보안 개발]: #develop
  [다음 단계]: #next
  [사용자 지정 도메인 이름]: http://azure.microsoft.com/ko--kr/documentation/articles/web-sites-custom-domain-name/
  [Azure 웹 사이트에 HTTPS 사용]: /ko--kr/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/ko--kr/library/system.web.mvc.requirehttpsattribute.aspx
  [Azure 웹 사이트에 보안 ASP.NET MVC 5 앱 배포]: /ko--kr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [example]: #example
  [FTP를 통해 파일 업로드/다운로드 및 진단 로그 수집]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [URL 다시 쓰기]: http://www.iis.net/downloads/microsoft/url-rewrite
  [웹 사이트 구성]: /ko--kr/documentation/articles/web-sites-configure/
  [Azure 관리 포털]: https://manage.windowsazure.com
  [File Transfer Protocol]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Microsoft Azure 보안 센터]: /ko--kr/support/trust-center/security/
  [Azure 웹 사이트에서 잠금 해제된 구성 옵션]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [진단 로깅 사용]: /ko--kr/documentation/articles/web-sites-enable-diagnostic-log/
