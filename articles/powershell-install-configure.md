<properties
	pageTitle="Azure PowerShell을 설치 및 구성하는 방법"
	description="Azure PowerShell을 설치 및 구성하는 방법에 대해 알아봅니다."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Azure PowerShell을 설치 및 구성하는 방법#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

Windows PowerShell을 사용하면 명령 프롬프트에서 대화형으로 또는 스크립트를 통해 자동으로 Azure에서 다양한 작업을 수행할 수 있습니다. Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. cmdlet을 사용하여 Azure 플랫폼을 통해 제공되는 솔루션과 서비스를 만들고 테스트, 배포 및 관리할 수 있습니다. 대부분의 경우 cmdlet을 사용하여 Azure 관리 포털을 통해 수행할 수 있는 것과 동일한 작업을 수행할 수 있습니다. 예를 들어 클라우드 서비스, 가상 컴퓨터, 가상 네트워크 및 웹 앱을 만들고 구성할 수 있습니다.

모듈은 다운로드 가능한 파일로 분산되고 소스 코드는 공개적으로 사용 가능한 리포지토리를 통해 관리됩니다. 다운로드 가능한 파일의 링크는 이 항목의 뒷부분에 있는 설치 지침에 제공되어 있습니다. 소스 코드에 대한 자세한 내용은 [Azure PowerShell 코드 리포지토리](https://github.com/Azure/azure-powershell)(영문)를 참조하십시오.

이 가이드에서는 Azure PowerShell을 설치 및 설정하여 Azure 플랫폼을 관리하는 방법에 대한 기본 정보를 제공합니다.

### <a id="Prereq"></a>Azure PowerShell 사용을 위한 필수 구성 요소

Azure는 구독 기반 플랫폼입니다. 즉, 플랫폼을 사용하려면 구독이 필요함을 의미합니다. 대부분의 경우 cmdlet에서 구독을 사용하여 작업을 수행하려면 구독 정보가 필요함을 의미하기도 합니다. 저장소 관련 cmdlet 중 일부는 이 정보 없이도 사용할 수 있습니다. 구독에 연결하도록 컴퓨터를 구성하여 이 정보를 제공합니다. 지침은 이 문서의 "방법:구독에 연결"에서 제공합니다.

> [AZURE.NOTE]버전 0.8.5부터는 Azure PowerShell 모듈에 Microsoft .NET Framework 4.5가 필요합니다.

모듈을 설치할 때 설치 관리자는 시스템에서 필수 소프트웨어를 확인하고 Windows PowerShell 및 .NET Framework의 올바른 버전과 같은 모든 종속성을 설치합니다.

<a id="Install"></a>
## Azure PowerShell을 설치하는 방법

[Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?LinkId=320376)를 실행하여 Azure PowerShell 모듈을 다운로드하고 설치할 수 있습니다. 메시지가 표시되면 **실행**을 클릭합니다. 웹 플랫폼 설치 관리자는 Azure PowerShell 모듈 및 모든 종속성을 설치합니다. 프롬프트에 따라 설치를 완료합니다.

> [AZURE.NOTE]PowerShell 설치 프로그램을 다운로드하려면 https://github.com/Azure/azure-powershell/releases을 방문하세요. 이 repo에서도 PowerShell cmdlet에 대한 소스 코드를 찾을 수 있습니다.

Azure에 사용 가능한 명령줄 도구에 대한 자세한 내용은 [명령줄 도구](http://go.microsoft.com/fwlink/?LinkId=320796)를 참조하십시오.

모듈을 설치하면 Azure PowerShell에 대한 사용자 지정 콘솔도 설치됩니다. 표준 Windows PowerShell 콘솔이나 Azure PowerShell 콘솔에서 cmdlet을 실행할 수 있습니다.

콘솔을 여는 데 사용하는 방법은 실행 중인 Windows 버전에 따라 달라집니다.

- Windows 8 또는 Windows Server 2012 이상을 실행하는 컴퓨터에서 기본 제공 검색을 사용할 수 있습니다. 시작 화면에서 **power**를 입력합니다. 그러면 Windows PowerShell 및 Azure PowerShell이 포함된 앱 범위 목록이 반환됩니다. 콘솔을 열려면 앱을 클릭합니다. 시작 화면에 앱을 고정하려면 아이콘을 마우스 오른쪽 단추로 클릭합니다.

- Windows 8 또는 Windows Server 2012 이전 버전을 실행하는 컴퓨터에서 시작 메뉴를 사용합니다. 시작 메뉴에서 **모든 프로그램**, **Azure**, **Azure PowerShell**을 차례로 클릭합니다.

<a id="Connect"></a>
## 방법: 구독에 연결합니다.

Azure를 사용하려면 구독이 필요합니다. 구독이 없는 경우 [Azure 시작](http://go.microsoft.com/fwlink/p/?LinkId=320795)을 참조하십시오.

서비스를 관리할 수 있도록 cmdlet에 구독이 필요합니다. Windows PowerShell에 구독 정보를 제공하는 두 가지 방법이 있습니다. 정보가 포함된 관리 인증서를 사용하거나, Microsoft 계정 또는 학교 계정을 사용하여 Azure에 로그인할 수 있습니다. 로그인하면 Azure AD(Azure Active Directory)가 자격 증명을 인증하며 Azure PowerShell이 계정을 관리할 수 있도록 해 주는 액세스 토큰을 반환합니다.

요구에 적합한 인증 방법을 선택하려면 다음을 고려합니다.

- Azure AD는 구독에 대한 액세스를 보다 쉽게 관리할 수 있게 해 주므로 권장되는 인증 방법입니다. 버전 0.8.6의 업데이트를 적용하면 직장이나 학교 계정이 사용되는 경우 Azure AD 인증에도 자동 시나리오가 가능합니다. Azure 리소스 관리자 API에서도 작동합니다.
- 인증서 방법을 사용하는 경우 구독 및 인증서가 유효한 동안 계속해서 구독 정보를 사용할 수 있습니다. 그러나 이 방법을 사용하면 여러 사용자가 계정에 액세스할 수 있는 권한이 있는 경우 등 공유 구독에 대한 액세스를 관리하는 것이 더 어렵습니다. 또한 Azure 리소스 관리자 API는 인증서 인증을 받아들이지 않습니다.

Azure의 인증 및 구독 관리에 대한 자세한 내용은 [계정, 구독 및 관리 역할 관리](http://go.microsoft.com/fwlink/?LinkId=324796)를 참조하십시오.

### Azure AD 방법 사용

1. [방법: Azure PowerShell 설치](#Install)의 지침에 따라 Azure PowerShell 콘솔을 엽니다.

2. 다음 명령을 입력합니다.

		Add-AzureAccount

3. 창에서 계정과 연결된 전자 메일 주소 및 암호를 입력합니다.

4. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

5. 0\.8.6 부터는 회사나 학교 계정 작업을 사용하여 로그인하는 경우, 팝업 창을 사용하지 않도록 다음 명령을 입력할 수 있습니다. 회사나 학교 계정 사용자 이름 및 암호를 입력하기 위한 표준 Windows PowerShell 자격 증명 창이 팝업 창으로 나타납니다.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	> [AZURE.NOTE]보안 및 자격 증명 사용에 대한 자세한 내용은 [ASP.NET 및 Azure 웹 사이트에 암호 및 기타 중요한 데이터 배포를 위한 모범 사례](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)를 참조하십시오.

	> [AZURE.NOTE]이 비 대화형 로그인 방법은 회사 또는 학교 계정으로만 작동합니다. 회사 또는 학교 계정은 회사나 학교에서 관리되는 사용자이며, 회사나 직장에 대한 Azure Active Directory 인스턴스에 정의됩니다. 현재 회사나 학교 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.
	>
	> 1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인한 후 **Active Directory**를 클릭합니다.
	>
	> 2. 디렉터리가 없는 경우 **디렉터리 만들기**를 선택하고 요청된 정보를 제공합니다.
	>
	> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 이 새 사용자는 회사 또는 학교 계정을 사용하여 서명할 수 있습니다.
	>
	>     사용자를 만드는 동안 사용자의 전자 메일 주소와 임시 암호가 제공됩니다. 이 정보는 다른 단계에서 사용되므로 저장해 두십시오.
	>
	> 4. 관리 포털에서 **설정**을 선택한 후 **관리자**를 선택합니다. **추가**를 선택하고 새 사용자를 공동 관리자로 추가합니다. 따라서 회사 또는 학교 계정이 Azure 구독을 관리할 수 있습니다.
	>
	> 5. 마지막으로, Azure 포털에서 로그아웃한 후 새 회사나 학교 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.
	>
	>회사 또는 학교 계정을 사용한 Microsoft Azure 서명에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록](sign-up-organization.md)을 참조하십시오.

### 인증서 방법 사용

Azure 모듈에는 인증서를 다운로드하고 가져오는 데 도움이 되는 cmdlet이 포함되어 있습니다.

> [AZURE.NOTE]AzureResourceManager 모듈의 cmdlet은 Azure AD 메서드(Add-AzureAccount)를 필요로 합니다. 이 cmdlet은 설정 파일 게시를 지원하지 않습니다. AzureResourceManager 모듈의 cmdlet에 대한 자세한 내용은 [Azure 리소스 관리자 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765)(영문)을 참조하십시오.


- **Get-AzurePublishSettingsFile** cmdlet은 구독 정보를 다운로드할 수 있는 Azure 관리 포털의 웹 페이지를 엽니다. 정보는 .publishsettings 파일에 포함되어 있습니다.

- **Import-AzurePublishSettingsFile**은 모듈에서 사용하기 위해 .publishsettings 파일을 가져옵니다. 이 파일에는 보안 자격 증명이 있는 관리 인증서가 포함되어 있습니다.

> [AZURE.IMPORTANT]이 설정을 가져온 후 <b>Get-AzurePublishSettingsFile</b>을 사용하여 다운로드한 게시 프로필을 삭제하는 것이 좋습니다. 관리 인증서에는 보안 자격 증명이 포함되어 있으므로 권한 없는 사용자가 액세스하면 안 됩니다. 구독에 대한 정보가 필요한 경우 [Azure 관리 포털](http://manage.windowsazure.com/) 또는 [Microsoft Online Services 고객 포털](http://go.microsoft.com/fwlink/p/?LinkId=324875)에서 가져올 수 있습니다.

1. Azure 계정에 대한 자격 증명을 사용하여 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. [방법: Azure PowerShell 설치](#Install)의 지침에 따라 Azure PowerShell 콘솔을 엽니다.

3. 다음 명령을 입력합니다.

		Get-AzurePublishSettingsFile

4. 메시지가 표시되면 게시 프로필을 다운로드하여 저장하고 .publishsettings 파일의 경로와 이름을 확인합니다. 이 정보는 **Import-AzurePublishSettingsFile** cmdlet을 실행하여 설정을 가져올 때 필요합니다. 기본 위치 및 파일 이름 형식은 다음과 같습니다.

			C:\\Users<UserProfile>\\Download\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. 자리 표시자를 Windows 계정 이름과 경로 및 파일 이름으로 대체하여 다음과 유사한 명령을 입력합니다.

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE]게시 설정을 가져온 후 공동 관리자로 다른 구독에 추가된 경우 이 프로세스를 반복하여 새 .publishsettings 파일을 다운로드한 다음 해당 설정을 가져와야 합니다. 구독 서비스 관리에 도움이 되도록 공동 관리자를 추가하는 방법에 대한 자세한 내용은 [Azure 구독에 대한 공동 관리자 추가 및 제거](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx)를 참조하십시오.

### 계정 및 구독 세부 정보 보기

Azure PowerShell에서 사용 가능한 계정과 구독이 여러 개 있을 수 있습니다. Add-AzureAccount를 여러 번 실행하면 여러 계정을 추가할 수 있습니다.

사용 가능한 Azure 계정을 가져오려면 다음을 입력합니다.

	Get-AzureAccount

Azure 구독을 가져오려면 다음을 입력합니다.

	Get-AzureSubscription

## <a id="Ex"></a>cmdlet을 사용하는 방법: 예제 ##

모듈을 설치하고 구독에 연결하도록 컴퓨터를 구성한 후 Azure 웹 앱을 만들 수 있습니다. 이 예제는 Azure cmdlet 사용 방법을 보여 줍니다.

1. Azure PowerShell 콘솔을 시작합니다.

2. 웹 응용 프로그램 이름을 선택합니다. DNS 명명 규칙을 준수하는 이름을 선택합니다. 유효한 이름에는 문자 'a'~'z', 숫자 '0'~'9', 하이픈('-')만 사용할 수 있습니다.

	웹 앱 이름 Azure에서 고유해야 합니다. 이 예에서는 "mySite"를 사용하지만 실제로는 계정 이름 뒤에 숫자를 붙인 것과 같은 다른 이름을 선택해야 합니다.

	이름을 선택한 후 다음과 유사한 명령을 입력합니다. "MySite"에 대한 웹 앱 이름으로 대체합니다.

		New-AzureWebsite mySite

	Cmdlet는 웹 앱을 만들고 새 웹 앱을 나타내는 개체를 반환합니다. 개체 속성에는 웹 앱에 대한 유용한 정보가 포함되어 있습니다.

3. 웹 앱에 대한 정보를 가져오려면 이 명령을 입력합니다. 방금 만든 웹 앱을 비롯하여 구독에 있는 모든 웹 사이트에 대한 약간의 정보를 반환합니다.

		Get-AzureWebsite

4. 웹 앱에 대 한 자세한 정보를 가져오려면, 웹 앱 이름을 명령에 포함합니다. "mySite"를 실제 웹 앱의 이름으로 바꿔야 합니다.

		Get-AzureWebsite -Name mySite

5. 웹 앱이 만들어진 후 시작됩니다. 웹 앱을 중지하려면 웹 앱의 이름을 포함하여 이 명령을 입력합니다.

		Stop-AzureWebsite -Name mySite

6. 사이트 상태가 'stopped'인지 확인하기 위해 Get-AzureWebsite 명령을 다시 실행합니다.

		Get-AzureWebsite

7. 이 테스트를 완료하려면 웹 앱을 삭제 합니다. 형식:

		Remove-AzureWebsite -Name mySite

7. 작업을 완료하기 위해 웹 앱이 삭제되었음을 확인합니다.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>도움말 보기##

다음 리소스는 특정 cmdlet에 대한 도움말을 제공합니다.


-   콘솔 내에서 기본 제공 도움말 시스템을 사용할 수 있습니다. **Get-Help** cmdlet은 이 시스템에 대한 액세스 권한을 제공합니다. 다음 표에서는 도움말을 보기 위해 사용할 수 있는 명령의 몇 가지 예를 제공합니다. **help**를 입력하면 콘솔 내에서 추가 명령을 볼 수 있습니다.

    <table border="1" cellspacing="4" cellpadding="4">
<tbody>
<tr align="left" valign="top">
	<td><b>명령</b></td>
	<td><b>결과</b></td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help</td>
	<td>도움말 시스템을 사용하는 방법을 설명합니다. <p><b>참고</b>: 설명에는 Azure 모듈에 적용되지 않는 도움말 파일에 대한 정보가 일부 포함되어 있습니다. 특히 도움말 파일은 모듈을 설치할 때 설치됩니다. 별도로 다운로드할 수 없습니다.</p>
</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help Azure</td>
	<td>Azure 모듈의 모든 cmdlet을 가져옵니다.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>language</b>>-dev</td>
	<td>특정 언어로 응용 프로그램을 개발하고 관리하기 위한 cmdlet을 가져옵니다. 예: help node-dev, help php-dev, help python-dev</td>
</tr>
    <tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>></td>
	<td>Windows PowerShell cmdlet에 대한 도움말을 가져옵니다. <cmdlet>을 cmdlet 이름으로 바꿉니다.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Parameter *</td>
	<td>cmdlet 매개 변수의 설명을 가져옵니다. 별표(*)는 "모두"를 의미합니다.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Examples</td>
	<td>cmdlet을 사용하는 구문과 예제를 가져옵니다.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Full</td>
	<td>세부적인 기술 정보를 포함하여 cmdlet에 대한 모든 도움말을 가져옵니다.</td>
</tr>
</tbody>
</table>



- Azure 라이브러리에서 Azure PowerShell 모듈의 cmdlet에 대한 참조 정보를 사용할 수도 있습니다. 자세한 내용은 [Azure Cmdlet 참조](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx)를 확인하십시오.

커뮤니티에서 도움을 얻으려면 다음과 같은 포럼을 참조하십시오.

- [MSDN의 Azure 포럼](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>추가 리소스 ##

Azure 및 Windows PowerShell을 사용하는 방법을 알아볼 수 있는 몇 가지 리소스는 다음과 같습니다.

- Azure 저장소 구성 요소를 액세스하는 방법에 대해 알아보려면[ Azure 저장소와 Azure PowerShell 사용](storage-powershell-guide-full.md)을 참조하세요.

- cmdlet에 대한 의견을 제공하거나, 문제를 보고하거나, 소스 코드에 액세스하려면 [Azure PowerShell 코드 리포지토리](https://github.com/WindowsAzure/azure-sdk-tools)를 참조하십시오.

- Windows PowerShell 명령줄 및 스크립팅 환경에 대해 알아보려면 [TechNet 스크립트 센터](http://go.microsoft.com/fwlink/p/?LinkId=320211)를 참조하십시오.

- Windows PowerShell 설치, 학습, 사용 및 사용자 지정에 대한 자세한 내용은 [Windows PowerShell을 사용한 스크립팅](http://go.microsoft.com/fwlink/p/?LinkId=320210)을 참조하십시오.

- 스크립트 정의 및 Windows PowerShell에서 스크립트를 실행하는 방법에 대한 자세한 내용은 [스크립트 실행](http://go.microsoft.com/fwlink/p/?LinkId=320627)을 참조하십시오. 이 문서에는 스크립트를 만들고 스크립트를 실행하도록 컴퓨터를 구성하는 방법에 대한 기본 정보가 포함되어 있습니다.

- Azure AD용 cmdlet에 대한 자세한 내용은 [Windows PowerShell을 사용하여 Azure AD 관리](http://go.microsoft.com/fwlink/p/?LinkId=320628)를 참조하십시오.





  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx

<!---HONumber=July15_HO3-->