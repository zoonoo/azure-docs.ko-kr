<properties
	pageTitle="DevTest Labs FAQ | Microsoft Azure"
	description="일반적인 DevTest Labs 질문에 대한 답 찾기"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="tarcher"/>

# DevTest Labs FAQ

이 문서에서는 DevTest Labs에 대한 일반적인 질문에 대한 답변을 제공합니다.

## 일반
- [DevTest Labs를 사용해야 하는 이유는 무엇입니까?](#why-should-i-use-devtest-labs)
- ["걱정 없는 무료, 셀프 서비스"는 무슨 의미입니까?](#what-does-quotworry-free-self-servicequot-mean)
- [DevTest Labs를 어떻게 사용할 수 있습니까?](#how-can-i-use-devtest-labs)
- [DevTest Labs에 대해 요금이 어떻게 청구됩니까?](#how-will-i-be-charged-for-devtest-labs)
 
## 보안 
- [DevTest Labs의 다른 보안 수준은 무엇입니까?](#what-are-the-different-security-levels-in-devtest-labs)
- [사용자가 단일 작업만을 수행하도록 특정 역할을 어떻게 만듭니까?](#how-do-i-create-a-specific-role-to-allow-users-to-perform-only-a-single-task)
 
## CI/CD 통합 및 자동화 
 
- [DevTest Labs는 CI/CD 도구 체인과 통합됩니까?](#does-devtest-labs-integrate-with-my-cicd-toolchain)
 
## 가상 컴퓨터 
 
- [DevTest Labs 내에서 표시되는 Azure 가상 컴퓨터 블레이드의 특정 VM이 표시되지 않는 이유는 무엇입니까?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-devtest-labs)
- [사용자 지정 이미지와 수식 간의 차이는 무엇입니까?](#what-is-the-difference-between-custom-images-and-formulas)
- [동일한 템플릿에서 여러 VM을 한 번에 어떻게 만듭니까?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
- [기존 Azure VM을 DevTest Labs 랩으로 어떻게 이동합니까?](#how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab)
- [여러 개의 디스크를 VM에 연결할 수 있습니까?](#can-i-attach-multiple-disks-to-my-vms)
- [사용자 지정 이미지를 만들도록 VHD 파일 업로드 과정을 어떻게 자동화합니까?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
 
## 아티팩트 
 
- [아티팩트는 무엇입니까?](#what-are-artifacts)
 
## 랩 구성 
 
- [Azure Resource Manager 템플릿에서 어떻게 랩을 만듭니까?](#how-do-i-create-a-lab-from-an-arm-template)
- [서로 다른 리소스 그룹에서 만들어진 모든 VM이 임의의 이름을 갖는 이유는 무엇입니까? 이름을 변경하거나 이러한 리소스 그룹을 수정할 수 있습니까?](#why-are-all-of-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
- [동일한 구독에서 얼마나 많은 랩을 만들 수 있습니까?](#how-many-labs-can-i-create-under-the-same-subscription)
- [랩당 얼마나 많은 VM을 만들 수 있습니까?](#how-many-vms-can-i-create-per-lab)
 
## 문제 해결 
 
- [VM 생성 도중 아티팩트가 실패했습니다. 어떻게 해결합니까?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
- [기존 가상 네트워크가 제대로 저장되지 않는 이유는 무엇입니까?](#why-isnt-my-existing-virtual-network-saving-properly)

### DevTest Labs를 사용해야 하는 이유는 무엇입니까? 
DevTest Labs를 통해 팀 시간과 비용을 절약할 수 있습니다. 개발자는 다양한 자료를 사용하여 자신의 환경을 만들고 아티팩트를 사용하여 응용 프로그램을 신속하게 배포하고 구성할 수 있습니다. 사용자 지정 이미지 및 수식을 사용하여 가상 컴퓨터는 템플릿으로 저장되어 쉽게 재현될 수 있습니다. 또한 랩은 랩 관리자가 낭비를 줄이고 팀의 환경을 관리하도록 허용하는 자동 종료, 비용 임계값, 사용자당 최대 VM 및 최대 VM 크기와 같은 몇 가지 구성 가능한 정책을 제공합니다. DevTest Labs의 자세한 설명은 [개요](devtest-lab-overview.md)를 읽거나 [소개 비디오](/documentation/videos/videos/what-is-azure-devtest-labs)를 확인하세요.

### "걱정 없는 무료, 셀프 서비스"는 무슨 의미입니까?
"걱정 없는 무료, 셀프 서비스"는 개발자 및 테스터가 필요에 따라 자신의 환경을 만들 수 있으며 관리자가 DevTest Labs를 통해 낭비를 최소화하고 비용을 제어한다는 인식의 보안을 갖는 것을 의미합니다. 관리자는 허용되는 VM 크기, VM의 최대 수 및 VM의 시작 및 종료 시기를 지정할 수 있습니다. 또한 랩에서 리소스가 사용되는 방식을 인식하기 위해 DevTest Labs를 통해 쉽게 비용을 모니터링하고 경고를 설정합니다.

### DevTest Labs를 어떻게 사용할 수 있습니까? 
DevTest Labs는 개발 또는 테스트 환경이 필요할 때마다 그리고 신속하게 재현 및/또는 비용 절감 정책을 통해 관리하려고 하는 경우 유용합니다.

고객이 DevTest Labs를 사용하는 몇 가지 시나리오는 다음과 같습니다.

- 한 곳에서 개발 및 테스트 환경 관리, 팀 내에서 빌드를 공유하기 위해 비용 및 사용자 지정 이미지를 줄이도록 정책 활용.
- 개발 단계에 걸쳐 디스크 상태를 저장하도록 사용자 지정 이미지를 사용하여 응용 프로그램 개발.
- 진행 상태와 관련하여 비용 추적.
- 품질 보증 테스트에 대한 대량 테스트 환경 만들기.
- 다양한 환경에서 응용 프로그램을 쉽게 구성하고 재현하도록 아티팩트 및 수식 사용.
- 해커톤(공동 개발 또는 테스트 작업)에 대해 VM을 배포한 다음 이벤트가 종료될 때 쉽게 프로비전 해제.

### DevTest Labs에 대해 요금이 어떻게 청구됩니까? 
DevTest Labs는 무료 서비스입니다. 즉 랩을 만들고 정책, 템플릿 및 아티팩트를 구성하는 것이 무료입니다. 가상 컴퓨터, 저장소 계정 및 가상 네트워크와 같은 랩 내에서 사용하는 Azure 리소스에 대해서만 비용을 지불합니다. 랩 리소스의 비용에 대한 자세한 내용은 [Azure DevTest Labs 가격 책정](https://azure.microsoft.com/pricing/details/devtest-lab/)을 참조하세요.

### DevTest Labs의 다른 보안 수준은 무엇입니까?  
보안 액세스는 [Azure 역할 기반 액세스 제어(RBAC)](../active-directory/role-based-access-built-in-roles.md)를 통해 결정됩니다. 액세스의 작동 방식을 이해하기 위해 RBAC에 의해 정의된 대로 사용 권한, 역할 및 범위 사이의 차이점을 이해하는 데 도움을 줍니다. 사용 권한은 특정 작업에 대해 정의된 액세스입니다(예: 모든 가상 컴퓨터에 대한 읽기 액세스). 역할은 그룹화되고 사용자에게 할당될 수 있는 사용 권한의 집합입니다(예: "구독 소유자"는 구독 내의 모든 리소스에 대한 액세스를 가짐). 범위는 Azure 리소스의 계층 구조 내 수준입니다(예: 리소스 그룹 또는 단일 랩 또는 전체 구독).
 
DevTest Labs의 범위 내에 사용자 사용 권한을 정의하는 두 가지 유형의 역할(랩 소유자 및 랩 사용자)이 있습니다.

- 랩 소유자 - 랩 소유자는 랩 내의 모든 리소스에 대한 액세스를 가지므로 정책을 수정하고 VM을 읽고 쓰고, 가상 네트워크를 변경하는 등의 작업을 할 수 있습니다.
- 랩 사용자 - 랩 사용자는 VM, 정책 및 가상 네트워크와 같은 모든 랩 리소스를 볼 수 있지만 정책 또는 다른 사용자가 만든 VM을 수정할 수 없습니다. 또한 DevTest Labs에서 사용자 지정 역할을 만들 수 있으며 [특정 랩 정책에 사용자 권한 부여](devtest-lab-grant-user-permissions-to-specific-lab-policies.md) 문서에서 작업을 수행하는 방법을 배울 수 있습니다.
 
범위는 계층적이므로 사용자가 특정 범위에서 사용 권한을 가진 경우 포함된 모든 하위 수준 범위에서 해당 사용 권한이 자동으로 부여됩니다. 예를 들어 사용자가 구독 소유자의 역할에 할당되면 모든 가상 컴퓨터, 모든 가상 네트워크 및 모든 랩을 포함하는 구독의 모든 리소스에 대한 액세스를 가집니다. 따라서 구독 소유자는 자동으로 랩 소유자의 역할을 상속합니다. 그러나 반대의 경우는 적용되지 않습니다. 랩 소유자는 구독 수준보다 낮은 범위인 랩에 대한 액세스를 가집니다. 따라서 랩 소유자는 랩 외부에 있는 가상 컴퓨터 또는 가상 네트워크 또는 리소스를 볼 수 없습니다.

### 사용자가 특정 작업을 수행하도록 허용하는 역할을 어떻게 만듭니까?
사용자 지정 역할을 만들고 해당 역할에 권한을 할당하는 방법에 대한 포괄적인 문서를 여기에서 찾아볼 수 있습니다. 랩의 모든 VM을 시작 및 중지하는 권한이 있는 "DevTest Labs 고급 사용자" 역할을 만드는 스크립트의 예는 다음과 같습니다.
 
	$policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User") 
	$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
	$policyRoleDef.Id = $null 
	$policyRoleDef.Name = "DevTest Labs Advance User" 
	$policyRoleDef.IsCustom = $true 
	$policyRoleDef.AssignableScopes.Clear() 
	$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
	$policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)  
 
### DevTest Labs는 CI/CD 도구 체인과 통합됩니까? 
VSTS를 사용하는 경우 DevTest Labs에서 릴리스 파이프라인을 자동화할 수 있는 [Azure DevTest Labs 작업 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)이 있습니다. 이 확장의 몇 가지 용도는 다음과 같습니다.

- VM을 자동으로 만들고 배포 및 Azure File Copy 또는 PowerShell VSTS 작업을 사용하여 최신 빌드로 구성.
- 추가 조사를 위해 동일한 VM에 대한 버그를 재현하는 테스트 후 VM의 상태를 자동으로 캡처.
- 더 이상 필요 없는 경우 릴리스 파이프라인의 끝에 VM 삭제.

다음 블로그 게시물은 VSTS 확장 사용에 대한 지침 및 정보를 제공합니다.
 
- [Azure DevTest Labs – VSTS 확장](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
- [VSTS에서 기존 AzureDevTestLab의 새 VM 배포](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [AzureDevTestLabs에 대한 연속 배포에 대해 VSTS 릴리스 관리 사용](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)
 
다른 CI/CD 도구 체인의 경우 VSTS 작업 확장을 통해 얻을 수 있는 앞에서 언급한 모든 시나리오는 [Azure PowerShell cmdlet](../resource-group-template-deploy.md) 및 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)를 사용한 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)을 통해 마찬가지로 얻을 수 있습니다. 또한 [DevTest Labs용 REST API](http://aka.ms/dtlrestapis)를 사용하여 도구 체인과 통합할 수 있습니다.

### DevTest Labs 내에서 표시되는 Azure 가상 컴퓨터 블레이드의 특정 VM이 표시되지 않는 이유는 무엇입니까?
VM이 DevTest Labs에서 만들어진 경우 해당 VM 및 사용자가 랩 블레이드 및 **가상 컴퓨터** 블레이드 모두에서 볼 수 있는 액세스에 대한 사용 권한이 부여됩니다. DevTest Labs 사용자인 경우 랩의 **모든 가상 컴퓨터** 블레이드를 통해 랩에서 만들어진 모든 가상 컴퓨터를 볼 수 있습니다. 그러나 DevTest Labs 사용자로서 다른 사용자가 만든 VM 리소스에 대한 읽기 액세스가 자동으로 부여되지 않으므로 **가상 컴퓨터** 블레이드에 표시되지 않습니다.

### 사용자 지정 이미지와 수식 간의 차이는 무엇입니까? 
사용자 지정 이미지는 VHD(가상 하드 디스크)인 반면 수식은 저장하고 재현할 수 있는 추가 설정으로 구성할 수 있는 이미지입니다. 사용자 지정 이미지는 동일한 기본, 변경할 수 없는 이미지로 여러 환경을 신속하게 만들려는 경우 적합할 수 있습니다. 수식은 최신 비트, 가상 네트워크/서브넷 또는 특정 크기로 VM의 구성을 재현하려는 경우 더 나을 수 있습니다. 더 자세한 설명은 [DevTest Labs에서 사용자 지정 이미지와 수식 비교](devtest-lab-comparing-vm-base-image-types.md) 문서를 참조하세요.
 
### 동일한 템플릿에서 여러 VM을 한 번에 어떻게 만듭니까? 
VM을 만드는 동안 [VSTS 작업 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 사용하거나 [Azure Resource Manager 템플릿을 생성](devtest-lab-add-vm-with-artifacts.md#save-arm-template)하고 [Windows PowerShell에서 Azure Resource Manager 템플릿을 배포](../resource-group-template-deploy.md)할 수 있습니다.
 
### 기존 Azure VM을 DevTest Labs 랩으로 어떻게 이동합니까? 
VM을 DevTest Labs로 직접 이동하는 솔루션을 설계했지만 현재 기존 VM을 다음과 같이 DevTest Labs에 복사할 수 있습니다.

1. 이 [Windows PowerShell 스크립트](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)를 사용하여 기존 VM의 VHD 파일을 복사합니다.
1. DevTest Labs 랩에 [사용자 지정 이미지를 만듭니다](devtest-lab-create-template.md).
1. 사용자 지정 이미지에서 랩에 VM을 만듭니다.
 
### 여러 개의 디스크를 VM에 연결할 수 있습니까? 
VM에 여러 디스크 연결이 지원됩니다.
 
### 사용자 지정 이미지를 만들도록 VHD 파일 업로드 과정을 어떻게 자동화합니까? 
두 가지 옵션이 있습니다.

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload)를 사용하여 랩과 연결된 저장소 계정에 VHD 파일을 복사 또는 업로드할 수 있습니다.
- [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, OSX 및 Linux에서 실행되는 독립 실행형 앱입니다.
 
랩과 연결된 대상 저장소 계정 검색:

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. 왼쪽 패널에서 **리소스 그룹**을 선택합니다.
1. 랩과 연결된 리소스 그룹을 찾고 선택합니다.
1. **개요** 블레이드에서 저장소 계정 중 하나를 선택합니다.
1. **Blob**을 선택합니다.
1. 목록에서 업로드를 찾습니다. 항목이 없으면 #4단계로 돌아가서 다른 저장소 계정을 시도합니다.
1. AzCopy 명령에서 대상으로 **URL**을 사용합니다.

### 아티팩트는 무엇입니까? 
아티팩트는 VM에 최신 비트 또는 개발 도구를 배포하는 데 사용할 수 있는 사용자 지정 가능한 요소입니다. 몇 가지 간단한 클릭으로 만드는 동안 VM에 연결되고 VM이 프로비전되면 아티팩트는 VM을 배포 및 구성합니다. [공용 Github 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)에 몇 가지 기존 아티팩트가 있지만 손쉽게 [사용자 고유의 아티팩트를 작성](devtest-lab-artifact-author.md)할 수도 있습니다.

### Azure Resource Manager 템플릿에서 어떻게 랩을 만듭니까? 
[랩 Azure Resource Manager 템플릿의 Github 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)가 있습니다. 이러한 각 템플릿에는 클릭하여 Azure 구독 아래에 DevTest Labs 랩을 배포할 수 있는 링크가 있습니다.
 
### 서로 다른 리소스 그룹에서 만들어진 모든 VM이 임의의 이름을 갖는 이유는 무엇입니까? 이름을 변경하거나 이러한 리소스 그룹을 수정할 수 있습니까? 
리소스 그룹은 DevTest Labs가 사용자 권한 및 가상 컴퓨터에 대한 액세스를 관리하기 위해 이러한 방식으로 만들어집니다. 더 많은 유연성을 허용하기 위해 이 환경을 향상시키도록 작업 중이지만 필요에 따라 이러한 리소스 그룹의 이름을 바꿀 수 있습니다. 사용 권한을 실수로 수정하지 않도록 다른 리소스 그룹으로 VM을 이동하지 않는 것이 좋습니다.
 
### 동일한 구독에서 얼마나 많은 랩을 만들 수 있습니까? 
구독당 만들 수 있는 랩의 수에는 특정 제한이 없지만 사용하는 리소스는 구독당 제한됩니다. [Azure 구독에 부과된 한도 및 할당량](../azure-subscription-service-limits.md) 및 [이러한 한도를 늘리는 방법](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)에 대해 알아볼 수 있습니다.
 
### 랩당 얼마나 많은 VM을 만들 수 있습니까? 
랩당 만들 수 있는 VM의 수에는 특정 제한이 없지만 현재 랩은 표준 저장소에서 동시에 실행되는 약 40개의 VM 및 프리미엄 저장소에서 동시에 실행되는 25개의 VM을 지원합니다. 현재 이러한 제한을 늘리도록 작업 중입니다.
 
### VM 생성 도중 아티팩트가 실패했습니다. 어떻게 해결합니까? 
실패한 아티팩트에 대한 로그를 얻는 방법을 알아보려면 MVP 중 한 명이 작성한 블로그 게시물 [AzureDevTestLabs에서 실패한 아티팩트 문제를 해결하는 방법](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)을 참조하세요.
 
### 기존 가상 네트워크가 제대로 저장되지 않는 이유는 무엇입니까?  
한 가지 가능성은 가상 네트워크 이름에 기간이 포함되어 있는 것입니다. 그렇다면 기간을 제거하거나 하이픈으로 바꾼 다음 가상 네트워크를 다시 저장합니다.

<!---HONumber=AcomDC_0810_2016-->