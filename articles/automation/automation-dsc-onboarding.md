---
title: Azure Automation 상태 구성을 통해 관리할 컴퓨터 사용
description: Azure Automation 상태 구성을 사용 하 여 관리할 컴퓨터를 설정 하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 52cd72d1144fa2acad993e927d49545d645d596f
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993747"
---
# <a name="enable-machines-for-management-by-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 통해 관리할 컴퓨터 사용

이 항목에서는 Azure Automation 상태 구성을 사용 하 여 관리할 컴퓨터를 설정 하는 방법에 대해 설명 합니다. 이 서비스에 대 한 자세한 내용은 [Azure Automation 상태 구성 개요](automation-dsc-overview.md)를 참조 하세요.

## <a name="enable-azure-vms"></a>Azure VM을 사용하도록 설정

Azure Automation 상태 구성을 사용 하면 Azure Portal, Azure Resource Manager 템플릿 또는 PowerShell을 사용 하 여 구성 관리를 위해 Azure Vm을 쉽게 사용 하도록 설정할 수 있습니다. 내부적으로 VM에 원격으로 로그인 해야 하는 관리자가 없으면 Azure VM 필요한 상태 구성 확장에서 VM을 Azure Automation 상태 구성으로 등록 합니다. Azure 확장은 비동기적으로 실행 되므로 진행 상황을 추적 하거나 문제를 해결 하는 단계는 [상태 구성에 대 한 VM 설정 문제 해결](#troubleshoot-vm-setup-for-state-configuration)에 제공 됩니다.

> [!NOTE]
>Linux 노드에 DSC를 배포 하면 **/tmp** 폴더가 사용 됩니다. 와 `nxautomation` 같은 모듈은 적절 한 위치에 설치 하기 전에 확인을 위해 임시로 다운로드 됩니다. 모듈이 제대로 설치 되도록 하려면 Linux 용 Log Analytics 에이전트에 **/tmp** 폴더에 대 한 읽기/쓰기 권한이 있어야 합니다.<br><br>
>Linux 용 Log Analytics 에이전트는 `omsagent` 사용자로 실행 됩니다. `omsagent` 사용자에 게 >쓰기 권한을 부여 하려면 명령을 `setfacl -m u:omsagent:rwx /tmp`실행 합니다.

### <a name="enable-a-vm-using-azure-portal"></a>Azure Portal를 사용 하 여 VM 사용

[Azure Portal](https://portal.azure.com/)를 통해 Azure VM에서 구성 상태를 설정 하려면 다음을 수행 합니다.

1. Vm을 사용 하도록 설정할 Azure Automation 계정으로 이동 합니다. 

2. 상태 구성 페이지에서 **노드** 탭을 선택 하 고 **추가**를 클릭 합니다.

3. 사용 하도록 설정할 VM을 선택 합니다.

4. 컴퓨터에 PowerShell 필요한 상태 확장이 설치 되어 있지 않고 전원 상태가 실행 중인 경우 **연결**을 클릭 합니다.

5. **등록**에서 사용 사례에 필요한 [PowerShell DSC 로컬 Configuration Manager 값](/powershell/scripting/dsc/managing-nodes/metaConfig) 을 입력 합니다. 선택적으로 VM에 할당할 노드 구성을 입력할 수 있습니다.

![VM 사용](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 VM 사용

Azure Resource Manager 템플릿을 사용 하 여 상태 구성에 대 한 VM을 설치 하 고 사용 하도록 설정할 수 있습니다. 상태 구성에 기존 VM을 사용 하도록 설정 하는 예제 템플릿은 [필요한 상태에서 관리 되는 서버 구성 서비스](https://azure.microsoft.com/resources/templates/101-automation-configuration/) 를 참조 하세요. 가상 머신 확장 집합을 관리 하는 경우 [Azure Automation에서 관리 하는 가상 머신 확장 집합 구성](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)의 예제 템플릿을 참조 하세요.

### <a name="enable-machines-using-powershell"></a>PowerShell을 사용 하 여 컴퓨터 사용

PowerShell에서 [AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet을 사용 하 여 상태 구성에 대 한 vm을 사용 하도록 설정할 수 있습니다. 

> [!NOTE]
>이 `Register-AzAutomationDscNode` cmdlet은 windows 확장만 트리거하기 때문에 현재 windows를 실행 하는 컴퓨터에 대해서만 구현 됩니다.

### <a name="register-vms-across-azure-subscriptions"></a>Azure 구독에서 Vm 등록

다른 Azure 구독에서 Vm을 등록 하는 가장 좋은 방법은 Azure Resource Manager 배포 템플릿에서 DSC 확장을 사용 하는 것입니다. 예는 [Azure Resource Manager 템플릿을 사용 하 여 필요한 상태 구성 확장](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)에서 제공 됩니다.

템플릿에서 매개 변수로 사용할 등록 키와 등록 URL을 찾으려면 [등록을 사용 하 여 컴퓨터를 안전 하](#enable-machines-securely-using-registration)게 사용을 참조 하세요.

## <a name="enable-physicalvirtual-windows-machines"></a>물리적/가상 Windows 컴퓨터 사용

온-프레미스 또는 다른 클라우드 환경 (AWS EC2 instances 포함)에서 실행 되는 Windows server를 사용 하도록 설정 하 여 상태 구성을 Azure Automation 수 있습니다. 서버에는 [Azure에 대 한 아웃 바운드 액세스](automation-dsc-overview.md#network-planning)권한이 있어야 합니다.

1. 상태 구성을 사용할 수 있도록 컴퓨터에 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치 되어 있는지 확인 합니다. 또한 컴퓨터를 사용 하도록 설정 하는 데 사용 하는 컴퓨터에 WMF 5가 설치 되어 있어야 합니다.
1. [Dsc 메타 구성을 생성](#generate-dsc-metaconfigurations) 의 지시에 따라 필요한 dsc 메타 구성을를 포함 하는 폴더를 만듭니다. 
1. 다음 cmdlet을 사용 하 여 PowerShell DSC 메타 구성을를 컴퓨터에 원격으로 적용 하 여 사용 하도록 설정 합니다. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC 메타 구성을를 원격으로 적용할 수 없는 경우 **메타 구성을** 폴더를 사용 하도록 설정 된 컴퓨터에 복사 합니다. 그런 다음 컴퓨터에서 로컬로 [set-dsclocalconfigurationmanager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 를 호출 하는 코드를 추가 합니다.
1. Azure Portal 또는 cmdlet을 사용 하 여 컴퓨터가 Azure Automation 계정에 등록 된 상태 구성 노드로 나타나는지 확인 합니다.

## <a name="enable-physicalvirtual-linux-machines"></a>물리적/가상 Linux 컴퓨터 사용

상태 구성에 대해 온-프레미스 또는 다른 클라우드 환경에서 실행 되는 Linux 서버를 사용 하도록 설정할 수 있습니다. 서버에는 [Azure에 대 한 아웃 바운드 액세스](automation-dsc-overview.md#network-planning)권한이 있어야 합니다.

1. 상태 구성에 사용할 수 있도록 컴퓨터에 최신 버전의 [PowerShell 필요한 상태 구성이](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 설치 되어 있는지 확인 합니다.
2. [POWERSHELL DSC 로컬 Configuration Manager 기본값이](/powershell/scripting/dsc/managing-nodes/metaConfig4) 사용 사례와 일치 하 고 컴퓨터를 사용 하도록 설정 하 여 상태 구성에 대 한 작업을 수행 하 고 보고 하려면 다음을 수행 합니다.

   - 각 Linux 컴퓨터에서을 사용 하도록 설정 `Register.py` 하려면를 사용 하 여 PowerShell DSC 로컬 Configuration Manager 기본값으로 컴퓨터를 사용 하도록 설정 합니다.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Automation 계정에 대 한 등록 키 및 등록 URL을 찾으려면 등록을 [사용 하 여 컴퓨터를 안전 하 게 사용](#enable-machines-securely-using-registration)을 참조 하세요.

3. PowerShell DSC 로컬 Configuration Manager (LCM) 기본값이 사용 사례와 일치 하지 않거나 Azure Automation 상태 구성에만 보고 하는 컴퓨터를 사용 하도록 설정 하려는 경우 4-7 단계를 수행 합니다. 그렇지 않으면 7 단계로 직접 이동 합니다.

4. [Dsc 메타 구성을 생성](#generate-dsc-metaconfigurations) 섹션의 지침에 따라 필요한 dsc 메타 구성을이 포함 된 폴더를 만듭니다.

5. 상태를 구성 하기 위해 컴퓨터를 사용 하도록 설정 하는 데 사용 되는 컴퓨터에 [WMF 5](https://aka.ms/wmf5latest) 의 최신 버전이 설치 되어 있는지 확인 합니다.

6. 사용할 수 있도록 컴퓨터에 원격으로 PowerShell DSC 메타 구성을를 적용 하는 코드를 다음과 같이 추가 합니다.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. PowerShell DSC 메타 구성을를 원격으로 적용할 수 없는 경우 4 단계에서 설명 하는 폴더에서 원격 컴퓨터에 해당 하는 메타 구성을를 Linux 컴퓨터에 복사 합니다.

8. 각 Linux 컴퓨터에서 `Set-DscLocalConfigurationManager.py` 로컬로 호출 하는 코드를 추가 하 여 상태 구성을 사용 하도록 설정 합니다.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Azure Portal 또는 cmdlet을 사용 하 여 이제 사용할 컴퓨터가 Azure Automation 계정에 등록 된 DSC 노드로 표시 되는지 확인 합니다.

## <a name="generate-dsc-metaconfigurations"></a>DSC 메타 구성을 생성

상태 구성에 대 한 모든 컴퓨터를 사용 하도록 설정 하려면 [DSC 메타](/powershell/scripting/dsc/managing-nodes/metaConfig)구성을 생성할 수 있습니다. 이 구성을 통해 DSC 에이전트가 Azure Automation 상태 구성으로 끌어오거나 보고 하도록 지시할 수 있습니다. PowerShell DSC 구성 또는 Azure Automation PowerShell cmdlet을 사용 하 여 Azure Automation 상태 구성에 대 한 DSC 메타 구성을 생성할 수 있습니다.

> [!NOTE]
> DSC 메타 구성을는 관리를 위해 Automation 계정에서 컴퓨터를 사용 하도록 설정 하는 데 필요한 암호를 포함 합니다. 사용한 후에 만들거나 삭제한 DSC 메타 구성을 제대로 보호해야 합니다.

메타 구성을에 대 한 프록시 지원은 Windows PowerShell DSC 엔진인 LCM에 의해 제어 됩니다. LCM은 모든 대상 노드에서 실행 되며, DSC 메타 구성 스크립트에 포함 된 구성 리소스를 호출 하는 일을 담당 합니다. `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb`및 `ReportServerWeb` 블록에서 필요에 따라 프록시 URL의 정의와 프록시 자격 증명을 포함 하 여 메타 구성에 프록시 지원을 포함할 수 있습니다. [로컬 Configuration Manager 구성을](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)참조 하세요.

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC 구성을 사용 하 여 DSC 메타 구성을 생성

1. 로컬 환경의 컴퓨터에서 관리자 권한으로 VSCode (또는 선호 하는 편집기)를 엽니다. 이 컴퓨터에는 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.
1. 다음 스크립트를 로컬로 복사합니다. 이 스크립트는 메타 구성을 만들기 위한 PowerShell DSC 구성 및 메타 구성 생성을 시작하는 명령을 포함합니다.

    > [!NOTE]
    > 상태 구성 노드 구성 이름은 Azure Portal에서 대/소문자를 구분 합니다. 대/소문자가 일치 하지 않으면 노드가 **노드** 탭에 표시 되지 않습니다.

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

1. Automation 계정에 대 한 등록 키와 URL을 입력 하 고 사용할 컴퓨터의 이름을 입력 합니다. 모든 다른 매개 변수는 선택 사항입니다. Automation 계정에 대 한 등록 키 및 등록 URL을 찾으려면 등록을 [사용 하 여 컴퓨터를 안전 하 게 사용](#enable-machines-securely-using-registration)을 참조 하세요.

1. 컴퓨터에서 DSC 상태 정보를 Azure Automation 상태 구성으로 보고 하 고 구성 또는 PowerShell 모듈을 가져오지 않으려면 `ReportOnly` 매개 변수를 true로 설정 합니다.

1. 가 `ReportOnly` 설정 되지 않은 경우 컴퓨터는 Azure Automation 상태 구성 및 끌어오기 구성 또는 PowerShell 모듈에 DSC 상태 정보를 보고 합니다. `ConfigurationRepositoryWeb`, 및 `ReportServerWeb` 블록에서 매개 변수 `ResourceRepositoryWeb`를 적절 하 게 설정 합니다.

1. 스크립트를 실행합니다. 이제 사용 하도록 설정할 컴퓨터의 PowerShell DSC 메타 구성을를 포함 하는 **DscMetaConfigs**라는 작업 디렉터리 폴더가 있습니다 (관리자).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Azure Automation cmdlet을 사용 하 여 DSC 메타 구성을 생성

PowerShell DSC LCM 기본값이 사용 사례와 일치 하 고 컴퓨터에서 Azure Automation 상태 구성으로의 끌어오기 및 보고를 사용 하도록 설정 하려는 경우 Azure Automation cmdlet을 사용 하 여 필요한 DSC 메타 구성을을 보다 간단 하 게 생성할 수 있습니다.

1. 로컬 환경의 컴퓨터에서 관리자 권한으로 PowerShell 콘솔 또는 VSCode를 엽니다.
2. [AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)를 사용 하 여 Azure Resource Manager에 연결 합니다.
3. 노드를 설정 하는 Automation 계정에서 사용 하도록 설정 하려는 컴퓨터에 대 한 PowerShell DSC 메타 구성을를 다운로드 합니다.

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

1. 이제 컴퓨터에서 사용 하도록 설정할 PowerShell DSC 메타 구성을를 포함 하는 **DscMetaConfigs** 폴더 (관리자 권한)가 있어야 합니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>등록을 사용 하 여 안전 하 게 컴퓨터 사용

WMF 5 DSC 등록 프로토콜을 통해 Azure Automation 계정에 대해 안전 하 게 컴퓨터를 사용 하도록 설정할 수 있습니다. 이 프로토콜을 사용 하면 DSC 노드에서 Azure Automation 상태 구성을 포함 하 여 PowerShell DSC 끌어오기 또는 보고서 서버에 인증할 수 있습니다. 노드는 등록 URL에서 서버에 등록 하 고 등록 키를 사용 하 여 인증 합니다. 등록 하는 동안 DSC 노드와 DSC 끌어오기/보고서 서버는 노드에 대 한 고유 인증서를 협상 하 여 서버에서 등록 후의 인증에 사용 합니다. 이 프로세스는 노드가 손상 되어 악의적으로 동작 하는 경우와 같이 노드가 서로를 가장할 수 없도록 합니다. 등록 후에는 등록 키가 다시 인증에 사용 되지 않고 노드에서 삭제 됩니다.

Azure Portal의 **계정 설정** 아래에 있는 **키**에서 상태 구성 등록 프로토콜에 필요한 정보를 얻을 수 있습니다. 

![Azure Automation 키 및 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 등록 URL은 키 페이지의 URL 필드입니다.
- 등록 키는 키 페이지의 **기본 액세스 키** 필드 또는 **보조 액세스 키** 필드의 값입니다. 둘 중 하나를 사용할 수 있습니다.

보안을 강화 하기 위해 키 페이지에서 언제 든 지 Automation 계정의 기본 및 보조 액세스 키를 다시 생성할 수 있습니다. 키를 다시 생성 하면 이후 노드 등록에서 이전 키를 사용할 수 없습니다.

## <a name="re-register-a-node"></a>노드 다시 등록

Azure Automation 상태 구성에서 DSC 노드로 컴퓨터를 등록 한 후 나중에 해당 노드를 다시 등록 해야 하는 이유는 여러 가지가 있습니다.

- **인증서 갱신.** Windows Server 2019 이전의 Windows Server 버전에서는 각 노드가 1 년 후에 만료 되는 인증에 대해 고유한 인증서를 자동으로 협상 합니다. 갱신 하지 않고 인증서가 만료 되는 경우 노드가 Azure Automation와 통신할 수 없으며로 표시 `Unresponsive`됩니다. 현재 PowerShell DSC 등록 프로토콜은 만료가 임박 하면 인증서를 자동으로 갱신할 수 없으므로 1 년 후에 노드를 다시 등록 해야 합니다. 다시 등록 하기 전에 각 노드에서 WMF 5 RTM을 실행 하 고 있는지 확인 합니다. 

    다시 등록 하면 인증서 만료 시간에서 또는 인증서 만료 시간 이후 언제 든 지 90 일 이내에 수행 되며 새 인증서가 생성 되어 사용 됩니다. 이 문제에 대 한 해결 방법은 Windows Server 2019 이상에 포함 되어 있습니다.

- **DSC LCM 값에 대 한 변경 내용입니다.** 노드의 초기 등록 중에 설정 된 [POWERSHELL DSC LCM 값](/powershell/scripting/dsc/managing-nodes/metaConfig4) 을 변경 해야 할 수도 있습니다 (예: `ConfigurationMode`). 현재는 다시 등록을 통해서만 이러한 DSC 에이전트 값을 변경할 수 있습니다. 한 가지 예외는 노드에 할당 된 노드 구성 값입니다. DSC Azure Automation에서 직접 변경할 수 있습니다.

이 문서에 설명 된 방법 중 하나를 사용 하 여 처음에 노드를 등록 한 것 처럼 노드를 다시 등록할 수 있습니다. 다시 등록 하기 전에 Azure Automation 상태 구성에서 노드를 등록 취소할 필요가 없습니다.

## <a name="troubleshoot-vm-setup-for-state-configuration"></a>상태 구성에 대 한 VM 설정 문제 해결

상태 구성을 사용 하면 구성 관리를 위해 Azure Windows Vm을 쉽게 사용 하도록 설정할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation 상태 구성에 등록하는 데 사용됩니다. Azure VM 필요한 상태 구성 확장은 비동기적으로 실행 되므로 진행 상황을 추적 하 고 실행 문제를 해결 하는 것이 중요할 수 있습니다.

> [!NOTE]
> Azure VM 필요한 상태 구성 확장을 사용 하는 상태 구성에 대해 Azure Windows Vm을 사용 하도록 설정 하는 방법은 Vm이 등록 된 것으로 표시 하는 데 최대 1 Azure Automation 시간이 걸릴 수 있습니다. 이러한 지연은 상태 구성에 대해 Vm을 사용 하도록 설정 하는 데 필요한 Azure VM 필요한 상태 구성 확장에 의해 VM에 WMF 5를 설치 하는 경우에 발생 합니다.

문제를 해결 하거나 Azure VM 필요한 상태 구성 확장의 상태를 확인 하려면:

1. Azure Portal에서 사용 하도록 설정 된 VM으로 이동 합니다.
2. **설정**아래에서 **확장** 을 클릭 합니다. 
3. 이제 운영 체제에 따라 **DSC** 또는 **dscforlinux**를 선택 합니다. 
4. 자세한 내용을 보려면 **자세한 상태 보기**를 클릭합니다.

문제 해결에 대 한 자세한 내용은 [Azure Automation 상태 구성 문제 해결](./troubleshoot/desired-state-configuration.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 시작 하려면 [Azure Automation 상태 구성 시작](automation-dsc-getting-started.md)을 참조 하세요.
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조 하세요.
- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조 하세요.
- 연속 배포 파이프라인에서 Azure Automation 상태 구성을 사용 하는 예제는 [사용 예제: Azure Automation 상태 구성 및 Chocolatey를 사용 하 여 가상 머신에 연속 배포](automation-dsc-cd-chocolatey.md)를 참조 하세요.
