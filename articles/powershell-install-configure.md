---
title: "Azure PowerShell을 설치 및 구성하는 방법"
description: "Azure PowerShell을 설치 및 구성하는 방법에 대해 알아봅니다."
editor: tysonn
manager: dongill
documentationcenter: 
services: 
author: coreyp-at-msft
ms.assetid: 802b28e0-1a8c-4872-a9ea-c889b15b9498
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: coreyp
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f20adb27f41cdbd6918340ae688b4ce00dd2d593


---
# <a name="how-to-install-and-configure-azure-powershell"></a>Azure PowerShell을 설치 및 구성하는 방법
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

## <a name="what-is-azure-powershell"></a>Azure PowerShell이란?
Azure PowerShell은 Windows PowerShell로 Azure를 관리하기 위한 cmdlet을 제공하는 모듈 집합입니다. cmdlet을 사용하여 Azure 플랫폼을 통해 제공되는 솔루션과 서비스를 만들고 테스트, 배포 및 관리할 수 있습니다. 대부분의 경우 cmdlet은 클라우드 서비스, 가상 컴퓨터, 가상 네트워크 및 웹앱을 만들고 구성하는 등 Azure Portal과 동일한 작업에 사용할 수 있습니다.

## <a name="how-versioning-works"></a>버전 관리 작동 방식
Azure PowerShell에서는 A 버전이 B 버전보다 높으면 A 버전이 최신 API 버전임을 나타내는 유의적 버전(Semantic Versioning) 관리 기능을 사용합니다. 또한 주 버전에 대한 변경 내용이 있으면 하나 이상의 cmdlet에 대해 주요 변경 내용이 있다는 것을 의미합니다.  예를 들어 1.7.0 버전에는 Azure PowerShell 1.x 버전에 대한 주요 변경 내용을 해결하는 핫픽스가 있습니다.

Azure PowerShell에서 유의적 버전 관리 방침에 대한 자세한 내용은 http://semver.org에 있는 유의적 버전 사양을 참조하세요.

최신 API를 가져오려면 2.x 버전을 사용해야 합니다. 하지만 1.x 버전에 기반하여 작성된 스크립트가 있고 2.x [릴리스 정보](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md)에 설명된 2.x 버전의 주요 변경 내용을 받아들이지 않을 경우에는 1.7.0 버전을 설치해야 합니다.

최신 버전의 프로필 모듈이 설치되어 있고 이 모듈에서 결정하여 이전 버전의 모듈을 로드하는 경우 버전 불일치가 발생할 수 있습니다. 이 문제를 해결하는 기장 쉬운 방법은 최신.msi를 설치하는 것입니다. .msi는 이전 버전의 모듈을 자동으로 정리합니다.

### <a name="installing-module-versions-side-by-side"></a>단계별 모듈 버전 설치
2.1.0 버전(및 AzureStack의 경우 1.2.6 버전)은 단계별로 설치하여 사용하도록 설계된 첫 번째 모듈 버전입니다. Azure PowerShell에서는 이진 모듈을 사용하므로 새 PowerShell 창을 열고 다음과 같이 **Import-Module**을 사용하여 특정 버전의 AzureRM cmdlet을 가져옵니다.

    Import-Module AzureRM -RequiredVersion 2.1.0**

2.1.0 이전 버전(1.2.6 버전 예외)은 다른 Azure PowerShell 모듈 버전과는 단계별로 제대로 작동하지 않습니다. 위와 같은 명령으로 이전 버전의 Azure PowerShell 모듈을 로드하는 경우에는 호환되지 않는 **AzureRM.Profile** 모듈 버전을 로드하여 로그인했음에도 불구하고 cmdlet을 실행할 때마다 로그인하도록 요구합니다.

이 문제를 해결하는 가장 쉬운 방법은 WebPI 피드 또는.msi에서 최신 Azure PowerShell을 설치하는 것이며, 이렇게 하면 갤러리에서 설치된 이전 버전의 모듈을 제거합니다. 

Azure와 AzureRM 모듈에는 모두 공통의 종속성이 있으므로 두 모듈을 사용하고 하나를 업데이트하는 경우에는 반드시 두 모듈을 모두 업데이트해야 합니다. 이전 버전의 AzureRM 모듈과 마찬가지로 이전 버전의 Azure 모듈에도 동일한 단계별 모듈 로딩 문제가 있습니다.

<a id="Install"></a>

## <a name="step-1-install"></a>1단계: 설치
다음은 Azure PowerShell을 설치할 수 있는 두 가지 방법입니다. 즉 PowerShell 갤러리 또는 WebPI 중 하나에서 Azure PowerShell을 설치할 수 있습니다.

### <a name="installing-azure-powershell-from-the-powershell-gallery"></a>PowerShell 갤러리에서 Azure PowerShell 설치
PowerShell 갤러리를 사용하는 것이 더 좋습니다. PowerShell 갤러리를 사용하려면 PowerShellGet 모듈이 필요합니다. 이 모듈은 [PowerShellGallery.com](https://www.powershellgallery.com/)에서 제공됩니다.

다음 명령을 사용하여 관리자 권한 Windows PowerShell 또는 PowerShell ISE(통합 스크립팅 환경) 프롬프트를 통해 PowerShell 갤러리에서 Azure PowerShell 1.3.0 이상을 설치합니다.

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### <a name="more-about-these-commands"></a>이러한 명령에 대한 자세한 내용
* **Install-Module AzureRM**은 Azure Resource Manager cmdlet 롤업 모듈을 설치합니다. AzureRM 모듈은 
* 각 Azure Resource Manager 모듈의 특정 버전 범위에 따라 달라집니다. 포함된 버전 범위를 사용하는 경우 주 버전이 같은 AzureRM 모듈을 설치할 때 중요한 모듈 변경 내용을 포함할 수 없습니다. AzureRM 모듈을 설치할 때는 이전에 설치되지 않은 Azure Resource Manager 모듈이 PowerShell 갤러리에서 다운로드되어 설치됩니다. Azure PowerShell 모듈에서 사용하는 유의적 버전에 대한 자세한 내용은 [semver.org](http://semver.org)를 참조하세요. 
* **Install-Module Azure**는 Azure 모듈을 설치합니다. 이 모듈은 Azure PowerShell 0.9.x의 서비스 관리 모듈입니다. 이 모듈에는 주요 변경 내용이 없어야 하며 이전 버전의 Azure 모듈에 대해 서로 교환할 수 있어야 합니다.

### <a name="installing-azure-powershell-from-webpi"></a>WebPI에서 Azure PowerShell 설치
WebPI에서 Azure PowerShell 1.0 이상을 설치하는 것은 0.9.x의 경우와 같습니다. [Azure Powershell](http://aka.ms/webpi-azps) 을 다운로드하고 설치를 시작합니다. Azure PowerShell 0.9.x를 설치한 경우에는 업그레이드의 일부분으로 버전 0.9.x가 제거됩니다. PowerShell 갤러리에서 Azure PowerShell 모듈을 설치한 경우 Azure PowerShell 환경의 일관성을 유지하기 위해 설치 전에 설치 관리자가 해당 모듈을 자동으로 제거합니다.

> [!NOTE]
> 이전에 PowerShell 갤러리에서 Azure 모듈을 설치한 경우에는 설치 관리자가 해당 모듈을 자동으로 제거합니다. 그러므로 설치한 모듈 버전과 모듈이 설치된 위치를 혼동하는 상황을 방지할 수 있습니다. PowerShell 갤러리 모듈은 대개 **%ProgramFiles%\WindowsPowerShell\Modules**에 설치됩니다. 반면 WebPI 설치 관리자에서는 Azure 모듈을 **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell\**에 설치합니다. 설치 중에 오류가 발생하는 경우 **%ProgramFiles%\WindowsPowerShell\Modules** 폴더에서 Azure* 폴더를 수동으로 제거한 다음 다시 설치하면 됩니다.
> 
> 

설치가 완료되면 ```$env:PSModulePath``` 설정에는 Azure PowerShell cmdlet이 들어 있는 디렉터리가 포함되어야 합니다.

> [!NOTE]
> PowerShell **$env:PSModulePath** 에는 WebPI에서 설치할 때 발생할 수 있는 알려진 문제가 있습니다. 시스템 업데이트 또는 다른 설치로 인해 컴퓨터를 다시 시작해야 하는 경우 **$env:PSModulePath** 업데이트 과정에서 Azure PowerShell이 설치된 경로가 포함되지 않을 수 있습니다. 이러한 상황에서 설치 또는 업그레이드 후 Azure PowerShell cmdlet을 사용하려고 하면 cmdlet을 인식할 수 없다는 메시지가 표시될 수 있습니다. 이 문제가 발생하는 경우 컴퓨터를 다시 시작하면 문제가 해결됩니다.
> 
> 

cmdlet을 로드하거나 실행하려고 할 때 다음과 같은 메시지가 표시되면

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

컴퓨터 다시 시작하거나 다음과 같이 C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\에서 cmdlet을 가져와서 이를 해결할 수 있습니다(여기서 XXXX는 설치된 PowerShell의 버전입니다).

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>2단계: 시작
표준 Windows PowerShell 콘솔 또는 PowerShell ISE(통합 스크립팅 환경)에서 cmdlet을 실행할 수 있습니다.
콘솔을 여는 데 사용하는 방법은 실행 중인 Windows 버전에 따라 달라집니다.

* Windows 8 또는 Windows Server 2012 이상을 실행하는 컴퓨터에서 기본 제공 검색을 사용할 수 있습니다. **시작** 화면에서 power를 입력합니다. 그러면 Windows PowerShell이 포함된 앱 범위 목록이 반환됩니다. 콘솔을 열려면 앱을 클릭합니다. **시작** 화면에서 앱을 고정하려면 마우스 오른쪽 단추로 아이콘을 클릭합니다.
* Windows 8 또는 Windows Server 2012 이전 버전을 실행하는 컴퓨터에서는 **시작 메뉴**를 사용합니다. **시작** 메뉴에서 **모든 프로그램**, **보조프로그램**, **Windows PowerShell** 폴더를 차례로 클릭한 다음 **Windows PowerShell**을 클릭합니다.

Windows PowerShell 콘솔에서 수행한 것과 같은 작업을 대부분 수행하려면 **Windows PowerShell ISE**를 실행하여 메뉴 항목과 바로 가기 키를 사용할 수도 있습니다. ISE를 사용하려면 Windows PowerShell 콘솔에서 Cmd.exe를 입력하거나 **실행** 상자에서 **powershell_ise.exe**를 입력합니다.

### <a name="commands-to-help-you-get-started"></a>시작하는 데 도움이 되는 명령
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
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

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>3단계: 연결
서비스를 관리할 수 있도록 cmdlet에 구독이 필요합니다. 아직 Azure 구독이 아직 없는 경우에는 이 구독을 구매하면 됩니다. 자세한 지침은 [Azure 구입 방법](http://go.microsoft.com/fwlink/p/?LinkId=320795)을 참조하세요.

1. **Login-AzureRmAccount**를 입력합니다.
2. 계정과 연결된 메일 주소 및 암호를 입력합니다. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

-또는-

회사 또는 학교 계정에 로그인합니다.

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [!NOTE]
> 조직 계정과 연결된 둘 이상의 테넌트가 있는 경우 TenantId 매개 변수를 지정합니다.
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> 이와 같은 비대화형 로그인 방법은 회사 또는 학교 계정으로만 작동합니다. 회사 또는 학교 계정은 회사나 학교에서 관리되는 사용자이며, 회사나 직장에 대한 Azure Active Directory 인스턴스에 정의됩니다. 현재 회사 또는 학교 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 해당 계정을 쉽게 만들 수 있습니다.
> 
> 1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인한 다음 **Active Directory**를 클릭합니다.
> 2. 디렉터리가 없는 경우 **디렉터리 만들기**를 선택한 다음 요청된 정보를 제공합니다.
> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 이 새 사용자는 회사 또는 학교 계정을 사용하여 서명할 수 있습니다. 사용자를 만드는 동안 사용자의 전자 메일 주소와 임시 암호가 제공됩니다. 이 정보는 아래의 5단계에서 사용되므로 저장해 두세요.
> 4. Azure 클래식 포털에서 **설정**을 선택한 다음 **관리자**를 선택합니다. **추가**를 선택한 다음 새 사용자를 공동 관리자로 추가합니다. 그러면 회사 또는 학교 계정으로 Azure 구독을 관리할 수 있습니다.
> 5. 마지막으로, Azure 클래식 포털에서 로그아웃한 후 새 회사 또는 학교 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.
> 
> 회사 또는 학교 계정으로 Microsoft Azure를 등록하는 것에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록](active-directory/sign-up-organization.md)을 참조하세요.
> 
> Azure에서 인증 및 구독 관리에 대한 자세한 내용은 [계정, 구독 및 관리 역할 관리](http://go.microsoft.com/fwlink/?LinkId=324796)를 참조하세요.
> 
> 

### <a name="view-account-and-subscription-details"></a>계정 및 구독 세부 정보 보기
Azure PowerShell에서 사용 가능한 계정과 구독이 여러 개 있을 수 있습니다. 계정을 추가할 때마다 **Add-AzureRmAccount**를 실행하면 여러 계정을 추가할 수 있습니다.

사용 가능한 Azure 계정을 표시하려면 **Get-AzureAccount**를 입력합니다.

Azure 구독을 표시하려면 **Get-AzureRmSubscription**을 입력합니다.

## <a name="a-idhelpagetting-help"></a><a id="Help"></a>도움말 보기
다음 리소스는 특정 cmdlet에 대한 도움말을 제공합니다.

* 콘솔 내에서 기본 제공 도움말 시스템을 사용할 수 있습니다. **Get-help** cmdlet은 이 시스템에 대한 액세스를 제공합니다. 
* 커뮤니티에서 도움을 얻으려면 다음과 같은 포럼을 참조하십시오.
  
  * [MSDN의 azure 포럼](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## <a name="learn-more"></a>자세한 정보
Cmdlet을 사용하는 방법에 대한 자세한 내용을 보려면 다음 리소스를 참조하세요.

Windows PowerShell을 사용하기 위한 기본 지침은 [Windows PowerShell 사용](http://go.microsoft.com/fwlink/p/?LinkId=321939)을 참조하세요.

cmdlet에 대한 참조 정보는 [Azure Cmdlet 참조](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx)를 참조하세요.

스크립팅을 사용하여 Azure를 관리하는 방법을 익히는 데 도움이 되는 샘플 스크립트 및 지침은 [스크립트 센터](http://go.microsoft.com/fwlink/p/?LinkId=321940)를 참조하세요.




<!--HONumber=Nov16_HO3-->


