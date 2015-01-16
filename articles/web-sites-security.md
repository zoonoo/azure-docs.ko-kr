<properties title="Securing an Azure Website" pageTitle="Azure 웹 사이트 보안 유지" description="Azure 웹 사이트의 보안을 유지하는 방법에 대해 알아봅니다." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


#Azure 웹 사이트에서 웹 응용 프로그램 보안 유지

웹 응용 프로그램을 개발할 때 한 가지 해결 과제는 고객에게 보안이 유지되는 안전한 서비스를 제공하는 방법입니다. 이 문서에서는 웹 응용 프로그램 보안을 유지할 수 있는 Azure 웹 사이트의 기능에 대해 알아봅니다.

> [WACOM.NOTE] 이 문서에서는 웹 기반 응용 프로그램의 보안 고려 사항을 모두 다루지는 않습니다. 웹 응용 프로그램 보안 유지를 위한 기본적인 추가 지침을 보려면 [OWASP(Open Web Application Security Project)]( https://www.owasp.org/index.php/Main_Page)(영문)에서 OWASP 구성원이 결정하는 최신 상위 10가지 중대 웹 응용 프로그램 보안 결함을 나열하는 [상위 10대 프로젝트](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)를 참조하세요.

###목차

* [보안 통신](#https)
* [보안 개발](#develop)
* [다음 단계](#next)
 
##<a name="https"></a>보안 통신

웹 사이트에 대해 만들어진 ***.azurewebsites.net** 도메인 이름을 사용하는 경우 모든 ***.azurewebsites.net** 도메인 이름에 대해 SSL 인증서가 제공되므로 HTTPS를 즉시 사용할 수 있습니다. 사이트에서 [사용자 지정 도메인 이름](http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-custom-domain-name/)을 사용하는 경우 SSL 인증서를 업로드하여 해당 사용자 지정 도메인에 대해 HTTPS를 사용하도록 설정할 수 있습니다.

##<a name="develop"></a>보안 개발 

###게시 프로필 및 게시 설정

**Visual Studio**, **Web Matrix**, **Azure PowerShell** 또는 Azure **플랫폼 간 명령줄 인터페이스**와 같은 유틸리티를 사용하여 응용 프로그램을 개발하거나, 관리 작업을 수행하거나, 작업을 자동화할 경우 *게시 설정* 파일 또는 *게시 프로필*을 사용할 수 있습니다. 두 항목 모두 Azure에서 사용자를 인증하고 무단 액세스를 방지하도록 보호합니다.

* **게시 설정** 파일에 포함된 내용

	* Azure 구독 ID

	* *계정 이름이나 암호를 제공할 필요 없이* 구독에 대해 관리 작업을 수행할 수 있도록 하는 관리 인증서

* **게시 프로필** 파일에 포함된 내용

	* Azure 웹 사이트에 게시하기 위한 정보

게시 설정 또는 게시 프로필을 사용하는 유틸리티를 사용하는 경우 게시 설정이나 프로필을 포함하는 파일을 해당 유틸리티로 가져온 다음 파일을 **삭제**합니다. 프로젝트에서 함께 작업하는 다른 사람들과 공유하기 위해 이 파일을 유지해야 하는 경우 **암호화된** 디렉터리와 같은 보안 위치에 엄격한 사용 권한을 지정하여 저장합니다.

또한 가져온 자격 증명의 보안이 유지되도록 해야 합니다. 예를 들어 **Azure PowerShell** 및 the **Azure 플랫폼 간 명령줄 인터페이스**는 둘 다 **홈 디렉터리**(Linux 또는 OS X 시스템의 경우 *~*, Windows 시스템의 경우 */users/yourusername*)에 저장합니다. 추가 보안을 위해 운영 체제에서 사용할 수 있는 암호화 도구를 사용하여 이러한 위치를 **암호화**할 수 있습니다.

###구성 설정 및 연결 문자열
연결 문자열, 인증 자격 증명 및 기타 민감한 정보를 구성 파일에 저장하는 것이 일반적인 방식입니다. 불행히도 이러한 파일은 웹 사이트에 노출되거나 공용 저장소에 체크 인되어 이 정보가 노출될 수 있습니다.

Azure 웹 사이트에서 구성 정보를 **앱 설정** 및 **연결 문자열**과 같은 웹 사이트 런타임 환경의 일부로 저장할 수 있습니다. 값은 대부분의 프로그래밍 언어에서 *환경 변수*를 통해 런타임에 응용 프로그램에 노출됩니다. .NET 응용 프로그램의 경우에는 이러한 값이 런타임에 .NET 구성에 주입됩니다.

**앱 설정** 및 **연결 문자열**은 Azure 관리 포털 또는 PowerShell이나 Azure 플랫폼 간 명령줄 인터페이스와 같은 유틸리티를 사용하여 구성할 수 있습니다.

앱 설정 및 연결 문자열에 대한 자세한 내용은 [웹 사이트 구성](/ko-kr/documentation/articles/web-sites-configure/)을 참조하세요.

###FTPS

Azure는 **FTPS**를 통해 웹 사이트용 파일 시스템에 대한 보안 FTP 액세스를 제공합니다. 따라서 진단 로그 뿐만 아니라 웹 사이트의 응용 프로그램 코드에도 안전하게 액세스할 수 있습니다. 웹 사이트에 대한 FTPS 링크는 [Azure 관리 포털](https://manage.windowsazure.com)의 **대시보드** 사이트에서 찾을 수 있습니다.

FTPS에 대한 자세한 내용은 [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol)(영문)을 참조하세요.

##다음 단계

Azure 플랫폼 보안에 대한 자세한 내용, **보안 인시던트 또는 남용** 보고 또는 사이트에 대한 **침입 테스트** 수행 계획을 Microsoft에 알리는 방법에 대한 자세한 내용은 [Microsoft Azure 보안 센터](/ko-kr/support/trust-center/security/)의 보안 섹션을 참조하세요.

Azure 웹 사이트의 **web.config** 또는 **applicationhost.config** 파일에 대한 자세한 내용은 [Azure 웹 사이트에서 잠금 해제된 구성 옵션](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)(영문)을 참조하세요.

공격을 감지하는 데 유용할 수 있는 Azure 웹 사이트의 로깅 정보에 대한 자세한 내용은 [진단 로깅 사용](/ko-kr/documentation/articles/web-sites-enable-diagnostic-log/)을 참조하세요.
