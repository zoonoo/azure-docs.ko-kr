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
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Azure PowerShell을 설치 및 구성하는 방법#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Azure PowerShell이란?#
Azure PowerShell은 Windows PowerShell로 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. cmdlet을 사용하여 Azure 플랫폼을 통해 제공되는 솔루션과 서비스를 만들고 테스트, 배포 및 관리할 수 있습니다. 대부분의 경우 cmdlet은 클라우드 서비스, 가상 컴퓨터, 가상 네트워크 및 웹앱 만들기 및 구성과 같이 Azure 관리 포털과 동일한 작업에 사용할 수 있습니다.

<a id="Install"></a>
## 1단계: 설치

다음은 Azure PowerShell을 설치할 수 있는 두 가지 방법입니다. WebPI 또는 PowerShell 갤러리에서 설치할 수 있습니다.

> [AZURE.NOTE]Windows PowerShell ISE(통합 스크립팅 환경)의 모든 명령을 보려면 설치 후 다시 부팅해야 할 수 있습니다.

###WebPI에서 Azure PowerShell 설치

WebPI에서 Azure PowerShell 1.0 이상을 설치하는 것은 0.9.x의 경우와 같습니다. [Azure Powershell](http://aka.ms/webpi-azps)을 다운로드하고 설치를 시작합니다. Azure PowerShell 0.9.x가 설치되어 있는 경우 0.9.x를 제거하라는 메시지가 표시됩니다. PowerShell 갤러리에서 Azure PowerShell 모듈을 설치한 경우 일관성 있는 Azure PowerShell 환경이 보장되도록 설치하기 전에 설치 관리자에서 모듈을 제거해야 합니다.

> [AZURE.NOTE]PowerShell 갤러리 Azure 모듈을 설치한 경우 제거해야 합니다. 이렇게 하면 설치한 모듈과 모듈의 위치에 대한 혼동을 방지합니다. PowerShell 갤러리 모듈은 **%ProgramFiles%\\WindowsPowerShell\\Modules**에 정상적으로 설치됩니다. 반면 WebPI 설치 관리자는 Azure 모듈을 **%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell**에 설치합니다. **PowerShellGet**은 모듈을 제거하고 모듈 종속성이 제거 중 로드되는 경우 잠긴 .dll 및 해당 폴더를 남겨 둡니다. PowerShell 갤러리 모듈을 제거해도 계속 설치에 대한 오류가 발생하면 **%ProgramFiles%\\WindowsPowerShell\\Modules** 폴더에서 Azure* 폴더를 제거합니다.

PowerShell 갤러리를 통해 Azure PowerShell을 설치했지만 대신 WebPI 설치를 사용하려는 경우 WebPI에서 설치하기 전에 다음 명령을 실행합니다.

    # Uninstall the AzureRM component modules
    Uninstall-AzureRM

    # Uninstall AzureRM module
    Uninstall-Module AzureRM

    # Uninstall the Azure module
    Uninstall-Module Azure

    # Or, you can remove all Azure modules
    # Uninstall-Module Azure* -Force

> [AZURE.NOTE]WebPI에서 설치 시 PowerShell **$env:PSModulePath**에 문제가 발생합니다. 컴퓨터에서 시스템 업데이트 또는 다른 설치로 인해 다시 시작이 필요한 경우 **$env:PSModulePath**에 Azure PowerShell이 설치되는 경로가 포함되지 않을 수 있습니다. 컴퓨터를 다시 시작하거나 Azure PowerShell 경로를 **$env: PSModulePath**에 추가하여 이 문제를 해결할 수 있습니다.

###갤러리에서 Azure PowerShell 설치

다음 명령을 사용하여 갤러리에서 Azure PowerShell 1.0 이상을 설치합니다.

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM
    Install-AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

    # Import AzureRM modules for the given version manifest in the AzureRM module
    Import-AzureRM

    # Import Azure Service Management module
    Import-Module Azure

####이러한 명령에 대한 자세한 내용

- **Install-Module AzureRM**은 AzureRM 모듈에 대한 부트스트래핑 모듈을 설치합니다. 이 모듈에는 안전하고 일관된 방식으로 AzureRM 모듈을 업데이트하고 제거하고 가져올 수 있는 cmdlet이 포함되어 있습니다. AzureRM 모듈에는 모듈의 목록과 AzureRM의 주 버전에 대해 주요 변경 내용이 없음을 확인하는 데 필요한 버전 범위(최소 및 최대)를 포함합니다. 의미 체계 버전 관리에 대한 자세한 내용은 [semver.org](http://semver.org)를 참조하세요. 즉, 특정 버전의 AzureRM을 사용하여 cmdlet을 작성하고 부트스트래퍼를 통해 설치된 모든 모듈에 주요 변경 내용이 없음을 알 수 있습니다.
- **Install-AzureRM**은 부트스트래핑 모듈에 선언된 모든 모듈을 설치합니다.
- **Install-Module Azure**는 Azure 모듈을 설치합니다. 이 모듈은 Azure PowerShell 0.9.x의 서비스 관리 모듈입니다. 이 모듈에는 주요 변경 내용이 없어야 하며 이전 버전의 Azure 모듈에 대해 서로 교환할 수 있어야 합니다.
- **Import-AzureRM**은 AzureRM 모듈의 모듈 및 버전 목록에 있는 모든 모듈을 가져옵니다. 이렇게 하면 로드되는 Azure PowerShell 모듈이 AzureRM 모듈에 필요한 버전 범위 내에 있게 됩니다.
- **Import-Module Azure**는 Azure 서비스 관리 모듈을 가져옵니다. Azure 모듈 및 AzureRM 모듈은 PowerShell 세션에 로드되고 모두 함께 사용할 수 있습니다.


## 2단계: 시작
모듈은 Azure PowerShell에 대한 사용자 지정 콘솔을 설치합니다. 표준 Windows PowerShell 콘솔이나 Azure PowerShell 콘솔에서 cmdlet을 실행할 수 있습니다. 콘솔을 여는 데 사용하는 방법은 실행 중인 Windows 버전에 따라 달라집니다.

- Windows 8 또는 Windows Server 2012 이상을 실행하는 컴퓨터에서 기본 제공 검색을 사용할 수 있습니다. **시작** 화면에서 power를 입력하기 시작합니다. 그러면 Windows PowerShell 및 Azure PowerShell이 포함된 앱 범위 목록이 반환됩니다. 콘솔을 열려면 앱을 클릭합니다. **시작** 화면에 앱을 고정하려면 아이콘을 마우스 오른쪽 단추로 클릭합니다.

- Windows 8 또는 Windows Server 2012 이전 버전을 실행하는 컴퓨터에서 **시작 메뉴**를 사용합니다. **시작** 메뉴에서 **모든 프로그램**, **Azure**, **Azure PowerShell**을 차례로 클릭합니다.

Windows PowerShell 콘솔에서 수행한 많은 동일한 작업을 수행하는 데 **Windows PowerShell ISE**를 실행하여 메뉴 항목 및 바로 가기 키를 사용할 수도 있습니다. ISE를 사용하려면 Windows PowerShell 콘솔, Cmd.exe 또는 **실행** 상자에서 **powershell\_ise.exe**를 입력합니다.

###시작하는 데 도움이 되는 명령

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
	# If the Azure PowerShell module is not listed when you run Get-Module, you may need to import it
    Import-Module Azure 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To import the Azure.Storage data plane module (blob, queue, table)
    Import-Module Azure.Storage

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## 3단계: 연결
서비스를 관리할 수 있도록 cmdlet에 구독이 필요합니다. Azure 구독이 아직 없는 경우 구매할 수 있습니다. 자세한 내용은 [Azure 구입 방법](http://go.microsoft.com/fwlink/p/?LinkId=320795)을 참조하세요.

1. **Login-AzureRmAccount**를 입력합니다.

2. 계정과 연결된 메일 주소 및 암호를 입력합니다. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

-또는-

회사 또는 학교 계정에 로그인합니다.

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE]조직 계정과 연결된 둘 이상의 테넌트가 있는 경우 TenantId 매개 변수를 지정합니다.

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE]이 비 대화형 로그인 방법은 회사 또는 학교 계정으로만 작동합니다. 회사 또는 학교 계정은 회사나 학교에서 관리되는 사용자이며, 회사나 직장에 대한 Azure Active Directory 인스턴스에 정의됩니다. 현재 회사나 학교 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.

> 1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인한 후 **Active Directory**를 클릭합니다.

> 2. 디렉터리가 없는 경우 **디렉터리 만들기**를 선택하고 요청된 정보를 제공합니다.

> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 이 새 사용자는 회사 또는 학교 계정을 사용하여 서명할 수 있습니다. 사용자를 만드는 동안 사용자의 전자 메일 주소와 임시 암호가 제공됩니다. 이 정보는 아래의 5단계에서 사용되므로 저장해 두세요.

> 4. 관리 포털에서 **설정**을 선택한 후 **관리자**를 선택합니다. **추가**를 선택하고 새 사용자를 공동 관리자로 추가합니다. 따라서 회사 또는 학교 계정이 Azure 구독을 관리할 수 있습니다.

> 5. 마지막으로, Azure 포털에서 로그아웃한 후 새 회사나 학교 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.

> 회사 또는 학교 계정을 사용한 Microsoft Azure 서명에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록](sign-up-organization.md)을 참조하십시오.

> Azure의 인증 및 구독 관리에 대한 자세한 내용은 [계정, 구독 및 관리 역할 관리](http://go.microsoft.com/fwlink/?LinkId=324796)를 참조하십시오.

### 계정 및 구독 세부 정보 보기

Azure PowerShell에서 사용 가능한 계정과 구독이 여러 개 있을 수 있습니다. **Add-AzureRmAccount**를 여러 번 실행하면 여러 계정을 추가할 수 있습니다.

사용 가능한 Azure 계정을 표시하려면 **Get-AzureAccount**를 입력합니다.

Azure 구독을 표시하려면 **Get-AzureRmSubscription**을 입력합니다.

##<a id="Help"></a>도움말 보기##

다음 리소스는 특정 cmdlet에 대한 도움말을 제공합니다.


-   콘솔 내에서 기본 제공 도움말 시스템을 사용할 수 있습니다. **Get-Help** cmdlet은 이 시스템에 대한 액세스 권한을 제공합니다. 

- 커뮤니티에서 도움을 얻으려면 다음과 같은 포럼을 참조하십시오.

 - [MSDN의 Azure 포럼](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##자세한 정보


Cmdlet을 사용하는 방법에 대한 자세한 내용을 보려면 다음 리소스를 참조하세요.

Windows PowerShell을 사용하는 방법에 대한 기본 지침은 [Windows PowerShell 사용](http://go.microsoft.com/fwlink/p/?LinkId=321939)을 참조하세요.

Cmdlet에 대한 참조 내용은 [Azure Cmdlet 참조](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx)를 참조하세요.

스크립팅을 사용하여 Azure를 관리하는 방법을 배우는 데 도움이 되는 샘플 스크립트 및 지침은 [스크립트 센터](http://go.microsoft.com/fwlink/p/?LinkId=321940)를 참조하세요.

<!---HONumber=AcomDC_0107_2016-->