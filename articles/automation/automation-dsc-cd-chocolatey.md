---
title: Chocolatey를 사용 하 여 상태 구성 연속 배포 Azure Automation
description: Chocolatey 패키지 관리자를 사용 하 여 Azure Automation 상태 구성을 사용 하는 DevOps 연속 배포에 대해 설명 합니다. 전체 JSON 리소스 관리자 템플릿 및 PowerShell 소스에 대 한 예제가 포함 되어 있습니다.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 278c6ee05fdf78cbfa8653381b65233fbb513593
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996115"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Automation 상태 구성 및 Chocolatey를 사용 하 여 가상 머신에 연속 배포 제공

DevOps 세계에는 연속 통합 파이프라인에서 다양 한 요소를 지 원하는 여러 도구가 있습니다. Azure Automation [상태 구성은](automation-dsc-overview.md) devops 팀이 사용할 수 있는 옵션에 대 한 새로운 추가 시작입니다. 

Azure Automation는 runbook, 노드 및 공유 리소스 (예: 자격 증명, 일정 및 전역 변수)를 사용 하 여 다양 한 작업을 자동화할 수 있는 Microsoft Azure의 관리 되는 서비스입니다. Azure Automation 상태 구성은 PowerShell DSC (필요한 상태 구성) 도구를 포함 하도록이 자동화 기능을 확장 합니다. 여기서 알맞은 [개요](automation-dsc-overview.md)를 제공합니다.

이 문서에서는 Windows 컴퓨터에 대 한 CD (연속 배포)를 설정 하는 방법을 보여 줍니다. 역할에 필요한 만큼의 Windows 컴퓨터 (예: 웹 사이트)를 포함 하는 기술을 쉽게 확장 하 고 해당 위치에서 추가 역할로 이동할 수 있습니다.

![IaaS VM에 대한 연속 배포](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>높은 수준

여기서는 약간의 작업을 수행 하지만 다행히 두 가지 주요 프로세스로 나눌 수 있습니다.

- 즉 코드를 작성하여 테스트한 다음 시스템의 주 버전과 부 버전에 대해 설치 패키지를 만들고 게시하는 것입니다.
- 패키지에서 코드를 설치 하 고 실행 하는 Vm 만들기 및 관리  

이러한 핵심 프로세스가 모두 준비 되 면 새 버전이 만들어지고 배포 될 때 Vm에서 패키지를 자동으로 업데이트 하는 것이 쉽습니다.

## <a name="component-overview"></a>구성 요소 개요

[Apt](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 와 같은 패키지 관리자는 Linux 세계에서 잘 알려져 있지만, Windows의 경우는 많지 않습니다.
[Chocolatey](https://chocolatey.org/) 는 그러한 일 이며,이 항목에서 Scott Hanselman의 [블로그](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) 를 소개 하는 것이 좋습니다. 간단히 말해서 Chocolatey를 사용 하면 명령줄을 사용 하 여 중앙 리포지토리의 패키지를 Windows 운영 체제에 설치할 수 있습니다. 자체 리포지토리를 만들어 관리할 수 있고 Chocolatey가 사용자가 지정한 수 만큼의 리포지토리에서 패키지를 설치할 수 있습니다.

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview) 는 컴퓨터에 대해 원하는 구성을 선언할 수 있도록 하는 powershell 도구입니다. 예를 들어 Chocolatey를 설치 하 고, IIS를 설치 하 고, 포트 80를 열고, 웹 사이트의 버전 1.0.0을 설치 하려는 경우 LCM (DSC Local Configuration Manager)은 해당 구성을 구현 합니다. DSC 끌어오기 서버는 컴퓨터에 대 한 구성 리포지토리를 보유 합니다. 각 컴퓨터의 LCM이 주기적인 검사를 통해 자신의 구성이 저장된 구성과 일치하는지 확인합니다. 상태를 보고하거나, 컴퓨터를 저장된 구성과 일치하는 상태로 되돌리는 작업을 수행할 수 있습니다. 풀 서버에서 저장된 구성을 편집하여 컴퓨터나 컴퓨터 집합이 변경된 구성과 일치하게 만들 수 있습니다.

DSC 리소스는 네트워킹, Active Directory 또는 SQL Server를 관리 하는 등의 특정 기능을 포함 하는 코드의 모듈입니다. Chocolatey DSC 리소스는 NuGet 서버 액세스(서로 간), 패키지 다운로드, 패키지 설치 방법 등을 이해하고 있습니다. 여러 다른 DSC 리소스는 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)에 있습니다. 이러한 모듈은 사용자의 구성에서 사용할 수 있도록 Azure Automation 상태 구성 끌어오기 서버에 설치 합니다.

리소스 관리자 템플릿은 네트워크, 서브넷, 네트워크 보안, 라우팅, 부하 분산 장치, Nic, Vm 등의 인프라를 생성 하는 선언적 방법을 제공 합니다. 다음은 선언적 (리소스 관리자 배포 모델)을 Azure 서비스 관리 (ASM 또는 클래식) 배포 모델 (명령적)과 비교 하는 [문서](../azure-resource-manager/management/deployment-models.md) 입니다. 이 문서에는 계산, 저장소 및 네트워크와 같은 핵심 리소스 공급자에 대 한 설명이 포함 되어 있습니다.

리소스 관리자 템플릿의 핵심 기능은 프로 비전 되는 vm에 VM 확장을 설치 하는 기능입니다. VM 확장은 사용자 지정 스크립트를 실행 하 고 바이러스 백신 소프트웨어를 설치 하 고 DSC 구성 스크립트를 실행 하는 등의 특정 기능을 제공 합니다. VM 확장에는 여러 다른 형식이 있습니다.

## <a name="quick-trip-around-the-diagram"></a>간략히 다이어그램 둘러보기

위쪽에서 시작 하 여 코드를 작성 하 고 빌드하고 테스트 한 다음 설치 패키지를 만듭니다. Chocolatey는 MSI, MSU, ZIP 등과 같은 다양한 형태의 설치 패키지를 처리할 수 있습니다. Chocolatey의 기본 기능이 작동 하지 않는 경우 PowerShell을 완전히 활용 하 여 실제 설치를 수행할 수 있습니다. 패키지 리포지토리를 통해 연결할 수 있는 위치에 패키지를 배치 합니다. 이 사용 예는 Auzre Blob Storage 계정의 공용 폴더를 사용하지만 어디에나 있을 수 있습니다. Chocolatey는 패키지 메타데이터의 관리를 위해 NuGet 서버 및 기타 제품과 기본적으로 연동됩니다. [이 문서](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 에서는 이러한 옵션에 대해 설명합니다. 사용 예에서는 NuGet을 사용 합니다. Nuspec은 패키지에 관한 메타데이터입니다. Nuspec 정보는 NuPkg로 컴파일되고 NuGet 서버에 저장 됩니다. 구성에서 이름으로 패키지를 요청 하 고 NuGet 서버를 참조 하는 경우 VM의 Chocolatey DSC 리소스는 패키지를 가져와 패키지를 설치 합니다. 특정 버전의 패키지를 요청할 수도 있습니다.

그림의 왼쪽 아래에 Azure Resource Manager 템플릿이 있습니다. 이 사용 예제에서 VM 확장은 VM을 Azure Automation 상태 구성 끌어오기 서버를 노드로 등록 합니다. 구성은 일반 텍스트로 한 번, MOF 파일로 컴파일된 후에 끌어오기 서버에 두 번 저장 됩니다. Azure Portal에서 MOF는 간단한 구성이 아니라 노드 구성을 나타냅니다. 노드가 해당 구성을 알 수 있도록 노드에 연결 된 아티팩트입니다. 아래의 세부 정보는 노드 구성을 노드에 할당하는 방법을 보여줍니다.

Nuspec을 만들고, 컴파일하고, NuGet 서버에 저장 하는 것은 작은 일입니다. 이미 VM을 관리하고 있습니다. 

연속 배포에 대 한 다음 단계를 수행 하려면 풀 서버를 한 번 설정 하 고, 노드를 한 번에 등록 하 고, 서버에서 초기 구성을 만들고 저장 해야 합니다. 패키지가 업그레이드 되 고 리포지토리에 배포 되 면 필요에 따라 끌어오기 서버에서 구성 및 노드 구성을 새로 고쳐야 합니다.

리소스 관리자 템플릿으로 시작 하지 않는 경우에는 괜찮습니다. Vm을 끌어오기 서버에 등록 하는 데 도움이 되는 PowerShell 명령이 있습니다. 자세한 내용은 [Azure Automation 상태 구성을 통해 관리를 위한 컴퓨터 온 보 딩](automation-dsc-onboarding.md)을 참조 하세요.

## <a name="about-the-usage-example"></a>사용 예제 정보

이 문서의 사용 예제는 Azure 갤러리에서 일반 Windows Server 2012 R2 이미지의 VM으로 시작 합니다. 아무 저장된 이미지에서나 시작하고 DSC 구성을 통해 조정을 진행할 수 있습니다.
그러나 이미지에 반영되는 구성을 변경하는 작업은 DSC를 사용하여 구성을 동적으로 업데이트하는 작업보다 훨씬 어렵습니다.

이 방법을 VM에 사용하기 위해 Resource Manager 템플릿이나 VM 확장을 사용할 필요는 없습니다. 또한 VM이 CD 관리를 위해 Azure에 있을 필요도 없습니다. 오직 Chocolatey을 설치하고 풀 서버 위치를 알 수 있게 VM에 LCM을 구성하기만 하면 됩니다.

프로덕션에 있는 VM에서 패키지를 업데이트 하는 경우 업데이트가 설치 되는 동안 해당 VM을 순환에서 제외 해야 합니다. 이를 수행하는 방법은 크게 다릅니다. 예를 들어, Azure 부하 분산 장치 뒤에 있는 VM에서는 사용자 지정 프로브를 추가할 수 있습니다. VM을 업데이트하는 동안 프로브 엔드포인트가 400을 반환하게 합니다. 이 변경을 위해 필요한 조정은 구성 안에 있을 수 있습니다. 업데이트가 완료되면 조정에서 200을 반환하도록 복귀되기 때문입니다.

이 사용 예의 전체 원본은 GitHub의 [이 Visual Studio 프로젝트](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 에 있습니다.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1 단계: 끌어오기 서버 및 Automation 계정 설정

인증된(`Connect-AzAccount`) PowerShell 명령줄에서(풀 서버를 설정하는 데 몇 분 정도 소요될 수 있음):

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

사용자는 미국 동부 2, 미국 중 북부, US Gov 버지니아, 유럽 서부, 동남 아시아, 일본 동부, 인도 중부 및 오스트레일리아 남동쪽, 캐나다 중부, 유럽 등의 지역에 Automation 계정을 넣을 수 있습니다.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2 단계: VM 확장을 리소스 관리자 템플릿에 맞게 조정

VM 등록에 대한 세부 정보(PowerShell DSC VM 확장 사용)는 이 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)에서 제공합니다.
이 단계는 새 VM을 상태 구성 노드 목록의 풀 서버에 등록합니다. 이 등록의 일부는 노드에 적용할 노드 구성을 지정하고 있습니다. 이 노드 구성은 끌어오기 서버에 아직 존재 하지 않아도 되므로 4 단계에서 처음으로이 작업을 수행 하는 것이 좋습니다. 하지만 여기 2단계에서 노드의 이름 및 구성의 이름을 결정해야 합니다. 이 사용 예에서 노드는 'isvbox'이고 구성은 'ISVBoxConfig'입니다. 따라서 (DeploymentTemplate.json 지정할) 노드 구성 이름은 'ISVBoxConfig.isvbox'입니다.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3 단계: 필요한 DSC 리소스를 끌어오기 서버에 추가

Azure Automation 계정에 DSC 리소스를 설치하기 위해 PowerShell 갤러리가 계측됩니다.
원하는 리소스로 이동 하 고 "Azure Automation에 배포" 단추를 클릭 합니다.

![PowerShell 갤러리 예](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Azure Portal에 최근에 추가 된 다른 기술을 사용 하 여 새 모듈을 끌어오거나 기존 모듈을 업데이트할 수 있습니다. Automation 계정 리소스, 자산 타일, 마지막으로 모듈 타일을 차례로 클릭 합니다. 갤러리 찾아보기 아이콘을 사용 하면 갤러리에서 모듈 목록을 보고, 세부 정보로 드릴 다운 하 고, 궁극적으로 Automation 계정으로 가져올 수 있습니다. 이는 모듈을 최신 상태로 유지할 수 있는 좋은 방법입니다. 그리고 가져오기 기능은 다른 모듈의 종속성을 확인하여 동기화에서 빠져 나가지 않도록 합니다.

또한 나중에 업그레이드 하려는 경우가 아니면 리소스 당 한 번만 사용 되는 수동 방법이 있습니다. PowerShell 통합 모듈 작성에 대 한 자세한 내용은 [Azure Automation 통합 모듈 제작](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)을 참조 하세요.

>[!NOTE]
>Windows 컴퓨터에 대 한 PowerShell 통합 모듈의 폴더 구조는 Azure Automation에 필요한 폴더 구조와 약간 다릅니다. 

1. Windows [Management Framework v5](https://aka.ms/wmf5latest) (windows 10에는 필요 하지 않음)를 설치 합니다.

2. 통합 모듈을 설치 합니다.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. **C:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** 에서 임시 폴더로 모듈 폴더를 복사 합니다.

4. 주 폴더에서 샘플과 설명서를 삭제 합니다.

5. Zip 파일의 이름을 폴더 이름으로 지정 하 여 기본 폴더를 압축 합니다.

6. Azure Storage 계정의 blob storage와 같은 연결할 수 있는 HTTP 위치에 ZIP 파일을 넣습니다.

7. 다음 명령을 실행합니다.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

포함 된 예제에서는 cChoco 및 xNetworking에 대해 이러한 단계를 구현 합니다. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4 단계: 끌어오기 서버에 노드 구성 추가

구성을 풀 서버로 처음 가져와 컴파일하는 데는 별다른 사항이 없습니다. 동일한 구성의 모든 가져오기 또는 컴파일은 동일 하 게 보입니다. 패키지를 업데이트하고 프러덕션에 푸시해야 할 때마다 구성 파일이 정확한지 확인한 후(새 패키지 버전 포함) 이 단계를 수행합니다. **Isvboxconfig**구성 파일은 다음과 같습니다.

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

다음은 **New-ConfigurationScript** 스크립트입니다 (Az module을 사용 하도록 수정 됨).

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

이러한 단계를 수행 하면 **Isvboxconfig. isvbox** 라는 새 노드 구성이 끌어오기 서버에 배치 됩니다. 노드 구성 이름은로 `configurationName.nodeName`빌드됩니다.

## <a name="step-5-create-and-maintain-package-metadata"></a>5 단계: 패키지 메타 데이터 만들기 및 유지 관리

패키지 리포지토리에 저장 하는 각 패키지에 대해이를 설명 하는 Nuspec 필요 합니다. 이를 컴파일하여 NuGet 서버에 저장 해야 합니다. 이 프로세스는 [여기](https://docs.nuget.org/create/creating-and-publishing-a-package)에서 설명합니다. 

**MyGet.org** 를 NuGet 서버로 사용할 수 있습니다. 이 서비스는 구매할 수 있지만 무료 스타터 SKU입니다. [Nuget](https://www.nuget.org/)에서 개인 패키지에 대 한 사용자 고유의 nuget 서버를 설치 하는 방법에 대 한 지침을 찾을 수 있습니다.

## <a name="step-6-tie-it-all-together"></a>6 단계: 함께 연결

버전이 QA를 전달 하 고 배포를 승인할 때마다 패키지가 생성 되 고 nuspec 및 nupkg가 업데이트 되어 NuGet 서버에 배포 됩니다. 새 버전 번호에 동의 하도록 구성 (4 단계)도 업데이트 해야 합니다. 그런 다음이 파일을 끌어오기 서버로 보내고 컴파일해야 합니다.

해당 지점부터 업데이트를 끌어오고 설치하는 작업은 해당 구성에 종속되는 VM의 역할입니다. 이러한 각 업데이트는 간단 하 고 PowerShell의 두 줄만 있습니다. Azure DevOps의 경우 일부는 빌드에 함께 연결 될 수 있는 빌드 작업에 캡슐화 됩니다. 이 [문서](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)에 자세한 내용이 나와 있습니다. 이 [GitHub 리포지토리](https://github.com/Microsoft/vso-agent-tasks) 는 사용 가능한 빌드 작업을 자세히 설명 합니다.

## <a name="related-articles"></a>관련된 문서
* [Azure Automation DSC 개요](automation-dsc-overview.md)
* [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure Automation 상태 구성](automation-dsc-overview.md)을 참조 하세요.
- 시작 하려면 [Azure Automation 상태 구성 시작](automation-dsc-getting-started.md)을 참조 하세요.
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조 하세요.
- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조 하세요.
- 연속 배포 파이프라인에서 Azure Automation 상태 구성을 사용 하는 예제를 보려면 [Azure Automation State 구성 및 Chocolatey를 사용 하 여 연속 배포](automation-dsc-cd-chocolatey.md)를 참조 하세요.
