---
title: Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드
description: Azure 자동화 상태 구성으로 관리를 위한 컴퓨터를 설정하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c718b9a66b378044618c8c52eec3a1a498ace83c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383197"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Azure Automation 상태 구성으로 머신을 관리하는 이유는?

Azure 자동화 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터의 원하는 상태 구성(DSC) 노드에 대한 구성 관리 서비스입니다. **구성 관리**에서 **상태 구성(DSC)을** 선택하여 Azure 포털에서 액세스합니다. 

이 서비스를 통해 중앙의 안전한 위치에서 수천 대의 기계에서 빠르고 쉽게 확장성을 구현할 수 있습니다. 컴퓨터를 쉽게 온보기하고 선언적 구성을 할당하고 각 컴퓨터가 지정한 원하는 상태를 준수하는 지 보여주는 보고서를 볼 수 있습니다.

Azure 자동화 상태 구성 서비스는 Azure 자동화 실행책이 PowerShell 스크립팅에 어떤 것인지 DSC에 대한 것입니다. 즉, Azure Automation에서 PowerShell 스크립트 관리를 지원하는 동일한 방법으로 DSC 구성 관리를 지원합니다. Azure Automation 상태 구성을 사용하는 이점에 대해 자세히 알려면 [Azure Automation 상태 구성 개요](automation-dsc-overview.md)를 참조하세요.

Azure Automation 상태 구성을 다양한 머신의 관리에 사용할 수 있습니다.

- Azure 가상 머신
- Azure 가상 머신(기본)
- 온-프레미스 또는 Azure 이외의 클라우드(AWS EC2 인스턴스 포함) 물리적/가상 Windows 컴퓨터
- 온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

클라우드에서 컴퓨터 구성을 관리할 준비가 되지 않은 경우 Azure 자동화 상태 구성을 보고서 전용 끝점으로 사용할 수 있습니다. 이 기능을 사용하면 DSC를 통해 구성을 설정(푸시)하고 Azure Automation에서 보고 세부 정보를 볼 수 있습니다.

> [!NOTE]
> 설치된 Azure VM 원하는 상태 구성 확장 버전이 2.70보다 큰 경우 Azure 자동화 상태 구성을 통해 Azure VM을 관리하는 것은 추가 비용 없이 포함됩니다. 자세한 내용은 [**자동화 가격 책정 페이지를**](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.

이 문서의 다음 섹션에서는 위에 나열된 컴퓨터를 Azure 자동화 상태 구성에 온보온하는 방법을 설명합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="onboarding-azure-vms"></a>Azure VM 온보딩

Azure 자동화 상태 구성을 사용하면 Azure 포털, Azure 리소스 관리자 템플릿 또는 PowerShell을 사용하여 구성 관리를 위해 Azure VM을 쉽게 온보싱할 수 있습니다. 관리자가 VM으로 원격을 입력하지 않아도 되는 경우 Azure VM 원하는 상태 구성 확장은 Azure 자동화 상태 구성으로 VM을 등록합니다. Azure 확장이 비동기적으로 실행되므로 진행 상황을 추적하거나 문제를 해결하는 단계는 이 문서의 [문제 해결 Azure 가상 시스템 온보딩](#troubleshooting-azure-virtual-machine-onboarding) 섹션에 제공됩니다.

> [!NOTE]
>Linux 노드에 DSC를 배포하려면 **/tmp** 폴더를 사용합니다. 같은 `nxautomation` 모듈은 해당 위치에 설치하기 전에 확인을 위해 일시적으로 다운로드됩니다. 모듈이 올바르게 설치되도록 하려면 Linux용 로그 분석 에이전트에서 **/tmp** 폴더에 대한 읽기/쓰기 권한이 필요합니다.<br><br>
>Linux용 로그 분석 에이전트는 사용자로 실행됩니다. `omsagent` `omsagent` 사용자에게 쓰기 권한을 >권한을 부여하려면 `setfacl -m u:omsagent:rwx /tmp`명령을 실행합니다.

### <a name="onboard-a-vm-using-azure-portal"></a>Azure 포털을 사용하여 VM 온보로

Azure 포털을 통해 Azure VM을 Azure 자동화 상태 구성에 온보덕인하려면 [다음을 수행합니다.](https://portal.azure.com/)

1. VM을 온보온할 Azure 자동화 계정으로 이동합니다. 

2. 상태 구성 페이지에서 노드 탭을 선택한 다음 **에 추가를** **클릭합니다.**

3. 온보던할 VM을 선택합니다.

4. 컴퓨터에 PowerShell 원하는 상태 확장이 설치되어 있지 않고 전원 상태가 실행 중인 경우 **연결을**클릭합니다.

5. **등록에서**사용 사례에 필요한 [PowerShell DSC 로컬 구성 관리자 값을](/powershell/scripting/dsc/managing-nodes/metaConfig) 입력합니다. 선택적으로 노드 구성을 입력하여 VM에 할당할 수 있습니다.

![온보딩](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿을 사용하여 VM온보보드

Azure 리소스 관리자 템플릿을 사용하여 VM을 Azure 자동화 상태 구성에 배포하고 온보싱할 수 있습니다. 기존 VM을 Azure 자동화 상태 구성에 온보팅하는 예제 템플릿은 [원하는 상태 구성 서비스에서 관리하는 서버를](https://azure.microsoft.com/resources/templates/101-automation-configuration/) 참조하십시오. VM 규모 집합을 관리하는 경우 [Azure Automation에서 관리하는 VM 규모 집합 구성의](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)예제 템플릿을 참조하십시오.

### <a name="onboard-machines-using-powershell"></a>PowerShell을 사용하는 온보드 머신

PowerShell의 [레지스터-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet을 사용하여 VM을 Azure 자동화 상태 구성에 온보딩할 수 있습니다. 

> [!NOTE]
>`Register-AzAutomationDscNode` cmdlet은 현재 Windows 확장을 실행하는 컴퓨터에 대해서만 구현됩니다.

### <a name="register-vms-across-azure-subscriptions"></a>Azure 구독에서 VM 등록

다른 Azure 구독에서 VM을 등록하는 가장 좋은 방법은 Azure 리소스 관리자 배포 템플릿에서 DSC 확장을 사용하는 것입니다. 예제는 Azure [리소스 관리자 템플릿을 사용 하](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)고 원하는 상태 구성 확장에 제공 됩니다.

템플릿에서 매개 변수로 사용할 등록 키 및 등록 URL을 찾으려면 이 문서의 등록 섹션을 [사용하여 온보딩을 참조하십시오.](#onboarding-securely-using-registration)

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>온-프레미스 또는 Azure 이외의 클라우드(AWS EC2 인스턴스 포함)에서 물리적/가상 Windows 컴퓨터를 온보딩

온-프레미스 또는 다른 클라우드 환경에서 실행되는 Windows 서버를 Azure 자동화 상태 구성에 온보덕할 수 있습니다. 서버는 Azure 에 [대한 아웃바운드 액세스 권한이](automation-dsc-overview.md#network-planning)있어야 합니다.

1. 최신 버전의 [WMF 5가](https://aka.ms/wmf5latest) 컴퓨터에 설치되어 Azure 자동화 상태 구성에 온보로 전송되는지 확인합니다. 또한 WMF 5는 온보딩 작업에 사용하는 컴퓨터에 설치해야 합니다.
1. [DSC 메타구성](#generating-dsc-metaconfigurations) 생성 섹션의 지침을 따라 필요한 DSC 메타구성이 포함된 폴더를 만듭니다. 
1. 다음 cmdlet을 사용하여 PowerShell DSC 메타구성을 온보드하려는 컴퓨터에 원격으로 적용합니다. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC 메타구성을 원격으로 적용할 수 없는 경우 **메타구성 폴더를** 온보딩중인 컴퓨터에 복사합니다. 그런 다음 컴퓨터에서 `Set-DscLocalConfigurationManager` 로컬로 호출할 코드를 추가합니다.
1. Azure 포털 또는 cmdlet을 사용하여 온보딩할 컴퓨터가 Azure Automation 계정에 등록된 상태 구성 노드로 나타나는지 확인합니다.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>온-프레미스 또는 Azure 이외의 클라우드에서 물리적/가상 Linux 컴퓨터를 온보딩

온-프레미스 또는 다른 클라우드 환경에서 실행되는 Linux 서버를 Azure 자동화 상태 구성에 온보덕일 수 있습니다. 서버는 Azure 에 [대한 아웃바운드 액세스 권한이](automation-dsc-overview.md#network-planning)있어야 합니다.

1. [Linux용 PowerShell 원하는 상태 구성의](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 최신 버전이 컴퓨터에 설치되어 Azure 자동화 상태 구성에 온보로 전송되는지 확인합니다.
2. [PowerShell DSC 로컬 구성 관리자 기본값이](/powershell/scripting/dsc/managing-nodes/metaConfig4) 사용 사례와 일치하고 컴퓨터를 온보딩하여 둘 다 Azure 자동화 상태 구성으로 가져오고 보고하도록 하려는 경우:

   - Azure 자동화 상태 구성에 온보딩하는 `Register.py` 각 Linux 컴퓨터에서 PowerShell DSC 로컬 구성 관리자 기본값을 사용하여 온보딩하는 데 사용합니다.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 자동화 계정의 등록 키 및 등록 URL을 찾으려면 이 문서의 등록 섹션을 [사용하여 온보딩을 안전하게](#onboarding-securely-using-registration) 참조하십시오.

3. PowerShell DSC 로컬 구성 관리자(LCM) 기본값이 사용 사례와 일치하지 않거나 Azure 자동화 상태 구성에만 보고하는 컴퓨터를 온보딩하려는 경우 4-7단계를 따릅니다. 그렇지 않으면 7단계로 직접 진행합니다.

4. 생성 DSC 메타구성 섹션의 지침을 따라 필요한 [DSC 메타구성이](#generating-dsc-metaconfigurations) 포함된 폴더를 생성합니다.

5. 최신 버전의 [WMF 5가](https://aka.ms/wmf5latest) 온보딩에 사용되는 컴퓨터에 설치되어 있는지 확인합니다.

6. 다음과 같이 코드를 추가하여 PowerShell DSC 메타구성을 온보딩하려는 컴퓨터에 원격으로 적용합니다.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. PowerShell DSC 메타구성을 원격으로 적용할 수 없는 경우 4단계에서 설명한 폴더에서 원격 컴퓨터에 해당하는 메타 구성을 Linux 컴퓨터로 복사합니다.

8. Azure 자동화 `Set-DscLocalConfigurationManager.py` 상태 구성에 온보온에 각 Linux 컴퓨터에서 로컬로 호출하는 코드를 추가합니다.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Azure 포털 또는 cmdlet을 사용하여 온보딩할 컴퓨터가 이제 Azure 자동화 계정에 등록된 DSC 노드로 표시되도록 합니다.

## <a name="generating-dsc-metaconfigurations"></a>DSC 메타 구성 생성

모든 컴퓨터를 Azure 자동화 상태 구성에 온보딩하려면 [DSC 메타구성을](/powershell/scripting/dsc/managing-nodes/metaConfig)생성할 수 있습니다. 이 구성은 DSC 에이전트가 Azure 자동화 상태 구성에서 가져오거나 보고하도록 지시합니다. PowerShell DSC 구성 또는 Azure 자동화 PowerShell cmdlet을 사용하여 Azure 자동화 상태 구성에 대한 DSC 메타구성을 생성할 수 있습니다.

> [!NOTE]
> DSC 메타 구성은 관리를 위해 Automation 계정에 컴퓨터를 등록하는 데 필요한 암호를 포함합니다. 사용한 후에 만들거나 삭제한 DSC 메타 구성을 제대로 보호해야 합니다.

메타 구성에 대한 프록시 지원은 Windows PowerShell DSC 엔진인 LCM에 의해 제어됩니다. LCM은 모든 대상 노드에서 실행되며 DSC 메타구성 스크립트에 포함된 구성 리소스를 호출합니다. 에 필요에 따라 프록시 URL 및 프록시 자격 증명의 정의를 포함 하 여 `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`메타 `ReportServerWeb` 구성에 프록시 지원을 포함할 수 있습니다. [로컬 구성 관리자 구성](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)을 참조하십시오.

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC 구성을 사용하여 DSC 메타구성 생성

1. 로컬 환경에서 컴퓨터에서 관리자로 VSCode(또는 즐겨 찾는 편집기)를 엽니다. 이 컴퓨터에는 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.
1. 다음 스크립트를 로컬로 복사합니다. 이 스크립트는 메타 구성을 만들기 위한 PowerShell DSC 구성 및 메타 구성 생성을 시작하는 명령을 포함합니다.

    > [!NOTE]
    > 상태 구성 노드 구성 이름은 Azure 포털에서 대/소문자를 구분합니다. 케이스가 일치하지 않으면 노드 탭 아래에 **노드가** 표시되지 않습니다.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. 등록할 컴퓨터의 이름 뿐만 아니라 Automation 계정에 대한 등록 키 및 URL을 입력합니다. 모든 다른 매개 변수는 선택 사항입니다. 자동화 계정의 등록 키 및 등록 URL을 찾으려면 등록 섹션을 [사용하여 온보딩을 참조하십시오.](#onboarding-securely-using-registration)

1. 컴퓨터가 DSC 상태 정보를 Azure 자동화 상태 구성에 보고하지만 구성 또는 PowerShell 모듈을 가져오지 않도록 하려면 매개 변수를 `ReportOnly` true로 설정합니다.

1. 설정되지 않은 경우 `ReportOnly` 컴퓨터는 DSC 상태 정보를 Azure 자동화 상태 구성에 보고하고 구성 또는 PowerShell 모듈을 가져옵니다. 에 이에 `ConfigurationRepositoryWeb`따라 매개 `ResourceRepositoryWeb`변수를 설정합니다. `ReportServerWeb`

1. 스크립트를 실행합니다. 이제 컴퓨터가 온보딩할 수 있는 PowerShell DSC 메타구성을 포함하는 **DscMetaConfigs라는**작업 디렉토리 폴더가 있어야 합니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Azure 자동화 cmdlet을 사용하여 DSC 메타구성 생성

PowerShell DSC LCM 기본값이 사용 사례와 일치하고 컴퓨터를 온보딩하여 Azure 자동화 상태 구성으로 가져오고 보고하려는 경우 Azure 자동화 cmdlet을 사용하여 필요한 DSC 메타구성을 더 간단하게 생성할 수 있습니다.

1. 로컬 환경에서 컴퓨터에서 관리자로 PowerShell 콘솔 또는 VSCode를 엽니다.
2. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)를 사용하여 Azure 리소스 관리자에 연결합니다.
3. 노드를 설정하는 자동화 계정에서 온보딩하려는 컴퓨터에 대한 PowerShell DSC 메타구성을 다운로드합니다.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 이제 관리자로 온보딩할 컴퓨터에 대한 PowerShell DSC 메타구성이 포함된 **DscMetaConfigs** 폴더가 있어야 합니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>등록을 사용하여 안전하게 온보딩

컴퓨터는 WMF 5 DSC 등록 프로토콜을 통해 Azure 자동화 계정에 안전하게 온보딩할 수 있습니다. 이 프로토콜을 사용하면 DSC 노드가 Azure 자동화 상태 구성을 포함하여 PowerShell DSC 끌어오기 또는 보고서 서버에 인증할 수 있습니다. 노드는 등록 URL에서 서버에 등록하고 등록 키를 사용하여 인증합니다. 등록 하는 동안 DSC 노드 및 DSC 풀/보고서 서버 는 등록 후 서버에 대 한 인증에 사용할 노드에 대 한 고유 인증서를 협상 합니다. 이 프로세스는 노드가 손상되어 악의적으로 행동하는 경우와 같은 온보딩된 노드가 서로 가장하는 것을 방지합니다. 등록 후 등록 키는 인증에 다시 사용 되지 않습니다 및 노드에서 삭제 됩니다.

Azure Portal의 **계정 설정** 아래에 있는 **키**에서 상태 구성 등록 프로토콜에 필요한 정보를 얻을 수 있습니다. 

![Azure Automation 키 및 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 등록 URL은 키 페이지의 URL 필드입니다.
- 등록 키는 **기본 액세스 키** 필드 또는 키 페이지의 보조 액세스 **키** 필드의 값입니다. 둘 중 하나를 사용할 수 있습니다.

보안을 강화하려면 키 페이지에서 언제든지 자동화 계정의 기본 및 보조 액세스 키를 다시 생성할 수 있습니다. 키 재생성은 이후 노드 등록이 이전 키를 사용하지 못하도록 합니다.

## <a name="re-registering-a-node"></a>노드 다시 등록

Azure 자동화 상태 구성에서 컴퓨터를 DSC 노드로 등록한 후 나중에 해당 노드를 다시 등록해야 하는 몇 가지 이유가 있습니다.

- **인증서 갱신.** Windows Server 2019 이전의 Windows 서버 버전의 경우 각 노드는 1년 후에 만료되는 인증을 위해 고유한 인증서를 자동으로 협상합니다. 인증서가 갱신되지 않고 만료되면 노드가 Azure 자동화와 통신할 수 없으며 로 표시됩니다. `Unresponsive` 현재 PowerShell DSC 등록 프로토콜은 만료가 가까워지면 인증서를 자동으로 갱신할 수 없으며 1년 이후에 노드를 다시 등록해야 합니다. 다시 등록하기 전에 각 노드가 WMF 5 RTM을 실행하고 있는지 확인합니다. 

    재등록은 인증서 만료 시간으로부터 90일 이내로 수행되거나 인증서 만료 시간 이후에 언제든지 새 인증서가 생성되고 사용됩니다. 이 문제에 대한 해결 방법은 Windows Server 2019 이상에 포함되어 있습니다.

- **DSC LCM 값을 변경합니다.** 예를 들어 노드를 처음 등록하는 동안 설정된 [PowerShell DSC LCM 값을](/powershell/scripting/dsc/managing-nodes/metaConfig4) 변경해야 할 수 `ConfigurationMode`있습니다. 현재는 재등록을 통해서만 이러한 DSC 에이전트 값을 변경할 수 있습니다. 한 가지 예외는 노드에 할당된 노드 구성 값입니다. Azure 자동화 DSC에서 직접 변경할 수 있습니다.

이 문서에 설명된 온보딩 방법을 사용하여 노드를 처음 등록한 것처럼 노드를 다시 등록할 수 있습니다. 노드를 다시 등록하기 전에 Azure 자동화 상태 구성에서 노드를 등록 취소할 필요가 없습니다.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure 가상 머신 온보드 문제 해결

Azure Automation 상태 구성을 사용하면 구성 관리를 위해 간편하게 Azure Windows VM을 온보드할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation 상태 구성에 등록하는 데 사용됩니다. Azure VM 원하는 상태 구성 확장이 비동기적으로 실행되므로 진행 상황을 추적하고 실행 문제를 해결하는 것이 중요할 수 있습니다.

> [!NOTE]
> Azure VM 원하는 상태 구성 확장을 사용하는 Azure Windows VM을 Azure 자동화 상태 구성에 온보딩하는 모든 메서드는 Azure 자동화가 등록된 것으로 표시하는 데 최대 1시간이 걸릴 수 있습니다. 이 지연은 Azure VM 원하는 상태 구성 확장에 의해 VM에 WMF 5를 설치하기 때문이며, 이는 VM을 Azure 자동화 상태 구성에 온보온하는 데 필요합니다.

Azure VM 원하는 상태 구성 확장의 상태를 해결하거나 보려면 다음을 수행하십시오.

1. Azure 포털에서 온보던 VM으로 이동합니다.
2. **설정** **에서 확장을 클릭합니다.** 
3. 이제 운영 체제에 따라 **DSC** 또는 **DSCForLinux를**선택합니다. 
4. 자세한 내용을 보려면 **자세한 상태 보기**를 클릭합니다.

문제 해결에 대한 자세한 내용은 [Azure 자동화 원하는 상태 구성(DSC)의 문제 해결을](./troubleshoot/desired-state-configuration.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- 시작하려면 Azure [자동화 상태 구성을 시작하십시오.](automation-dsc-getting-started.md)
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure 자동화 상태 구성의 구성 컴파일을](automation-dsc-compile.md)참조하십시오.
- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하십시오.
- 가격 정보는 [Azure 자동화 상태 구성 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.
- 연속 배포 파이프라인에서 Azure 자동화 상태 구성을 사용하는 예는 [사용 예제: Azure 자동화 상태 구성 및 초콜릿을 사용하여 가상 시스템에 대한 지속적인 배포를](automation-dsc-cd-chocolatey.md)참조하십시오.
