---
title: Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 Azure DevTest Labs에 다중 VM 환경 및 PaaS 리소스를 만드는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: 144fd11e9c1ee3e00412320840e864a3190ccdb0
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833980"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)을 사용하면 쉽게 [랩에 한 번에 하나의 VM을 추가](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm)할 수 있습니다. 그러나 환경에서 여러 VM을 포함하는 경우 각 VM을 개별적으로 만들어야 합니다. 다중 계층 웹앱 또는 SharePoint 팜과 같은 시나리오의 경우 한 번에 여러 VM을 만들 수 있는 메커니즘이 필요합니다. Azure Resource Manager 템플릿을 사용하면 Azure 솔루션의 인프라와 구성을 정의하고 여러 VM을 일관된 상태로 반복적으로 배포할 수 있습니다. 이 기능은 다음과 같은 이점을 제공합니다.

- Azure Resource Manager 템플릿은 소스 제어 리포지토리(GitHub 또는 Azure DevOps Services Git)에서 직접 로드됩니다.
- 일단 구성되면 사용자가 Azure Portal에서 다른 유형의 [VM 자료](./devtest-lab-comparing-vm-base-image-types.md)를 처리하는 것처럼 Azure Resource Manager 템플릿을 선택하여 환경을 만들 수 있습니다.
- Azure Resource Manager 템플릿과 IaaS VM의 환경에서 Azure PaaS 리소스를 프로비전할 수 있습니다.
- 다른 유형의 자료로 만든 개별 VM 외에도 랩에서 환경의 비용을 추적할 수 있습니다.
- PaaS 리소스가 만들어지고 비용 추적에 표시되지만 VM 자동 종료는 PaaS 리소스에 적용되지 않습니다.

단일 작업에서 모든 랩 리소스를 배포, 업데이트 또는 삭제하는 데 많은 [Resource Manager 템플릿 사용의 이점](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager)에 대해 자세히 알아봅니다.

> [!NOTE]
> 더 많은 랩 VM을 만드는 기준으로 Resource Manager 템플릿을 사용하는 경우 다중 VM 또는 단일 VM을 만드는지 염두에 둘 차이점이 있습니다. [가상 머신의 Azure Resource Manager 템플릿 사용](devtest-lab-use-resource-manager-template.md)은 이러한 차이점을 더 자세히 설명합니다.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="devtest-labs-public-environments"></a>DevTest Labs 공개 환경
Azure DevTest Labs에 제공되는 [Azure Resource Manager 템플릿의 공용 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments)를 사용하면 외부 GitHub 소스에 직접 연결하지 않고도 환경을 구축할 수 있습니다. 이 리포지토리에는 Azure Web Apps, Service Fabric Cluster 및 개발 SharePoint Farm 환경처럼 자주 사용되는 템플릿이 포함되어 있습니다. 이 기능은 사용자가 만든 모든 랩에 포함된 아티팩트의 공용 리포지토리와 비슷합니다. 이 환경 리포지토리를 사용하면 랩 내에서 PaaS 리소스에 원활한 시작 환경을 제공하기 위해 최소한의 입력 매개 변수로 미리 작성된 환경 템플릿으로 빠르게 시작할 수 있습니다. 자세한 내용은 [DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조하세요.

## <a name="configure-your-own-template-repositories"></a>사용자 고유의 템플릿 리포지토리 구성
코드 기반 인프라와 코드 기반 구성을 사용하는 모범 사례 중 하나인 환경 템플릿은 소스 제어에서 관리해야 합니다. Azure DevTest Labs에서는 이 사례를 따르고 GitHub 또는 Azure DevOps Services Git 리포지토리에서 직접 Azure Resource Manager 템플릿을 모두 로드합니다. 결과적으로, Resource Manager 템플릿은 테스트 환경에서 프로덕션 환경까지 전체 릴리스 주기에서 사용될 수 있습니다.

[공용 GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments)에서 DevTest Labs 팀이 만든 템플릿을 확인하세요. 이 공용 리포지토리에서 직접 사용하거나 필요에 맞게 사용자 지정할 수 있으며 다른 사용자가 공유한 템플릿을 볼 수 있습니다. 템플릿을 만든 후 이를 다른 사용자와 공유하려면 이 리포지토리에 저장합니다. 클라우드에서 환경을 설정하는 데 사용될 수 있는 템플릿으로 사용자 고유의 Git 리포지토리를 설정할 수 있습니다. 

리포지토리에 Azure Resource Manager 템플릿을 구성하는 따라야 할 몇 가지 규칙이 있습니다.

- 마스터 템플릿 파일의 이름은 `azuredeploy.json`으로 지정해야 합니다. 

    ![주요 Azure Resource Manager 템플릿 파일](./media/devtest-lab-create-environment-from-arm/master-template.png)

- 매개 변수 파일에 정의한 매개 변수 값을 사용하려면 매개 변수 파일의 이름을 `azuredeploy.parameters.json`으로 지정해야 합니다.
- DevTest Labs에서 중첩된 템플릿을 자동으로 관리할 수 있도록 매개 변수 `_artifactsLocation` 및 `_artifactsLocationSasToken`을 사용하여 parametersLink URI 값을 생성합니다. 자세한 내용은 [테스트 환경에 대 한 중첩 된 Azure Resource Manager 템플릿을 배포](deploy-nested-template-environments.md)합니다.
- 템플릿 표시 이름과 설명을 지정하기 위해 메타데이터를 정의할 수 있습니다. 이 메타데이터는 `metadata.json`이라는 파일에 있어야 합니다. 다음 예제 메타데이터 파일에서는 표시 이름과 설명을 지정하는 방법을 보여 줍니다. 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

다음 단계는 Azure Portal을 사용하여 랩에 리포지토리를 추가하는 과정을 안내합니다. 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 원하는 랩을 탭합니다.   
1. 랩의 **개요** 창에서 **구성 및 정책**을 선택합니다.

    ![구성 및 정책](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **구성 및 정책** 설정 목록에서 **리포지토리**를 선택합니다. **리포지토리** 창에서 랩에 추가된 리포지토리를 나열합니다. 모든 랩에 대해 `Public Repo`라는 리포지토리가 자동으로 생성되고 사용할 여러 VM 아티팩트를 포함하고 있는 [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)에 연결됩니다.

    ![공용 리포지토리](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **추가+** 를 선택하여 Azure Resource Manager 템플릿 리포지토리를 추가합니다.
1. 두 번째 **리포지토리** 창이 열리면 다음과 같이 필요한 정보를 입력합니다.
    - **이름** - 랩에서 사용되는 리포지토리 이름을 입력합니다.
    - **Git 복제 URL** - GitHub 또는 Azure DevOps Services의 GIT HTTPS 복제 URL을 입력합니다.  
    - **분기** - Azure Resource Manager 템플릿 정의에 액세스하기 위한 분기 이름을 입력합니다. 
    - **개인 액세스 토큰** - 개인 액세스 토큰은 리포지토리에 안전하게 액세스하는 데 사용됩니다. Azure DevOps Services에서 토큰을 가져오려면 **&lt;YourName> > 내 프로필 > 보안 > 공용 액세스 토큰**을 차례로 선택합니다. GitHub에서 토큰을 가져오려면 **설정 > 공용 액세스 토큰**을 선택한 다음 아바타를 선택합니다. 
    - **폴더 경로** - 두 입력 필드 중 하나를 사용하여 슬래시(/)로 시작하는 폴더 경로를 입력합니다. 이 경로는 아티팩트 정의(첫 번째 입력 필드) 또는 Azure Resource Manager 템플릿 정의에 대한 Git 복제 URI의 상대 경로입니다.   
    
        ![공용 리포지토리](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. 필수 필드를 모두 입력하고 유효성 검사를 통과하면 **저장**을 선택합니다.

다음 섹션에서는 Azure Resource Manager 템플릿에서 환경을 만드는 과정을 안내합니다.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Azure Portal을 사용하여 Resource Manager 템플릿에서 환경 만들기

랩에 Azure Resource Manager 템플릿 리포지토리를 구성한 후에 랩 사용자는 Azure Portal을 사용하여 다음 단계에 따라 환경을 만들 수 있습니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 원하는 랩을 탭합니다.   
1. 랩의 창에서 **추가+** 를 선택합니다.
1. **기본 선택** 창에는 먼저 나열된 Azure Resource Manager 템플릿에서 사용할 수 있는 기본 이미지가 표시됩니다. 원하는 Azure Resource Manager 템플릿을 선택합니다.

    ![기본 선택](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. **추가** 창에서 **환경 이름** 값을 입력합니다. 환경 이름은 랩에서 사용자에게 표시되는 이름입니다. 나머지 입력 필드는 Azure Resource Manager 템플릿에 정의되어 있습니다. 기본값이 템플릿에 정의되어 있거나 `azuredeploy.parameter.json` 파일이 있으면 해당 입력 필드에 기본값이 표시되어 있습니다. *보안 문자열* 형식의 매개 변수에는 Azure Key Vault에 저장된 비밀을 사용할 수 있습니다. 키 자격 증명 모음에 비밀을 저장하고 랩 리소스를 만들 때 이 비밀을 사용하는 방법을 알아보려면 [Azure Key Vault에 비밀 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조하세요.  

    ![창 추가](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > 지정된 경우에도 빈 값으로 표시되는 몇 가지 매개 변수 값이 있습니다. 따라서 사용자가 Azure Resource Manager 템플릿의 매개 변수에 해당 값을 할당하는 경우 DevTest Labs는 값을 표시하지 않습니다. 대신 빈 입력 필드는 랩 사용자가 환경을 만들 때 값을 입력해야 하는 곳으로 표시됩니다.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. **추가**를 선택하여 환경을 만듭니다. 환경에서는 **내 가상 머신** 목록에 상태를 표시하는 즉시 프로비전을 시작합니다. Azure Resource Manager 템플릿에 정의된 모든 리소스를 프로비전하기 위해 랩에서 새 리소스 그룹을 자동으로 만듭니다.
1. 환경이 만들어지면 **내 가상 머신** 목록에서 환경을 선택하여 리소스 그룹 창을 열고 환경에 프로비전된 모든 리소스를 찾습니다.
    
    ![내 가상 머신 목록](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   또한 환경을 확장하여 환경에서 프로비전되는 VM의 목록만 볼 수 있습니다.
    
    ![내 가상 머신 목록](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 환경을 클릭하여 아티팩트 적용, 데이터 디스크 연결, 자동 종료 시간 변경 등과 같은 사용 가능한 작업을 확인합니다.

    ![환경 작업](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>환경 배포 자동화
Azure DevTest Labs를 사용 하는 기능을 제공을 [Azure 클래식 관리 템플릿](../azure-resource-manager/resource-group-authoring-templates.md) 랩의 리소스 집합과 환경을 만들려고 합니다. 이러한 환경에는 Resource Manager 템플릿을 사용 하 여 만들 수 있는 모든 Azure 리소스를 포함할 수 있습니다. DevTest Labs 환경을 쉽게 랩의 범위 내에서 일관 된 방식으로 복잡 한 인프라를 배포 하는 사용자를 수 있습니다. 현재 Azure portal을 사용 하 여 랩 환경을 추가 한 번 생성할 때 적합 하지만 개발 또는 여러 만들기가 발생 하는 테스트 상황에서 자동화 된 배포 환경을 향상 하려면 허용.

다음 단계를 완료 합니다 [고유한 템플릿 리포지토리 구성](#configure-your-own-template-repositories) 계속 하기 전에 섹션: 

1. 만들려는 리소스를 정의 하는 Resource Manager 템플릿을 만듭니다. 
2. 리포지토리를 Git에서 Resource Manager 템플릿을 설정 합니다. 
3. 랩에 Git 리포지토리를 연결 합니다. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>Resource Manager 템플릿을 배포 하기 위한 PowerShell 스크립트
하드 디스크에 다음 섹션에서 PowerShell 스크립트를 저장 (예: deployenv.ps1) SubscriptionId, ResourceGroupName, LabName RepositoryName, 값을 지정한 후 스크립트를 실행 하 고 Git 리포지토리에서 EnvironmentName TemplateName (폴더).

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>샘플 스크립트
랩에서 환경을 만드는 샘플 스크립트는 다음과 같습니다. 스크립트의 주석을 스크립트를 더 잘 이해 하는 데 도움이 합니다. 

```powershell
#Requires -Module Az.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzAccount

# Select the subscription that has the lab.  
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzResource command. 
New-AzResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

또한 Resource Manager 템플릿 사용 하 여 리소스를 배포 하려면 Azure CLI를 사용할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)를 참조하세요.

> [!NOTE]
> 랩 소유자 권한을 가진 사용자만 Azure PowerShell을 사용하여 Resource Manager 템플릿에서 VM을 만들 수 있습니다. Resource Manager 템플릿을 사용하여 VM 생성을 자동화하려고 하는데 사용자 권한만 있는 경우 [CLI의 **az lab vm create** 명령](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create)을 사용할 수 있습니다.

### <a name="general-limitations"></a>일반적인 제한 사항 

DevTest Labs에서 Resource Manager 템플릿을 사용하는 경우 이러한 제한 사항을 고려합니다.

- 만드는 모든 Resource Manager 템플릿은 기존 리소스를 참조할 수 없습니다. 새 리소스만 참조할 수 있습니다. 또한 DevTest Labs 외부에서 일반적으로 배포하는 기존 Resource Manager 템플릿이 있고 기존 리소스에 대한 참조를 포함하는 경우 랩에서 사용할 수 없습니다.

   이에 대한 유일한 예외는 기존 가상 네트워크를 참조**할 수 있는** 것입니다. 

- Resource Manager 템플릿에서 생성된 랩 VM에서 수식을 만들 수 없습니다. 

- Resource Manager 템플릿에서 생성된 랩 VM에서 사용자 지정 이미지를 만들 수 없습니다. 

- Resource Manager 템플릿을 배포할 때 대부분의 정책이 평가되지 않습니다.

   예를 들어 사용자가 5개의 VM만 만들 수 있음을 지정하는 랩 정책을 가질 수 있습니다. 그러나 사용자는 수십 개의 VM을 만드는 Resource Manager 템플릿을 배포할 수 있습니다. 평가되지 않는 정책은 다음과 같습니다.

   - 사용자당 VM 수
   - 랩 사용자당 프리미엄 VM 수
   - 랩 사용자당 프리미엄 디스크 수


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>랩 사용자에 대한 환경 리소스 그룹 액세스 권한 구성

랩 사용자는 Resource Manager 템플릿을 배포할 수 있습니다. 하지만 기본적으로 읽기 권한자 액세스 권한을 갖습니다. 즉, 환경 리소스 그룹에서 리소스를 변경할 수 없습니다. 예를 들어 해당 리소스를 중지하거나 시작할 수 없습니다.

랩 사용자에게 참가자 액세스 권한을 제공하려면 다음 단계를 수행합니다. 그런 다음 Resource Manager 템플릿을 배포할 때 해당 환경에서 리소스를 편집할 수 있습니다. 


1. 랩의 **개요** 창에서 **구성 및 정책**을 선택합니다.
1. **랩 설정**을 선택합니다.
1. 랩 설정 창에서 **참가자**를 선택하여 랩 사용자에게 쓰기 권한을 부여합니다.

    ![랩 사용자 액세스 권한 구성](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계
* VM을 만든 후에는 해당 VM의 관리 창에서 **연결**을 선택하여 VM에 연결할 수 있습니다.
* 랩의 **내 가상 머신** 목록에서 환경을 선택하여 환경에서 리소스를 보고 관리합니다. 
* [Azure 빠른 시작 템플릿 갤러리의 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)(영문)을 탐색합니다.
