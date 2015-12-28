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
	ms.date="12/02/2015"
	ms.author="coreyp"/>

# Azure PowerShell을 설치 및 구성하는 방법#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Azure PowerShell이란?#
Azure PowerShell은 Windows PowerShell로 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. cmdlet을 사용하여 Azure 플랫폼을 통해 제공되는 솔루션과 서비스를 만들고 테스트, 배포 및 관리할 수 있습니다. 대부분의 경우 cmdlet은 클라우드 서비스, 가상 컴퓨터, 가상 네트워크 및 웹앱 만들기 및 구성과 같이 Azure 관리 포털과 동일한 작업에 사용할 수 있습니다.

모듈 및 소스 코드는 공개적으로 사용 가능한 리포지토리에서 다운로드할 수 있습니다.

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Azure PowerShell 1.0.1 소스 코드](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## 1단계: 설치
[PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)을 다운로드하여 설치합니다. <a id="Connect"></a>

## 2단계: 시작
모듈은 Azure PowerShell에 대한 사용자 지정 콘솔을 설치합니다. 표준 Windows PowerShell 콘솔이나 Azure PowerShell 콘솔에서 cmdlet을 실행할 수 있습니다.

## 3단계: 연결
서비스를 관리할 수 있도록 cmdlet에 구독이 필요합니다. Azure 구독이 아직 없는 경우 구매할 수 있습니다. 자세한 내용은 [Azure 구입 방법](http://go.microsoft.com/fwlink/p/?LinkId=320795)을 참조하세요.

1. **Add-AzureAccount**를 입력합니다.

2. 계정과 연결된 메일 주소 및 암호를 입력합니다. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

-또는-

회사 또는 학교 계정에 로그인합니다.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE]이 비 대화형 로그인 방법은 회사 또는 학교 계정으로만 작동합니다. 회사 또는 학교 계정은 회사나 학교에서 관리되는 사용자이며, 회사나 직장에 대한 Azure Active Directory 인스턴스에 정의됩니다. 현재 회사나 학교 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.

> 1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인한 후 **Active Directory**를 클릭합니다.

> 2. 디렉터리가 없는 경우 **디렉터리 만들기**를 선택하고 요청된 정보를 제공합니다.

> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 이 새 사용자는 회사 또는 학교 계정을 사용하여 서명할 수 있습니다. 사용자를 만드는 동안 사용자의 전자 메일 주소와 임시 암호가 제공됩니다. 이 정보는 아래의 5단계에서 사용되므로 저장해 두세요.

> 4. 관리 포털에서 **설정**을 선택한 후 **관리자**를 선택합니다. **추가**를 선택하고 새 사용자를 공동 관리자로 추가합니다. 따라서 회사 또는 학교 계정이 Azure 구독을 관리할 수 있습니다.

> 5. 마지막으로, Azure 포털에서 로그아웃한 후 새 회사나 학교 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.

> 회사 또는 학교 계정을 사용한 Microsoft Azure 서명에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록](sign-up-organization.md)을 참조하십시오.

### 계정 및 구독 세부 정보 보기

Azure PowerShell에서 사용 가능한 계정과 구독이 여러 개 있을 수 있습니다. **Add-AzureAccount**를 여러 번 실행하면 여러 계정을 추가할 수 있습니다.

사용 가능한 Azure 계정을 표시하려면 **Get-AzureAccount**를 입력합니다.

Azure 구독을 표시하려면 **Get-AzureSubscription**을 입력합니다.

## 4단계: 테스트<a id="Ex"></a>


모듈을 설치하고 구독에 연결하도록 컴퓨터를 구성한 후 Azure 웹앱을 만들어 모두 작동하는지 확인합니다.

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


-   콘솔 내에서 기본 제공 도움말 시스템을 사용할 수 있습니다. **Get-Help** cmdlet은 이 시스템에 대한 액세스 권한을 제공합니다. 



- Azure 라이브러리에서 Azure PowerShell 모듈의 cmdlet에 대한 참조 정보를 사용할 수도 있습니다. 자세한 내용은 [Azure Cmdlet 참조](https://msdn.microsoft.com/library/azure/dn708514.aspx)를 확인하십시오.

커뮤니티에서 도움을 얻으려면 다음과 같은 포럼을 참조하십시오.

- [MSDN의 Azure 포럼](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1217_2015-->