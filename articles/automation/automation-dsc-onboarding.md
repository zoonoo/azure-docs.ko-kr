---
title: Azure 자동화 상태 구성으로 관리를 위한 온보드 머신
description: 이 문서에서는 Azure 자동화 상태 구성을 사용하여 관리를 위한 컴퓨터를 설정하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406193"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Azure 자동화 상태 구성으로 관리를 위한 온보드 머신

Azure 자동화 상태 구성은 클라우드 또는 온-프레미스 데이터 센터에서 원하는 DSC(상태 구성) 노드에 대한 구성 관리 서비스입니다. **구성 관리**에서 **상태 구성 (DSC)을** 선택 하 여 Azure 포털에서 서비스에 액세스할 수 있습니다. 

Azure 자동화 상태 구성을 사용하면 중앙의 안전한 위치에서 수천 대의 컴퓨터에서 빠르고 쉽게 확장성을 달성할 수 있습니다. 컴퓨터를 쉽게 온보기하고 선언적 구성을 할당하고 각 컴퓨터가 지정한 상태와 일치한다는 보고서를 볼 수 있습니다.

Azure 자동화 상태 구성 서비스는 Azure 자동화 실행책이 PowerShell 스크립팅에 어떤 것인지 DSC에 대한 것입니다. 즉, Azure Automation에서 PowerShell 스크립트 관리를 지원하는 동일한 방법으로 DSC 구성 관리를 지원합니다. Azure Automation 상태 구성을 사용하는 이점에 대해 자세히 알려면 [Azure Automation 상태 구성 개요](automation-dsc-overview.md)를 참조하세요.

Azure 자동화 상태 구성을 사용하여 다양한 컴퓨터를 관리할 수 있습니다.

- Azure VM(가상 머신)
- Azure 가상 머신(기본)
- 온-프레미스 또는 Azure 이외의 클라우드에서 물리적/가상 Windows 컴퓨터(Amazon 웹 서비스[AWS] 탄력적 컴퓨팅 클라우드 [EC2] 인스턴스 포함)
- 온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

클라우드에서 컴퓨터 구성을 관리할 준비가 되지 않은 경우 Azure 자동화 상태 구성을 보고서 전용 끝점으로 사용할 수 있습니다. 이 기능을 사용하면 DSC를 통해 구성을 설정하거나 푸시하고 Azure Automation에서 보고 세부 정보를 볼 수 있습니다.

> [!NOTE]
> 설치된 Azure VM 원하는 상태 구성 확장이 버전 2.70 이상인 경우 Azure 자동화 상태 구성을 통해 Azure VM을 관리하는 것은 추가 비용 없이 포함됩니다. 자세한 내용은 [자동화 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.

이 문서의 다음 섹션에서는 이전에 나열된 컴퓨터를 Azure 자동화 상태 구성에 온보온하는 방법을 설명합니다.

## <a name="onboard-azure-vms"></a>Azure VM 등록

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자에 대한 Az 모듈 설치 지침은 [Azure PowerShell 설치 모듈 을](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 업데이트 모듈을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

Azure 확장은 비동기적으로 실행되므로 이 문서의 [문제 해결 Azure 가상 시스템 온보딩](#troubleshoot-azure-virtual-machine-onboarding) 섹션에서 진행 상황을 추적하거나 문제를 해결하는 단계를 제공합니다.

> [!NOTE]
> Linux 노드에 DSC를 배포하려면 */tmp* 폴더를 사용합니다. 같은 `nxautomation` 모듈은 설치하기 전에 확인을 위해 일시적으로 다운로드됩니다. 모듈이 올바르게 설치되도록 하려면 Linux용 로그 분석 에이전트에서 */tmp* 폴더에 대한 읽기/쓰기 권한이 필요합니다.<br><br>
> Linux용 로그 분석 에이전트는 *옴에이전트* 사용자로 실행됩니다. *omsagent* 사용자에게 쓰기 권한을 부여하려면 명령을 `setfacl -m u:omsagent:rwx /tmp`실행합니다.

### <a name="onboard-vms-by-using-the-azure-portal"></a>Azure 포털을 사용하여 VM 온보보드

Azure 포털을 통해 Azure VM을 Azure 자동화 상태 구성에 온보덕인하려면 [다음을 수행합니다.](https://portal.azure.com/)

1. VM을 온보승하려는 Azure 자동화 계정으로 이동합니다. 

1. 상태 **구성** 페이지에서 노드 탭을 선택한 다음 **에 추가를** **선택합니다.**

1. 온보던할 VM을 선택합니다.

1. 컴퓨터에 PowerShell 원하는 상태 확장이 설치되어 있지 않고 전원 상태가 실행 중인 경우 **연결을**선택합니다.

1. **등록에서**사용 사례에 필요한 [PowerShell DSC 로컬 구성 관리자(LCM) 값을](/powershell/scripting/dsc/managing-nodes/metaConfig) 입력합니다. 선택적으로 노드 구성을 입력하여 VM에 할당할 수 있습니다.

![VM 등록 창](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿을 사용하여 VM 온보로

Azure 리소스 관리자 템플릿을 사용하여 VM을 Azure 자동화 상태 구성에 배포하고 온보싱할 수 있습니다. 기존 VM을 Azure 자동화 상태 구성에 온보팅하는 예제 템플릿은 [원하는 상태 구성 서비스에서 관리하는 서버를](https://azure.microsoft.com/resources/templates/101-automation-configuration/)참조하십시오. 가상 시스템 규모 집합을 관리하는 경우 [Azure Automation에서 관리하는 가상 시스템 규모 집합 구성의](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)예제 템플릿을 참조하십시오.

### <a name="onboard-vms-by-using-powershell"></a>PowerShell을 사용하여 온보드 VM

PowerShell의 [레지스터-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet을 사용하여 VM을 Azure 자동화 상태 구성에 온보딩할 수 있습니다. 

> [!NOTE]
>`Register-AzAutomationDscNode` cmdlet은 Windows 확장을 트리거하기 때문에 현재 Windows를 실행하는 컴퓨터에 대해서만 구현됩니다.

### <a name="register-vms-across-azure-subscriptions"></a>Azure 구독에서 VM 등록

다른 Azure 구독에서 VM을 등록하는 가장 좋은 방법은 Azure 리소스 관리자 배포 템플릿에서 DSC 확장을 사용하는 것입니다. 예제는 Azure [리소스 관리자 템플릿을 사용 하](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)고 원하는 상태 구성 확장에 제공 됩니다.

템플릿에서 매개 변수로 사용할 등록 키 및 등록 URL을 찾으려면 이 문서의 등록 섹션을 [사용하여 온보드를 안전하게](#onboard-securely-by-using-registration) 참조하십시오.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>온-프레미스 또는 Azure 이외의 클라우드에서 온보보드 물리적/가상 Windows 컴퓨터

온-프레미스 또는 AWS EC2 인스턴스를 비롯한 다른 클라우드 환경에서 실행 중인 Windows 서버를 Azure 자동화 상태 구성에 온보덕일 수 있습니다. 서버는 Azure 에 [대한 아웃바운드 액세스 권한이](automation-dsc-overview.md#network-planning)있어야 합니다.

1. 최신 버전의 Windows [관리 프레임워크 5가](https://aka.ms/wmf5latest) 컴퓨터에 설치되어 Azure 자동화 상태 구성에 온보로 전송되는지 확인합니다. 또한 온보딩 작업에 사용 중인 컴퓨터에 Windows 관리 프레임워크 5를 설치해야 합니다.
1. 필요한 DSC 메타구성이 포함된 폴더를 만들려면 [DSC 메타구성 생성](#generate-dsc-metaconfigurations) 섹션의 지침을 따릅니다. 
1. PowerShell DSC 메타구성을 온보드하려는 컴퓨터에 원격으로 적용하려면 다음 cmdlet을 사용하십시오. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC 메타구성을 원격으로 적용할 수 없는 경우 **메타구성 폴더를** 온보딩중인 컴퓨터에 복사합니다. 그런 다음 코드를 추가하여 컴퓨터에서 로컬로 [Set-DscLocalConfigurationManager를](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 호출합니다.

1. Azure 포털에서 또는 cmdlet을 사용하여 온보딩할 컴퓨터가 Azure Automation 계정에 등록된 상태 구성 노드로 나타나는지 확인합니다.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>온-프레미스 또는 Azure 이외의 클라우드에서 온보보드 물리적/가상 Linux 시스템

온-프레미스 또는 다른 클라우드 환경에서 실행되는 Linux 서버를 Azure 자동화 상태 구성에 온보덕일 수 있습니다. 서버는 Azure 에 [대한 아웃바운드 액세스 권한이](automation-dsc-overview.md#network-planning)있어야 합니다.

1. [Linux용 PowerShell 원하는 상태 구성의](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 최신 버전이 Azure 자동화 상태 구성에 온보온으로 컴퓨터에 설치되어 있는지 확인합니다.
1. [PowerShell DSC LCM 기본값이](/powershell/scripting/dsc/managing-nodes/metaConfig4) 사용 사례와 일치하고 컴퓨터를 온보딩하여 둘 다 Azure 자동화 상태 구성으로 가져오고 보고하도록 하려면 다음을 수행합니다.

   a. Azure 자동화 상태 구성에 온보딩하는 `Register.py` 각 Linux 컴퓨터에서 PowerShell DSC 로컬 구성 관리자 기본값을 사용하여 온보딩하는 데 사용합니다.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. 자동화 계정의 등록 키 및 등록 URL을 찾으려면 이 문서의 등록 섹션을 [사용하여 온보드를 안전하게](#onboard-securely-by-using-registration) 참조하십시오.  
   다. 4단계로 건너뜁니다.
   
1. PowerShell DSC LCM 기본값이 사용 사례와 일치하지 않거나 Azure 자동화 상태 구성에만 보고하는 컴퓨터를 온보딩하려는 경우 a-d 단계를 따릅니다. 그렇지 않으면 d단계로 직접 진행합니다.

    a. 필요한 DSC 메타구성이 포함된 폴더를 생성하려면 [DSC 메타구성 생성](#generate-dsc-metaconfigurations) 섹션의 지침을 따릅니다.

    b. 최신 버전의 Windows [관리 프레임워크 5가](https://aka.ms/wmf5latest) 온보딩에 사용되는 컴퓨터에 설치되어 있는지 확인합니다.

    다. PowerShell DSC 메타구성을 온보딩하려는 컴퓨터에 원격으로 적용하려면 다음 코드를 추가하십시오.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. PowerShell DSC 메타구성을 원격으로 적용할 수 없는 경우 3a 단계에서 설명한 폴더에서 원격 컴퓨터에 해당하는 메타 구성을 Linux 컴퓨터로 복사합니다.

1. Azure 자동화 상태 구성에 온보온할 각 Linux 컴퓨터에서 로컬로 *Set-DscLocalConfigurationManager.py 호출할* 코드를 추가합니다.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Azure 포털 또는 cmdlet을 사용하여 온보딩할 컴퓨터가 이제 Azure 자동화 계정에 등록된 DSC 노드로 표시되도록 합니다.

## <a name="generate-dsc-metaconfigurations"></a>DSC 메타구성 생성

모든 컴퓨터를 Azure 자동화 상태 구성에 온보딩하려면 [DSC 메타구성을](/powershell/scripting/dsc/managing-nodes/metaConfig)생성할 수 있습니다. 이 구성은 DSC 에이전트가 Azure 자동화 상태 구성에서 가져오거나 보고하도록 지시합니다. PowerShell DSC 구성 또는 Azure 자동화 PowerShell cmdlet을 사용하여 Azure 자동화 상태 구성에 대한 DSC 메타구성을 생성할 수 있습니다.

> [!NOTE]
> DSC 메타구성에는 관리를 위해 자동화 계정에 컴퓨터를 온보딩하는 데 필요한 암호가 포함되어 있습니다. 생성한 DSC 메타구성을 적절히 보호하거나 사용 후 삭제해야 합니다.

메타 구성에 대한 프록시 지원은 Windows PowerShell DSC 엔진인 LCM에 의해 제어됩니다. LCM은 모든 대상 노드에서 실행되며 DSC 메타구성 스크립트에 포함된 구성 리소스를 호출합니다. 

에 필요에 따라 프록시 URL 및 프록시 자격 증명의 정의를 포함 하 여 `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`메타 `ReportServerWeb` 구성에 프록시 지원을 포함할 수 있습니다. [로컬 구성 관리자 구성](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)을 참조하십시오.

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>DSC 구성을 사용하여 DSC 메타구성 생성

1. 로컬 환경에서 컴퓨터에서 관리자로 시각적 스튜디오 코드(또는 즐겨 찾는 편집기)를 엽니다. 컴퓨터에 최신 버전의 [Windows 관리 프레임워크 5가](https://aka.ms/wmf5latest) 설치되어 있어야 합니다.
1. 다음 스크립트를 로컬로 복사합니다. 이 스크립트는 메타 구성을 만들기 위한 PowerShell DSC 구성 및 메타 구성 생성을 시작하는 명령을 포함합니다.

    > [!NOTE]
    > 상태 구성 노드 구성 이름은 Azure 포털에서 대/소문자를 구분합니다. 케이스가 일치하지 않으면 노드 탭 아래에 **노드가** 표시되지 않습니다.

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. 등록할 컴퓨터의 이름 뿐만 아니라 Automation 계정에 대한 등록 키 및 URL을 입력합니다. 모든 다른 매개 변수는 선택 사항입니다. 자동화 계정의 등록 키 및 등록 URL을 찾으려면 등록 섹션을 [사용하여 온보드를 안전하게](#onboard-securely-by-using-registration) 참조하십시오.

1. 컴퓨터가 DSC 상태 정보를 Azure 자동화 상태 구성에 보고하지만 구성 또는 PowerShell 모듈을 가져오지 않도록 하려면 매개 변수를 `ReportOnly` *true로*설정합니다.

1. 설정되지 않은 경우 `ReportOnly` 컴퓨터는 DSC 상태 정보를 Azure 자동화 상태 구성에 보고하고 구성 또는 PowerShell 모듈을 가져옵니다. 에 이에 `ConfigurationRepositoryWeb`따라 매개 `ResourceRepositoryWeb`변수를 설정합니다. `ReportServerWeb`

1. 스크립트를 실행합니다. 이제 컴퓨터가 온보딩할 수 있는 PowerShell DSC 메타구성이 포함된 *DscMetaConfigs라는*작업 디렉토리 폴더가 있어야 합니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Azure 자동화 cmdlet을 사용하여 DSC 메타구성 생성

PowerShell DSC LCM 기본값이 사용 사례와 일치하고 컴퓨터를 온보딩하여 Azure 자동화 상태 구성으로 가져오고 보고하려는 경우 Azure 자동화 cmdlet을 사용하여 필요한 DSC 메타구성을 더 간단하게 생성할 수 있습니다.

1. 로컬 환경에서 컴퓨터에서 관리자로 PowerShell 콘솔 또는 Visual Studio 코드를 엽니다.
1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)를 사용하여 Azure 리소스 관리자에 연결합니다.
1. 노드를 설정하는 자동화 계정에서 온보딩하려는 컴퓨터에 대한 PowerShell DSC 메타구성을 다운로드합니다.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 이제 온보딩하려는 컴퓨터(관리자)에 대한 PowerShell DSC 메타구성이 포함된 *DscMetaConfigs* 폴더가 있어야 합니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>등록을 사용하여 안전하게 온보드

컴퓨터는 Windows 관리 프레임워크 5 DSC 등록 프로토콜을 통해 Azure 자동화 계정에 안전하게 온보딩할 수 있습니다. 이 프로토콜을 사용하면 DSC 노드가 Azure 자동화 상태 구성을 포함하여 PowerShell DSC 끌어오기 또는 보고서 서버에 인증할 수 있습니다. 노드는 등록 URL에서 서버에 등록하고 등록 키를 사용하여 인증합니다. 

등록 하는 동안 DSC 노드및 DSC 끌어/보고서 서버는 등록 후 서버에 대한 인증에 사용할 노드에 대한 고유한 인증서를 협상합니다. 이 프로세스는 온보딩된 노드가 서로 가장하는 것을 방지합니다(예: 노드가 손상되어 악의적으로 행동하는 경우). 

등록 후 등록 키는 인증에 다시 사용 되지 않습니다 및 노드에서 삭제 됩니다.

상태 구성 등록 프로토콜에 필요한 정보를 얻으려면 Azure 포털로 이동하여 **계정 설정에서** **키를 선택합니다.** 

![키 창의 Azure 자동화 키 및 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **등록 URL**: **URL** 상자의 값입니다.
- **등록 키**: **기본 액세스 키** 상자 또는 보조 **액세스 키** 상자의 값입니다. 두 키 중 하나를 사용할 수 있습니다.

보안을 강화하려면 **키** 창에서 언제든지 자동화 계정의 기본 및 보조 액세스 키를 다시 생성할 수 있습니다. 키 재생성은 이후 노드 등록이 이전 키를 사용하지 못하도록 합니다.

## <a name="re-register-a-node"></a>노드 다시 등록

Azure 자동화 상태 구성에서 컴퓨터를 DSC 노드로 등록한 후 다음 두 가지 이유로 나중에 해당 노드를 다시 등록해야 할 수 있습니다.

- **인증서 갱신**: Windows Server 2019 이전 버전의 경우 각 노드는 1년 후에 만료되는 인증을 위해 고유한 인증서를 자동으로 협상합니다. 인증서가 갱신되지 않고 만료되면 노드가 Azure 자동화와 통신할 수 없으며 *응답하지 않음으로*표시됩니다. 

  현재 PowerShell DSC 등록 프로토콜은 만료가 가까워지면 인증서를 자동으로 갱신할 수 없으며 1년 이후에 노드를 다시 등록해야 합니다. 다시 등록하기 전에 각 노드가 Windows 관리 프레임워크 5 RTM을 실행하고 있는지 확인합니다. 

    재등록은 인증서 만료 시간으로부터 90일 이하를 수행하거나 인증서 만료 시간 이후 언제든지 새 인증서가 생성되고 사용됩니다. 이 문제에 대한 해결 방법은 Windows Server 2019 이상에 포함되어 있습니다.

- **DSC LCM 값 변경**: 노드의 초기 등록 시 설정된 [PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) 값을 `ConfigurationMode`변경해야 할 수 있습니다(예: ). 현재 재등록을 통해서만 이러한 DSC 에이전트 값을 변경할 수 있습니다. 한 가지 예외는 노드에 할당된 노드 구성 값입니다. Azure 자동화 DSC에서 이 값을 직접 변경할 수 있습니다.

이 문서에 설명된 온보딩 메서드를 사용하여 노드를 처음 등록한 것처럼 노드를 다시 등록할 수 있습니다. 노드를 다시 등록하기 전에 Azure 자동화 상태 구성에서 노드를 등록 취소할 필요가 없습니다.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Azure 가상 컴퓨터 온보딩 문제 해결

Azure Automation 상태 구성을 사용하면 구성 관리를 위해 간편하게 Azure Windows VM을 온보드할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation 상태 구성에 등록하는 데 사용됩니다. Azure VM 원하는 상태 구성 확장이 비동기적으로 실행되므로 진행 상황을 추적하고 실행 문제를 해결하는 것이 중요할 수 있습니다.

> [!NOTE]
> Azure VM 원하는 상태 구성 확장을 사용하는 Azure Windows VM을 Azure 자동화 상태 구성에 온보딩하는 모든 메서드는 Azure 자동화가 등록된 것으로 표시하는 데 최대 1시간이 걸릴 수 있습니다. 이 지연은 VM을 Azure 자동화 상태 구성에 온보온하는 데 필요한 Azure VM 원하는 상태 구성 확장에 의해 VM에 Windows 관리 프레임워크 5를 설치한 결과입니다.

Azure VM 원하는 상태 구성 확장의 상태를 해결하거나 보려면 다음을 수행하십시오.

1. Azure 포털에서 온보던 VM으로 이동합니다.
2. **설정**에서 **확장**을 선택합니다. 
3. 운영 체제에 따라 **DSC** 또는 **DSCForLinux를**선택합니다. 
4. 확장에 대한 자세한 내용은 **자세한 상태 보기를**선택합니다.

문제 해결에 대한 자세한 내용은 [Azure 자동화 원하는 상태 구성(DSC)의 문제 해결을](./troubleshoot/desired-state-configuration.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- 시작하려면 Azure [자동화 상태 구성을 시작하십시오.](automation-dsc-getting-started.md)
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure 자동화 상태 구성의 구성 컴파일을](automation-dsc-compile.md)참조하십시오.
- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)을 참조하십시오.
- 가격 정보는 [Azure 자동화 상태 구성 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.
- 연속 배포 파이프라인에서 Azure 자동화 상태 구성을 사용하는 예는 [Azure 자동화 상태 구성 및 초콜릿을 사용하여 가상 시스템에 대한 지속적인 배포를](automation-dsc-cd-chocolatey.md)참조하십시오.
