<properties
	pageTitle="Azure 앱 서비스에서 웹 앱 보안 유지"
	description="Azure 웹앱의 보안을 유지하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="cephalin"/>


#Azure 앱 서비스에서 웹 앱 보안 유지

웹 앱을 개발할 때 한 가지 해결 과제는 고객에게 보안이 유지되는 안전한 서비스를 제공하는 방법입니다. 이 문서에서는 웹앱의 보안을 유지할 수 있는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)의 기능에 대해 알아봅니다.

> [AZURE.NOTE]이 문서에서는 웹 기반 응용 프로그램의 보안 고려 사항을 모두 다루지는 않습니다. 웹 응용 프로그램 보안 유지를 위한 기본적인 추가 지침을 보려면 [OWASP(Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page)(영문)에서 OWASP 구성원이 결정하는 최신 상위 10가지 중대 웹 응용 프로그램 보안 결함을 나열하는 [상위 10대 프로젝트](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)를 참조하세요.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a name="https"></a> 보안 통신

웹앱에 대해 만든 ****.azurewebsites.net** 도메인 이름을 사용하는 경우 모든 ****.azurewebsites.net** 도메인 이름에 대해 SSL 인증서가 제공되므로 HTTPS를 즉시 사용할 수 있습니다. 사이트에서 [사용자 지정 도메인 이름](web-sites-custom-domain-name.md)을 사용하는 경우 SSL 인증서를 업로드하여 해당 사용자 지정 도메인에 대해 [HTTPS를 사용하도록 설정](web-sites-configure-ssl-certificate.md)할 수 있습니다.

##<a name="develop"></a> 보안 개발

### 게시 프로필 및 게시 설정

**Visual Studio**, **Web Matrix**, **Azure PowerShell** 또는 **Azure CLI(Azure 명령줄 인터페이스)**와 같은 유틸리티를 사용하여 응용 프로그램을 개발하거나, 관리 작업을 수행하거나, 작업을 자동화할 경우 *게시 설정* 파일 또는 *게시 프로필*을 사용할 수 있습니다. 두 항목 모두 Azure에서 사용자를 인증하고 무단 액세스를 방지하도록 보호합니다.

* **게시 설정** 파일에 포함된 내용

	* Azure 구독 ID

	* *계정 이름이나 암호를 제공할 필요 없이* 구독에 대해 관리 작업을 수행할 수 있도록 하는 관리 인증서

* **게시 프로필** 파일에 포함된 내용

	* Azure 웹 앱에 게시하기 위한 정보

게시 설정 또는 게시 프로필을 사용하는 유틸리티를 사용하는 경우 게시 설정이나 프로필을 포함하는 파일을 해당 유틸리티로 가져온 다음 파일을 **삭제**합니다. 프로젝트에서 함께 작업하는 다른 사람들과 공유하기 위해 이 파일을 유지해야 하는 경우 **암호화된** 디렉터리와 같은 보안 위치에 엄격한 사용 권한을 지정하여 저장합니다.

또한 가져온 자격 증명의 보안이 유지되도록 해야 합니다. 예를 들어 **Azure PowerShell** 및 **Azure CLI(Azure 명령줄 인터페이스)**는 둘 다 **home directory**(Linux 또는 OS X 시스템의 경우 *~*, Windows 시스템의 경우 */users/yourusername*)에 가져온 정보를 저장합니다. 추가 보안을 위해 운영 체제에서 사용할 수 있는 암호화 도구를 사용하여 이러한 위치를 **암호화**할 수 있습니다.

### 구성 설정 및 연결 문자열
연결 문자열, 인증 자격 증명 및 기타 민감한 정보를 구성 파일에 저장하는 것이 일반적인 방식입니다. 불행히도 이러한 파일은 웹 사이트에 노출되거나 공용 저장소에 체크 인되어 이 정보가 노출될 수 있습니다.

Azure 앱 서비스에서 구성 정보를 **앱 설정** 및 **연결 문자열**과 같은 웹앱 런타임 환경의 일부로 저장할 수 있습니다. 값은 대부분의 프로그래밍 언어에서 *환경 변수*를 통해 런타임에 응용 프로그램에 노출됩니다. .NET 응용 프로그램의 경우에는 이러한 값이 런타임에 .NET 구성에 주입됩니다.

**앱 설정** 및 **연결 문자열**은 [Azure Preview 포털](http://portal.azure.com) 또는 PowerShell이나 Azure CLI와 같은 유틸리티를 사용하여 구성할 수 있습니다.

앱 설정 및 연결 문자열에 대한 자세한 내용은 [웹앱 구성](web-sites-configure.md)을 참조하세요.

### FTPS

Azure는 **FTPS**를 통해 웹 앱용 파일 시스템에 대한 보안 FTP 액세스를 제공합니다. 따라서 진단 로그 뿐만 아니라 웹 앱의 응용 프로그램 코드에도 안전하게 액세스할 수 있습니다. 다음 단계에서 웹앱에 대한 FTPS 링크를 찾을 수 있습니다.

1. [Azure Preview 포털](http://portal.azure.com)을 엽니다.
2. **모두 찾아보기**를 선택합니다.
3. **찾아보기** 블레이드에서 **웹앱**을 선택합니다.
4. **웹앱** 블레이드에서 원하는 웹앱을 선택합니다.
5. 웹앱의 블레이드에서 **모든 설정**을 선택합니다.
6. **설정** 블레이드에서 **속성**을 선택합니다.
7. FTP 및 FTPS 링크는 **설정** 블레이드에서 제공됩니다. 

FTPS에 대한 자세한 내용은 [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol)(영문)을 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 다음 단계

Azure 플랫폼 보안에 대한 자세한 내용, **보안 인시던트 또는 남용** 보고 또는 사이트에 대한 **침입 테스트** 수행 계획을 Microsoft에 알리는 방법에 대한 자세한 내용은 [Microsoft Azure 보안 센터](http://azure.microsoft.com/support/trust-center/security/)의 보안 섹션을 참조하세요.

웹 앱의 **web.config** 또는 **applicationhost.config** 파일에 대한 자세한 내용은 [Azure 앱 서비스 웹￼ 앱에서 잠금 해제된 구성 옵션](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)을 참조하세요.

공격을 감지하는 데 유용할 수 있는 웹앱의 로깅 정보에 대한 자세한 내용은 [진단 로깅 사용](web-sites-enable-diagnostic-log.md)을 참조하세요.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.
 

<!---HONumber=Oct15_HO1-->