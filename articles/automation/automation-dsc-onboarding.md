<properties 
   pageTitle="Azure 자동화 DSC를 통한 관리를 위한 물리적 및 가상 컴퓨터 온보드| Microsoft Azure" 
   description="Azure 자동화 DSC를 통한 관리를 위한 컴퓨터 설정 방법" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="10/28/2015"
   ms.author="coreyp"/>

# Azure 자동화 DSC를 통한 관리를 위한 컴퓨터 온보드

## Azure 자동화 DSC로 컴퓨터를 관리하는 이유

[PowerShell 필요 상태 구성](https://technet.microsoft.com/library/dn249912.aspx)과 마찬가지로 Azure 자동화 필요 상태 구성은 클라우드나 온-프레미스 데이터 센터의 DSC 노드(물리적 및 가상 컴퓨터)를 위한 간단하지만 강력한 구성 관리 서비스입니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. Azure 자동화 관리 계층이 PowerShell 스크립팅에 대한 것이라면 Azure 자동화 DSC 관리 계층은 DSC에 대한 것입니다. 즉 Azure 자동화가 Powershell 스크립트 관리를 지원하는 것과 동일한 방식으로 DSC 구성을 지원합니다. Azure 자동화 DSC의 이점을 확인하려면 [Azure 자동화 DSC 개요](automation-dsc-overview/)를 참조하세요.

Azure 자동화 DSC를 다양한 컴퓨터의 관리에 사용할 수 있습니다.

*    Azure 가상 컴퓨터(기본)
*    Azure 가상 컴퓨터
*    온-프레미스나 Azure 이외의 클라우드에 있는 실제/가상 Windows 컴퓨터
*    온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

다음 섹션에서는 Azure 자동화 DSC에 대해 각 컴퓨터 형식을 온보드하는 방법을 간략히 설명합니다.

## Azure 가상 컴퓨터(기본)

Azure 자동화 DSC를 사용하면 Azure 포털이나 PowerShell을 사용하는 구성 관리를 위해 Azure 가상 컴퓨터(기본)를 간편하게 온보드할 수 있습니다. 내부적으로, VM에 대한 관리자의 원격 작업 없이 Azure VM 필요 상태 구성 확장이 VM을 Azure 자동화 DSC에 등록합니다. Azure VM 필요 상태 구성 확장은 비동기적으로 실행되므로 진행 상황을 추적하거나 문제를 해결하기 위한 절차는 아래의 [**Azure 가상 컴퓨터 온보드 문제 해결**](#troubleshooting-azure-virtual-machine-onboarding) 섹션에서 제공합니다.


### Azure 가상 컴퓨터

[Azure Preview 포털](http://portal.azure.com/)에서 **찾아보기** -> **가상 컴퓨터(기본)**를 클릭합니다. 온보드할 Windows VM을 선택합니다. 가상 컴퓨터의 대시보드 블레이드에서 **모든 설정** -> **확장** -> **추가** -> **Azure 자동화 DSC** -> **만들기**를 클릭합니다. 사용 사례에 필요한 [PowerShell DSC 로컬 구성 관리자 값](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396), 자동화 계정의 등록 키 및 등록 URL과, 선택적으로 VM에 할당할 노드 구성을 입력합니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


컴퓨터를 온보드하기 위해 자동화 계정에 대한 등록 URL과 키를 찾으려면 아래의 [**등록 보호**](#secure-registration) 섹션을 참조합니다.

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Login-AzureRmAccount
    
    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these DSC agent Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
       RegistrationKey = @{
         UserName = 'notused'
         Password = 'PrivateSettingsRef:RegistrationKey'
    }
      RegistrationUrl = $RegistrationInfo.Endpoint
      NodeConfigurationName = $NodeConfigName
      ConfigurationMode = "ApplyAndMonitor"
      ConfigurationModeFrequencyMins = 15
      RefreshFrequencyMins = 30
      RebootNodeIfNeeded = $False
      ActionAfterReboot = "ContinueConfiguration"
      AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension `
     -VM $vm `
     -Publisher Microsoft.Powershell `
     -ExtensionName DSC `
     -Version 2.6 `
     -PublicConfiguration $PublicConfiguration `
     -PrivateConfiguration $PrivateConfiguration
     -ForceUpdate

    $VM | Update-AzureVM

## Azure 가상 컴퓨터

Azure 자동화 DSC를 사용하면 Azure 포털, Azure 리소스 관리자 템플릿 또는 PowerShell을 사용하는 구성 관리를 위해 Azure 가상 컴퓨터를 간편하게 온보드할 수 있습니다. 내부적으로, VM에 대한 관리자의 원격 작업 없이 Azure VM 필요 상태 구성 확장이 VM을 Azure 자동화 DSC에 등록합니다. Azure VM 필요 상태 구성 확장은 비동기적으로 실행되므로 진행 상황을 추적하거나 문제를 해결하기 위한 절차는 아래의 [**Azure 가상 컴퓨터 온보드 문제 해결**](#troubleshooting-azure-virtual-machine-onboarding) 섹션에서 제공합니다.


### Azure 포털

[Azure 미리 보기 포털](http://portal.azure.com/)에서 가상 컴퓨터를 온보드할 Azure 자동화 계정으로 이동합니다. 자동화 계정 대시보드에서 **DSC 노드** -> **Azure VM 추가**를 클릭합니다.

**온보드할 가상 컴퓨터 선택**에서 온보드할 하나 이상의 Azure 가상 컴퓨터를 선택합니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


**등록 데이터 구성**에서 사용 사례에 필요한 [PowerShell DSC 로컬 구성 관리자 값](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396)과, 선택적으로 VM에 할당할 노드 구성을 입력합니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Azure 리소스 관리자 템플릿

Azure 가상 컴퓨터는 Azure 리소스 관리자 템플릿을 통해 Azure 자동화 DSC에 배포 및 온보드할 수 있습니다. 기존 VM을 Azure 자동화 DSC에 온보드하는 템플릿 예는 [DSC 확장 및 Azure 자동화 DSC를 통한 VM 구성](http://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/)을 참조하세요. 이 템플릿에서 입력으로 가져온 등록 키와 등록 URL을 찾으려면 아래의 [**등록 보호**](#secure-registration) 섹션을 참조하세요.

### PowerShell

The [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt244097.aspx?f=255&MSPPError=-2147217396) cmdlet를 PowerShell을 통한 Azure Preview 포털의 가상 컴퓨터를 온보드하는 데 사용할 수 있습니다.

### 온-프레미스나 Azure 이외의 클라우드에 있는 실제/가상 Windows 컴퓨터

온-프레미스 Windows 컴퓨터와 비 Azure 클라우드(예: Amazon Web Services)의 Windows 컴퓨터도 인터넷에 대한 아웃바운드 액세스 권한이 있다면 Azure 자동화 DSC에 간단한 절차를 통해 온보드할 수 있습니다.

1. 최신 버전의 [WMF 5](http://www.microsoft.com/ko-KR/download/details.aspx?id=48729)가 Azure 자동화 DSC에 온보드하려는 컴퓨터에 설치되었는지 확인합니다.

2. 로컬 환경의 컴퓨터에서 관리자 권한으로 PowerShell 콘솔이나 PowerShell ISE를 엽니다. 이 컴퓨터에도 최신 버전의 WMF 5가 설치되어 있어야 합니다.

3. Azure PowerShell 모듈을 사용하여 Azure 리소스 관리자에 연결합니다:`Login-AzureRmAccount`

4. 노드를 온보드할 자동화 계정에서 온보드하려는 컴퓨터에 대한 PowerShell DSC 메타 구성을 다운로드합니다.

	`Get-AzureRmAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName      		MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop`

5. 선택적으로 출력 폴더에서 메타 구성을 확인하고 기본값이 사용 사례에 맞지 않으면 필요에 따라 [PowerShell DSC 로컬 구성 관리자 필드 및 값](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396)에 맞게 업데이트합니다.

6. 온보드할 컴퓨터에 PowerShell DSC 메타 구성을 원격으로 적용합니다.

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

7. PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 4단계의 출력 폴더를 온보드할 각 컴퓨터에 복사합니다. 그런 다음 온보드할 각 컴퓨터에서 Set-DscLocalConfigurationManager를 로컬로 호출합니다.

8. Azure 포털 또는 cmdlet를 사용하여 Azure 자동화 계정에서 등록된 DSC 노드로 온보드할 컴퓨터가 표시되는지 확인합니다.

### 온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

온-프레미스 Linux 컴퓨터, Azure의 Linux 컴퓨터 및 비 Azure 클라우드의 Linux 컴퓨터 또한 몇 가지 간단한 절차를 통해 인터넷으로의 아웃 바운드 액세스 권한만큼 Azure 자동화 DSC에 온보드할 수 있습니다.

1. ㅁ최신 버전의 [DSC Linux 에이전트](http://www.microsoft.com/ko-KR/download/details.aspx?id=49150)가 Azure 자동화 DSC에 온보드하려는 컴퓨터에 설치되었는지 확인합니다.

2. [PowerShell DSC 로컬 구성 관리자 기본값](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396)이 사용 사례와 일치하는 경우

	*    Azure 자동화 DSC에 온보드할 각 Linux 컴퓨터에서 PowerShell DSC 로컬 구성 관리자 기본값으로 Register.py를 사용하여 온보드합니다.

		`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

	*    자동화 계정에 대한 등록 키와 등록 URL을 확인하려면 아래의 [**등록 보호**](#secure-registration) 섹션을 참조하세요.

	PowerShell DSC 로컬 구성 관리자 기본값이 사용 사례와 일치**하지** **않는** 경우 3-9단계를 따릅니다. 그렇지 않으면 9단계로 직접 이동합니다.

3. 로컬 환경의 Windows 컴퓨터에서 관리자 권한으로 PowerShell 콘솔이나 PowerShell ISE를 엽니다. 이 컴퓨터에는 최신 버전의 [WMF 5](http://www.microsoft.com/ko-KR/download/details.aspx?id=48729)가 설치되어 있어야 합니다.

4. Azure PowerShell 모듈을 사용하여 Azure 리소스 관리자에 연결합니다:

	`Login-AzureRmAccount`

5.  노드를 온보드할 자동화 계정에서 온보드하려는 컴퓨터에 대한 PowerShell DSC 메타 구성을 다운로드합니다.
	
	`Get-AzureRmAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop_`

6.  선택적으로 출력 폴더에서 메타 구성을 확인하고 기본값이 사용 사례에 맞지 않으면 필요에 따라 [PowerShell DSC 로컬 구성 관리자 필드 및 값](http://https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396)에 맞게 업데이트합니다.

7.  온보드할 컴퓨터에 PowerShell DSC 메타 구성을 원격으로 적용합니다.
    	
    	$SecurePass = ConvertTo-SecureString -string "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurPass
        $Opt = New-CimSessionOption -UseSs1:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential:$Cred -ComputerName:<your Linux machine> -Port:5986 -Authentication:basic -SessionOption:$Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs

8.  PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 온보드할 각 Linux 컴퓨터에 대해 5단계의 폴더에서 해당 컴퓨터에 대한 메타 구성을 Linux 컴퓨터에 복사합니다. 그런 다음 Azure 자동화 DSC에 온보드할 각 Linux 컴퓨터에서 로컬로 `SetDscLocalConfigurationManager.py`를 호출합니다.

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

9.  Azure 포털 또는 cmdlet를 사용하여 Azure 자동화 계정에서 등록된 DSC 노드로 온보드할 컴퓨터가 표시되는지 확인합니다.

##등록 보호

PowerShell DSC 풀 또는 보고 서버(Azure 자동화 DSC 포함)에 대한 DSC 노드 인증을 허용하는 V2WMF 5 DSC 등록 프로토콜을 통해 Azure 자동화 계정에 컴퓨터를 안전하게 온보드할 수 있습니다. 노드는 **등록 URL**에 있는 서버에 등록되며 **등록 키**를 통해 인증됩니다. 등록하는 동안 DSC 노드와 DSC 풀/보고 서버가 이 노드에 대해, 서버 게시-등록에 대한 인증에 사용할 고유 인증서를 협상합니다. 이 프로세스는 노드가 손상되어 악의적 동작을 수행하는 등, 온보드된 노드가 다른 노드를 가장하는 것을 방지합니다. 등록 후 해당 등록 키는 다시 인증에 사용되지 않으며 노드에서 삭제됩니다.

Azure Preview 포털의 **키 관리** 블레이드에서 DSC 등록 프로토콜에 필요한 정보를 가져올 수 있습니다. 자동화 계정의 **Essentials** 패널에서 키 아이콘을 클릭하여 이 블레이드를 엽니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    등록 URL은 키 관리 블레이드의 URL 필드입니다.
*    등록 키는 키 관리 블레이드에서 주기본 액세스 키 또는 보조 액세스 키입니다. 둘 중 하나를 사용할 수 있습니다.

이전 키를 사용한 추가적인 노드 등록 차단을 위해 자동화 계정의 기본 액세스 키와 보조 액세스 키는 언제든 다시 생성하여 보안을 강화할 수 있습니다(**키 관리** 블레이드에서).

##Azure 가상 컴퓨터 온보드 문제 해결

Azure 자동화 DSC를 사용하면 구성 관리를 위해 간편하게 Azure Windows VM을 온보드할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure 자동화 DSC에 등록하는 데 사용됩니다. Azure VM 필요 상태 구성 확장은 비동기적으로 실행되므로 그 진행 상황 추적과 실행 문제 해결이 중요할 수 있습니다.

>[AZURE.NOTE]Azure VM 필요 상태 구성 확장을 사용하는 Azure 자동화 DSC에 Azure Windows VM을 온보드하는 모든 방법에서 노드가 Azure 자동화에 등록된 것으로 나타나는 데는 최대 1시간이 소요됩니다. 이것은 Azure 자동화 DSC의 VM을 온보드하기 위해 필요한 Azure VM DSC 확장에 의한 VM의 Windows 관리 프레임워크 5.0의 설치 때문에 꼭 필요한 일입니다.

Azure VM 필요 상태 구성 확장의 상태를 보거나 문제를 해결하려면 Azure Preview 포털에서 온보드할 VM으로 이동한 다음 **모든 설정** -> **확장** -> **DSC**를 클릭합니다. 자세한 내용을 보려면 **자세한 상태 보기**를 클릭합니다.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)로 바꿉니다.

## 인증서 만료 및 다시 등록

등록 후에는 1년 후 만료되는 인증에 대해 각 노드가 자동으로 고유의 인증서를 협상합니다. 현재 PowerShell DSC 등록 프로토콜이 만료가 임박한 인증서를 자동으로 갱신할 수 없으므로 1년 기한 후 노드를 다시 등록해야 합니다. 다시 등록하기 전에 각 노드가 Windows Management Framework 5.0 RTM을 실행 중인지 확인합니다. 노드의 인증 인증서가 만료되고 노드가 다시 등록되지 않은 경우, 노드가 Azure 자동화와 통신할 수 없으며 '응답 없음'으로 표시됩니다. 재등록은 노드를 처음 등록할 때와 같은 방식으로 수행됩니다. 노드 만료 시점으로부터 90일 안이나, 인증서 만료 이후에 재등록을 수행하면 새 인증서를 생성하여 사용하게 됩니다.

## 관련 문서
* [Azure 자동화 DSC 개요](automation-dsc-overview.md)
* [Azure 자동화 DSC cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure 자동화 DSC 가격 책정](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=Nov15_HO1-->