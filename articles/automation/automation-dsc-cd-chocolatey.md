---
title: Azure 자동화 상태 구성 초콜릿을 가진 지속적인 배포
description: 초콜릿 패키지 관리자를 사용하여 Azure 자동화 상태 구성을 사용하여 DevOps 연속 배포에 대해 설명합니다. 전체 JSON 리소스 관리자 템플릿 및 PowerShell 소스가 포함된 예제를 포함합니다.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 0c61a431b985e494148500ed0a7aeb106534ed2c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392112"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>자동화 상태 구성 및 초콜릿을 사용하여 가상 시스템에 지속적인 배포 제공

DevOps 세계에서는 연속 통합 파이프라인의 다양한 지점을 지원하는 많은 도구가 있습니다. Azure 자동화 [상태 구성은](automation-dsc-overview.md) DevOps 팀이 사용할 수 있는 옵션에 새로 추가된 것을 환영합니다. 

Azure 자동화는 자격 증명, 일정 및 전역 변수와 같은 런북, 노드 및 공유 리소스를 사용하여 다양한 작업을 자동화할 수 있는 Microsoft Azure의 관리형 서비스입니다. Azure 자동화 상태 구성은 이 자동화 기능을 확장하여 DSC(PowerShell 원하는 상태 구성) 도구를 포함합니다. 여기서 알맞은 [개요](automation-dsc-overview.md)를 제공합니다.

이 문서에서는 Windows 컴퓨터에 대한 CD(연속 배포)를 설정하는 방법을 보여 줍니다. 웹 사이트와 같이 역할에 필요한 만큼의 Windows 컴퓨터를 포함하고 거기에서 추가 역할로 이동하는 기술을 쉽게 확장할 수 있습니다.

![IaaS VM에 대한 연속 배포](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="at-a-high-level"></a>높은 수준

여기에는 꽤 많은 문제가 있지만 다행히도 두 가지 주요 프로세스로 나눌 수 있습니다.

- 즉 코드를 작성하여 테스트한 다음 시스템의 주 버전과 부 버전에 대해 설치 패키지를 만들고 게시하는 것입니다.
- 패키지에서 코드를 설치하고 실행하는 VM을 만들고 관리합니다.  

이러한 두 가지 핵심 프로세스가 모두 배치되면 새 버전이 만들어지고 배포될 때 VM에서 패키지를 자동으로 업데이트할 수 있습니다.

## <a name="component-overview"></a>구성 요소 개요

[apt-get과](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 같은 패키지 관리자는 Linux 세계에서 잘 알려져 있지만 Windows 에서는 그다지 알려져 있지 않습니다.
[초콜릿은](https://chocolatey.org/) 그런 일이며, 주제에 대한 스콧 핸셀먼의 [블로그는](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) 훌륭한 소개입니다. 간단히 말해서, 초콜릿은 명령줄을 사용하여 중앙 리포지토리에서 Windows 운영 체제에 패키지를 설치할 수 있습니다. 자체 리포지토리를 만들어 관리할 수 있고 Chocolatey가 사용자가 지정한 수 만큼의 리포지토리에서 패키지를 설치할 수 있습니다.

[PowerShell DSC는](/powershell/scripting/dsc/overview/overview) 컴퓨터에 대해 원하는 구성을 선언할 수 있는 PowerShell 도구입니다. 예를 들어 초콜릿을 설치하려는 경우 IIS가 설치되고 포트 80이 열리고 웹 사이트의 버전 1.0.0이 설치되면 DSC 로컬 구성 관리자(LCM)가 해당 구성을 구현합니다. DSC 풀 서버는 컴퓨터에 대한 구성 저장소를 보유합니다. 각 컴퓨터의 LCM이 주기적인 검사를 통해 자신의 구성이 저장된 구성과 일치하는지 확인합니다. 상태를 보고하거나, 컴퓨터를 저장된 구성과 일치하는 상태로 되돌리는 작업을 수행할 수 있습니다. 풀 서버에서 저장된 구성을 편집하여 컴퓨터나 컴퓨터 집합이 변경된 구성과 일치하게 만들 수 있습니다.

DSC 리소스는 네트워킹, Active Directory 또는 SQL Server 관리와 같은 특정 기능이 있는 코드 모듈입니다. Chocolatey DSC 리소스는 NuGet 서버 액세스(서로 간), 패키지 다운로드, 패키지 설치 방법 등을 이해하고 있습니다. 여러 다른 DSC 리소스는 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)에 있습니다. 이러한 모듈을 Azure 자동화 상태 구성 풀 서버에서 설치하여 구성에서 사용할 수 있습니다.

리소스 관리자 템플릿은 네트워크, 서브넷, 네트워크 보안 및 라우팅, 로드 밸러블러, NIC, VM 등과 같은 인프라를 생성하는 선언적인 방법을 제공합니다. 다음은 리소스 관리자 배포 모델(선언적)과 ASM(클래식) 배포 모델(명령적)을 비교하는 [문서입니다.](../azure-resource-manager/management/deployment-models.md) 이 문서에는 컴퓨팅, 저장소 및 네트워크와 같은 핵심 리소스 공급자에 대한 설명이 포함되어 있습니다.

리소스 관리자 템플릿의 주요 기능 중 하나는 프로비전된 VM에 VM 확장을 설치하는 기능입니다. VM 확장에는 사용자 지정 스크립트 실행, 바이러스 백신 소프트웨어 설치 및 DSC 구성 스크립트 실행과 같은 특정 기능이 있습니다. VM 확장에는 여러 다른 형식이 있습니다.

## <a name="quick-trip-around-the-diagram"></a>간략히 다이어그램 둘러보기

맨 위에서 시작하여 코드를 작성하고, 빌드하고, 테스트한 다음, 설치 패키지를 만듭니다. Chocolatey는 MSI, MSU, ZIP 등과 같은 다양한 형태의 설치 패키지를 처리할 수 있습니다. 그리고 초콜릿의 기본 기능이 그것에 달려 있지 않은 경우 실제 설치를 할 수 있는 PowerShell의 모든 힘을 가지고. 패키지 저장소인 접근 가능한 장소에 패키지를 넣습니다. 이 사용 예는 Auzre Blob Storage 계정의 공용 폴더를 사용하지만 어디에나 있을 수 있습니다. Chocolatey는 패키지 메타데이터의 관리를 위해 NuGet 서버 및 기타 제품과 기본적으로 연동됩니다. [이 문서](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 에서는 이러한 옵션에 대해 설명합니다. 사용 예제에서는 NuGet을 사용합니다. Nuspec은 패키지에 관한 메타데이터입니다. Nuspec 정보는 NuPkg으로 컴파일되어 NuGet 서버에 저장됩니다. 구성에서 이름으로 패키지를 요청하고 NuGet 서버를 참조하면 VM의 초콜릿 DSC 리소스가 패키지를 잡고 설치합니다. 특정 버전의 패키지를 요청할 수도 있습니다.

그림 의 왼쪽 하단에는 Azure 리소스 관리자 템플릿이 있습니다. 이 사용 예에서 VM 확장은 Azure 자동화 상태 구성 끌어 서버를 노드로 등록합니다. 구성은 일반 텍스트로 한 번, 한 번MOF 파일로 컴파일된 두 번 끌어오기 서버에 저장됩니다. Azure 포털에서 MOF는 간단한 구성이 아닌 노드 구성을 나타냅니다. 노드가 노드의 구성을 알 수 있도록 노드와 연결된 아티팩트입니다. 아래의 세부 정보는 노드 구성을 노드에 할당하는 방법을 보여줍니다.

Nuspec을 만들고, 컴파일하고, NuGet 서버에 저장하는 것은 작은 일입니다. 이미 VM을 관리하고 있습니다. 

연속 배포로 다음 단계를 진행하려면 끌어오기 서버를 한 번 설정하고 노드를 한 번 등록하고 서버에 초기 구성을 만들고 저장해야 합니다. 패키지를 업그레이드하고 리포지토리에 배포하면 필요에 따라 끌어오기 서버의 구성 및 노드 구성만 새로 고치면 됩니다.

리소스 관리자 템플릿으로 시작하지 않는 경우 괜찮습니다. 끌어오기 서버로 VM을 등록하는 데 도움이 되는 PowerShell 명령이 있습니다. 자세한 내용은 [Azure 자동화 상태 구성의 관리를 위한 온보딩 컴퓨터를](automation-dsc-onboarding.md)참조하십시오.

## <a name="about-the-usage-example"></a>사용 예에 대해

이 문서의 사용 예는 Azure 갤러리의 일반 Windows Server 2012 R2 이미지의 VM으로 시작합니다. 아무 저장된 이미지에서나 시작하고 DSC 구성을 통해 조정을 진행할 수 있습니다.
그러나 이미지에 반영되는 구성을 변경하는 작업은 DSC를 사용하여 구성을 동적으로 업데이트하는 작업보다 훨씬 어렵습니다.

이 방법을 VM에 사용하기 위해 Resource Manager 템플릿이나 VM 확장을 사용할 필요는 없습니다. 또한 VM이 CD 관리를 위해 Azure에 있을 필요도 없습니다. 오직 Chocolatey을 설치하고 풀 서버 위치를 알 수 있게 VM에 LCM을 구성하기만 하면 됩니다.

프로덕션 중인 VM에서 패키지를 업데이트할 때 업데이트를 설치하는 동안 해당 VM을 회전하지 않아야 합니다. 이를 수행하는 방법은 크게 다릅니다. 예를 들어, Azure 부하 분산 장치 뒤에 있는 VM에서는 사용자 지정 프로브를 추가할 수 있습니다. VM을 업데이트하는 동안 프로브 엔드포인트가 400을 반환하게 합니다. 이 변경을 위해 필요한 조정은 구성 안에 있을 수 있습니다. 업데이트가 완료되면 조정에서 200을 반환하도록 복귀되기 때문입니다.

이 사용 예의 전체 원본은 GitHub의 [이 Visual Studio 프로젝트](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 에 있습니다.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1단계: 끌어오기 서버 및 자동화 계정 설정

인증된(`Connect-AzAccount`) PowerShell 명령줄에서(풀 서버를 설정하는 데 몇 분 정도 소요될 수 있음):

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

자동화 계정을 미국 동부 2, 미국 중남부, 미국 정부 버지니아, 서유럽, 동남아시아, 일본 동부, 인도 중부 및 호주 남동부, 캐나다 중부, 북유럽 중 하나라도 입력할 수 있습니다.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2단계: 리소스 관리자 템플릿에 VM 확장을 조정합니다.

VM 등록에 대한 세부 정보(PowerShell DSC VM 확장 사용)는 이 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)에서 제공합니다.
이 단계는 새 VM을 상태 구성 노드 목록의 풀 서버에 등록합니다. 이 등록의 일부는 노드에 적용할 노드 구성을 지정하고 있습니다. 이 노드 구성은 끌어오기 서버에 아직 존재할 필요가 없으므로 4단계는 처음으로 이 작업이 수행되는 위치입니다. 하지만 여기 2단계에서 노드의 이름 및 구성의 이름을 결정해야 합니다. 이 사용 예에서 노드는 'isvbox'이고 구성은 'ISVBoxConfig'입니다. 따라서 (DeploymentTemplate.json 지정할) 노드 구성 이름은 'ISVBoxConfig.isvbox'입니다.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3단계: 끌어오기 서버에 필요한 DSC 리소스 추가

Azure Automation 계정에 DSC 리소스를 설치하기 위해 PowerShell 갤러리가 계측됩니다.
원하는 리소스로 이동하여 "Azure 자동화에 배포" 단추를 클릭합니다.

![PowerShell 갤러리 예](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

최근에 Azure 포털에 추가된 또 다른 기술을 사용하면 새 모듈을 가져오거나 기존 모듈을 업데이트할 수 있습니다. 자동화 계정 리소스, 자산 타일 및 마지막으로 모듈 타일을 클릭합니다. 갤러리 찾아보기 아이콘을 사용하면 갤러리의 모듈 목록을 보고 세부 정보로 드릴다운한 다음 궁극적으로 자동화 계정으로 가져올 수 있습니다. 이는 모듈을 최신 상태로 유지할 수 있는 좋은 방법입니다. 그리고 가져오기 기능은 다른 모듈의 종속성을 확인하여 동기화에서 빠져 나가지 않도록 합니다.

나중에 업그레이드하지 않으려는 경우가 아니면 리소스당 한 번만 사용되는 수동 접근 방식도 있습니다. PowerShell 통합 모듈 작성에 대한 자세한 내용은 [Azure 자동화에 대한 통합 모듈 작성을](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)참조하십시오.

>[!NOTE]
>Windows 컴퓨터에 대한 PowerShell 통합 모듈의 폴더 구조는 Azure 자동화에서 기대하는 폴더 구조와 약간 다릅니다. 

1. [설치 윈도우 관리 프레임 워크 v5](https://aka.ms/wmf5latest) (윈도우 10에 필요하지 않음).

2. 통합 모듈을 설치합니다.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. 모듈 폴더를 **c:\프로그램 파일\WindowsPowerShell\모듈\모듈-이름에서** 임시 폴더에 복사합니다.

4. 기본 폴더에서 샘플 및 설명서를 삭제합니다.

5. 폴더 의 이름으로 ZIP 파일의 이름을 지정, 기본 폴더를 압축합니다.

6. ZIP 파일을 Azure Storage 계정의 Blob 저장소와 같이 연결할 수 있는 HTTP 위치에 넣습니다.

7. 다음 명령을 실행합니다.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

포함된 예제는 cChoco 및 xNetworking에 대한 이러한 단계를 구현합니다. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4단계: 끌어오기 서버에 노드 구성 추가

구성을 풀 서버로 처음 가져와 컴파일하는 데는 별다른 사항이 없습니다. 나중에 동일한 구성의 모든 가져오기 또는 컴파일은 정확히 동일하게 보입니다. 패키지를 업데이트하고 프러덕션에 푸시해야 할 때마다 구성 파일이 정확한지 확인한 후(새 패키지 버전 포함) 이 단계를 수행합니다. 구성 파일 **ISVBoxConfig.ps1은**다음과 같습니다.

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

다음은 **New-ConfigurationScript.ps1** 스크립트(Az 모듈을 사용하도록 수정됨)입니다.

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

이러한 단계를 수행하면 **ISVBoxConfig.isvbox라는** 새 노드 구성이 끌어오기 서버에 배치됩니다. 노드 구성 이름은 로 `configurationName.nodeName`빌드됩니다.

## <a name="step-5-create-and-maintain-package-metadata"></a>5단계: 패키지 메타데이터 생성 및 유지 관리

패키지 리포지토리에 넣는 각 패키지에 대해 이를 설명하는 Nuspec이 필요합니다. NuGet 서버에 컴파일되어 저장해야 합니다. 이 프로세스는 [여기](https://docs.nuget.org/create/creating-and-publishing-a-package)에서 설명합니다. 

**MyGet.org** NuGet 서버로 사용할 수 있습니다. 이 서비스를 구입할 수 있지만, 너는 무료 스타터 SKU입니다. [NuGet에서](https://www.nuget.org/)개인 패키지에 대한 자체 NuGet 서버 설치에 대한 지침을 찾을 수 있습니다.

## <a name="step-6-tie-it-all-together"></a>6 단계 : 모두 함께 묶어

버전이 QA를 통과하고 배포가 승인될 때마다 패키지가 만들어지고 nuspec 및 nupkg이 업데이트되어 NuGet 서버에 배포됩니다. 구성(4단계)도 새 버전 번호에 동의하도록 업데이트해야 합니다. 그런 다음 끌어오기 서버로 전송되고 컴파일되어야 합니다.

해당 지점부터 업데이트를 끌어오고 설치하는 작업은 해당 구성에 종속되는 VM의 역할입니다. 이러한 각 업데이트는 간단합니다 - PowerShell의 한 줄 또는 두 개. Azure DevOps의 경우 그 중 일부는 빌드에서 함께 연결할 수 있는 빌드 작업에 캡슐화되어 있습니다. 이 [문서](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)에 자세한 내용이 나와 있습니다. 이 [GitHub 리포지토리는](https://github.com/Microsoft/vso-agent-tasks) 사용 가능한 빌드 작업에 대해 자세히 설명합니다.

## <a name="related-articles"></a>관련 문서
* [Azure 자동화 DSC 개요](automation-dsc-overview.md)
* [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure 자동화 상태 구성을](automation-dsc-overview.md)참조하십시오.
- 시작하려면 Azure [자동화 상태 구성을 시작하십시오.](automation-dsc-getting-started.md)
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure 자동화 상태 구성의 구성 컴파일을](automation-dsc-compile.md)참조하십시오.
- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하십시오.
- 가격 정보는 [Azure 자동화 상태 구성 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.
- 연속 배포 파이프라인에서 Azure 자동화 상태 구성을 사용하는 예제를 보려면 [Azure 자동화 상태 구성 및 초콜릿을 사용한 지속적인 배포를](automation-dsc-cd-chocolatey.md)참조하십시오.
