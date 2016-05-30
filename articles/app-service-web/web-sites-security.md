<properties
	pageTitle="Azure 앱 서비스에서 앱 보안"
	description="웹앱, 모바일 앱 백 엔드, 또는 Azure 앱 서비스에서 API 앱을 보호하는 방법에 대해 알아봅니다."
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
	ms.date="01/12/2016"
	ms.author="cephalin"/>


#Azure 앱 서비스에서 앱 보안

이 문서는 웹앱, 모바일 앱 백 엔드 또는 Azure 앱 서비스의 API 앱 보안 설정을 시작할 때 도움이 됩니다.

Azure 앱 서비스의 보안에는 두 가지 수준이 있습니다.

- **인프라 및 플랫폼 보안** - Azure가 클라우드에서 실제로 안전하게 실행되어야 하는 서비스를 포함한다고 신뢰합니다.
- **응용 프로그램 보안** - 앱 자체를 안전하게 설계해야 합니다. 여기에는 Azure Active Directory와 통합하는 방법, 인증서를 관리하는 방법 및 다양한 서비스와 안전하게 통신할 수 있는지 확인하는 방법이 포함됩니다. 

#### 인프라 및 플랫폼 보안
앱 서비스는 Azure VM, 저장소, 네트워크 연결, 웹 프레임워크, 관리 및 통합 기능 등을 유지 관리하기 때문에 적극적으로 보안 및 확정되며 엄격한 규정 준수 과정을 거치고 다음을 지속적으로 확인합니다.

- 앱 서비스 앱은 인터넷과 다른 고객의 Azure 리소스로부터 격리됩니다.
- 앱 서비스 앱 및 기타 리소스 그룹의 Azure 리소스(예: SQL 데이터베이스) 간의 비밀 정보(예: 연결 문자열) 통신이 Azure 내에 유지되고 다른 네트워크 경계를 벗어나지 않습니다. 비밀 정보는 항상 암호화됩니다.
- 앱 서비스 앱 및 외부 리소스(예: PowerShell 관리, 명령줄 인터페이스, Azure SDK, REST API 및 하이브리드 연결) 간의 모든 통신은 적절하게 암호화됩니다.
- 24시간 위협 관리를 통해 맬웨어, DDoS(배포된 서비스 거부), MITM(메시지 가로채기) 및 기타 위협으로부터 앱 서비스 리소스를 보호합니다. 

Azure의 인프라 및 플랫폼 보안에 대한 자세한 내용은 [Azure 보안 센터](/support/trust-center/security/)를 참조하세요.

#### 응용 프로그램 보안

Azure가 응용 프로그램이 실행되는 인프라 및 플랫폼의 보안을 담당하는 반면 응용 프로그램 자체를 보호하는 것은 사용자 책임입니다. 즉, 응용 프로그램 코드 및 콘텐츠를 안전한 방식으로 개발, 배포 및 관리해야 합니다. 이렇게 하지 않으면 응용 프로그램 코드 또는 콘텐츠는 다음과 같은 위협에 취약할 수 있습니다.

- SQL 삽입
- 세션 하이재킹
- 교차 사이트 스크립팅
- 응용 프로그램 수준 MITM
- 응용 프로그램 수준 DDoS

이 문서에서는 웹 기반 응용 프로그램의 보안 고려 사항을 모두 다루지는 않습니다. 응용 프로그램 보안 유지를 위한 기본적인 추가 지침을 보려면 [OWASP(Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page)(영문)에서 OWASP 구성원이 결정하는 최신 상위 10가지 중대 웹 응용 프로그램 보안 결함을 나열하는 [상위 10대 프로젝트](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)를 참조하세요.

## 앱에 대한 침투 테스트 수행

앱 서비스 앱에서 취약점 테스트를 시작하는 가장 쉬운 방법 중 하나는 [Tinfoil Security와 통합](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)을 사용하여 앱에서 한 번의 클릭으로 취약점 검사를 수행하는 것입니다. 이해하기 쉬운 보고서에서 테스트 결과를 확인하고 단계별 지침에 따라 각 취약점을 수정하는 방법을 알아볼 수 있습니다.

사용자 고유의 침투 테스트를 수행하거나 다른 스캐너 도구 모음 또는 공급자를 사용하려면 [Azure 침투 테스트 승인 프로세스](https://security-forms.azure.com/penetration-testing/terms)에 따라 원하는 침투 테스트를 수행하기 위한 사전 승인을 얻어야 합니다.

##<a name="https"></a> 고객과 보안 통신

앱 서비스 앱에 대해 만든 ***.azurewebsites.net** 도메인 이름을 사용하는 경우 모든 ***.azurewebsites.net** 도메인 이름에 대해 SSL 인증서가 제공되므로 HTTPS를 즉시 사용할 수 있습니다. 사이트에서 [사용자 지정 도메인 이름](web-sites-custom-domain-name.md)을 사용하는 경우 SSL 인증서를 업로드하여 해당 사용자 지정 도메인에 대해 [HTTPS를 사용하도록 설정](web-sites-configure-ssl-certificate.md)할 수 있습니다.

[HTTPS](https://en.wikipedia.org/wiki/HTTPS)를 사용하도록 설정하면 앱과 사용자 간의 통신에서 MITM 공격으로부터 보호할 수 있습니다.

## 데이터 계층 보안

앱 서비스는 SQL 데이터베이스와 긴밀하게 통합되며 모든 연결 문자열은 전체적으로 암호화되고 앱이 실행되는 VM에서만, *그리고* 앱이 실행될 때만 해독됩니다. 또한 Azure SQL 데이터베이스는 [저장 시 암호화](https://msdn.microsoft.com/library/dn948096.aspx), [항상 암호화](https://msdn.microsoft.com/library/mt163865.aspx), [동적 데이터 마스킹](../sql-database/sql-database-dynamic-data-masking-get-started.md), [위협 감지](../sql-database/sql-database-threat-detection-get-started.md) 등 사이버 위협으로부터 응용 프로그램 데이터를 보호하는 다양한 보안 기능을 포함합니다. 중요한 데이터나 규정 준수 요구 사항이 있는 경우 데이터를 보호하는 방법에 대한 자세한 내용은 [SQL 데이터베이스 보안](../sql-database/sql-database-security.md)을 참조하세요.

ClearDB와 같은 타사 데이터베이스 공급자를 사용하는 경우 보안 모범 사례에 대한 공급자 설명서를 직접 참조해야 합니다.

##<a name="develop"></a> 개발 및 배포 보안

### 게시 프로필 및 게시 설정

**Visual Studio**, **Web Matrix**, **Azure PowerShell** 또는 **Azure CLI(Azure 명령줄 인터페이스)**와 같은 유틸리티를 사용하여 응용 프로그램을 개발하거나, 관리 작업을 수행하거나, 작업을 자동화할 경우 *게시 설정* 파일 또는 *게시 프로필*을 사용할 수 있습니다. 두 파일 형식 모두 Azure로 사용자를 인증하고 무단 액세스를 방지하도록 보호합니다.

* **게시 설정** 파일에 포함된 내용

	* Azure 구독 ID

	* *계정 이름이나 암호를 제공할 필요 없이* 구독에 대해 관리 작업을 수행할 수 있도록 하는 관리 인증서

* **게시 프로필** 파일에 포함된 내용

	* 앱에 게시하기 위한 정보

게시 설정 파일 또는 게시 프로필 파일을 사용하는 유틸리티를 사용하는 경우 게시 설정이나 프로필을 포함하는 파일을 해당 유틸리티로 가져온 다음 파일을 **삭제**합니다. 프로젝트에서 함께 작업하는 다른 사람들과 공유하기 위해 이 파일을 유지해야 하는 경우 *암호화된* 디렉터리와 같은 보안 위치에 엄격한 사용 권한을 지정하여 저장합니다.

또한 가져온 자격 증명의 보안이 유지되도록 해야 합니다. 예를 들어 **Azure PowerShell** 및 **Azure CLI(Azure 명령줄 인터페이스)**는 둘 다 **home directory**(Linux 또는 OS X 시스템의 경우 *~*, Windows 시스템의 경우 */users/yourusername*)에 가져온 정보를 저장합니다. 추가 보안을 위해 운영 체제에서 사용할 수 있는 암호화 도구를 사용하여 이러한 위치를 **암호화**할 수 있습니다.

### 구성 설정 및 연결 문자열
연결 문자열, 인증 자격 증명 및 기타 민감한 정보를 구성 파일에 저장하는 것이 일반적인 방식입니다. 불행히도 이러한 파일은 웹 사이트에 노출되거나 공용 저장소에 체크 인되어 이 정보가 노출될 수 있습니다. 예를 들어 [GitHub](https://github.com)에서 간단히 검색하여 공용 리포지토리에 노출된 비밀 정보를 사용하여 수많은 구성 파일을 발견할 수 있습니다.

가장 좋은 방법은 이 정보를 앱의 구성 파일 이외에 보관하는 것입니다. 앱 서비스에서 구성 정보를 **앱 설정** 및 **연결 문자열**과 같은 런타임 환경의 일부로 저장할 수 있습니다. 값은 대부분의 프로그래밍 언어에서 *환경 변수*를 통해 런타임에 응용 프로그램에 노출됩니다. .NET 응용 프로그램의 경우에는 이러한 값이 런타임에 .NET 구성에 주입됩니다. 이러한 경우 외에도 이러한 구성 설정은 [Azure 포털](https://portal.azure.com) 또는 PowerShell, Azure CLI와 같은 유틸리티를 사용하여 보거나 구성하지 않는 경우 암호화되어 유지됩니다.

앱 서비스에서 구성 정보를 저장하면 앱의 관리자는 프로덕션 앱에 대한 중요한 정보를 잠글 수 있습니다. 개발자는 앱 개발에 대한 별도의 구성 설정 집합을 사용할 수 있으며 이러한 설정은 앱 서비스에 구성된 설정으로 자동으로 대체됩니다. 개발자조차도 프로덕션 앱에 대해 구성된 비밀 정보를 알 수 없습니다. 앱 서비스에서 앱 설정 및 연결 문자열 구성에 대한 자세한 내용은 [웹앱 구성](web-sites-configure.md)을 참조하세요.

### FTPS

Azure 앱 서비스는 **FTPS**를 통해 앱용 파일 시스템에 대한 보안 FTP 액세스를 제공합니다. 따라서 진단 로그 뿐만 아니라 웹 앱의 응용 프로그램 코드에도 안전하게 액세스할 수 있습니다. FTP 대신 FTPS를 항상 사용하는 것이 좋습니다.

다음 단계에서 앱에 대한 FTPS 링크를 찾을 수 있습니다.

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. **모두 찾아보기**를 선택합니다.
3. **찾아보기** 블레이드에서 **앱 서비스**를 선택합니다.
4. **앱 서비스** 블레이드에서 원하는 앱을 선택합니다.
5. 앱의 블레이드에서 **모든 설정**을 선택합니다.
6. **설정** 블레이드에서 **속성**을 선택합니다.
7. FTP 및 FTPS 링크는 **설정** 블레이드에서 제공됩니다. 

FTPS에 대한 자세한 내용은 [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol)(영문)을 참조하세요.

## 다음 단계

Azure 플랫폼 보안에 대한 자세한 내용, **보안 인시던트 또는 남용** 보고 또는 사이트에 대한 **침입 테스트** 수행 계획을 Microsoft에 알리는 방법에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/security/)의 보안 섹션을 참조하세요.

앱 서비스 앱의 **web.config** 또는 **applicationhost.config** 파일에 대한 자세한 내용은 [Azure 앱 서비스 웹앱에서 잠금 해제된 구성 옵션](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)을 참조하세요.

공격을 감지하는 데 유용할 수 있는 앱 서비스 앱의 로깅 정보에 대한 자세한 내용은 [진단 로깅 사용](web-sites-enable-diagnostic-log.md)을 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용

* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!---HONumber=AcomDC_0518_2016-->