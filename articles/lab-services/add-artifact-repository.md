---
title: Azure DevTest Labs에서 랩에 아티팩트 리포지토리 추가 | Microsoft Docs
description: Azure DevTest labs에서 랩에 아티팩트 리포지토리를 추가 하는 방법에 알아봅니다.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c1e74efa9cf99e8510ea17aedc840ce3b0731c3b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916671"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>DevTest Labs에서 랩에 아티팩트 리포지토리 추가
DevTest Labs에서 VM 만들기 또는 VM을 만든 후에 VM에 추가할 아티팩트를 지정할 수 있습니다. 이 아티팩트 도구 또는 VM에 설치 하려는 응용 프로그램 일 수 있습니다. 아티팩트는 GitHub 또는 VSTS Git 리포지토리에서 로드 된 JSON 파일에 정의 됩니다. 

합니다 [공용 아티팩트 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)DevTest Labs에서 유지 관리, Windows 및 Linux에 대 한 여러 공통 도구를 제공 합니다. 이 리포지토리에 대 한 링크를 랩에 자동으로 추가 됩니다. 공용 아티팩트 리포지토리에서 사용할 수 없는 특정 도구를 사용 하 여 사용자 고유의 아티팩트 리포지토리를 만들 수 있습니다. 사용자 지정 아티팩트 만들기에 대 한 자세한 참조 [사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)합니다.

이 문서에서는 Azure portal, Azure Resource Management 템플릿 및 Azure PowerShell을 사용 하 여 사용자 지정 아티팩트 리포지토리를 추가 하는 방법을 설명 합니다. PowerShell 또는 CLI 스크립트를 작성 하 여 랩에 아티팩트 리포지토리 추가 자동화할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건
랩에 리포지토리를 추가하려면 먼저 리포지토리에서 특정 정보를 가져옵니다. 다음 섹션에서 호스팅되는 리포지토리에 대 한 필수 정보를 가져올 하는 방법에 설명 **GitHub** 하거나 **Azure DevOps**합니다.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub 리포지토리 복제 URL 및 개인 액세스 토큰 가져오기

1. 아티팩트 또는 Resource Manager 템플릿 정의를 포함하는 GitHub 리포지토리의 홈페이지로 이동합니다.
2. **복제 또는 다운로드**를 선택합니다.
3. URL을 클립보드에 복사하려면 **HTTPS 복제 url** 단추를 선택합니다. 나중에 사용할 수 있도록 URL을 저장합니다.
4. GitHub의 오른쪽 위에서 프로필 이미지를 선택한 다음 **설정**을 선택합니다.
5. 에 **개인 설정** 왼쪽에서 선택 메뉴 **개발자 설정을**합니다.
6. 선택 **개인용 액세스 토큰** 왼쪽된 메뉴에서.
7. **Generate new token**(새 토큰 생성)을 탭합니다.
8. **새 개인 액세스 토큰** 페이지의 **토큰 설명** 아래에 설명을 입력합니다. **범위 선택** 아래의 기본 항목에 동의한 다음 **토큰 생성**을 선택합니다.
9. 생성된 토큰을 저장합니다. 토큰은 나중에 사용합니다.
10. GitHub를 닫습니다.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos 복제 URL 및 개인 액세스 토큰 가져오기
1. 팀 컬렉션의 홈 페이지(예: https://contoso-web-team.visualstudio.com) )로 이동 한 다음 프로젝트를 선택합니다.
2. 프로젝트 홈 페이지에서 선택 **코드**합니다.
3. 복제 URL을 보려면 프로젝트 **코드** 페이지에서 **복제**를 선택합니다.
4. URL을 저장합니다. URL은 나중에 사용합니다.
5. 개인 액세스 토큰을 만들려면 사용자 계정 드롭다운 메뉴에서 **내 프로필**을 선택합니다.
6. 프로필 정보 페이지에서 **보안**을 탭합니다.
7. **보안** 탭에서 **추가**를 선택합니다.
8. **개인 액세스 토큰 만들기** 페이지에서:
   1. 토큰에 대한 **설명** 을 입력합니다.
   2. **다음 기간 내에 만료** 목록에서 **180일**을 선택합니다.
   3. **계정** 목록에서 **액세스 가능한 모든 계정**을 선택합니다.
   4. **모든 범위** 옵션을 선택합니다.
   5. **토큰 만들기**를 선택합니다.
9. 새 토큰이 **개인 액세스 토큰** 목록에 표시됩니다. **토큰 복사**를 선택하고 나중에 사용할 수 있게 토큰 값을 저장합니다.
10. 랩의 저장소 섹션에 연결을 계속 합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure portal에서 랩에 아티팩트 리포지토리를 추가 하는 단계를 제공 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **추가 서비스**를 선택한 후 서비스 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 랩을 선택합니다. 
4. 선택 **구성 및 정책** 왼쪽된 메뉴에서.
5. 선택 **리포지토리** 아래에서 **외부 리소스** 왼쪽 메뉴의 섹션입니다.
6. 선택 **+ 추가** 도구 모음에서 합니다.

    ![리포지토리 추가 버튼](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 에 **리포지토리** 페이지에서 다음 정보를 지정 합니다.
   1. **이름**. 리포지토리의 이름을 입력합니다.
   2. **Git Clone Url**. 이전에 GitHub 또는 Azure DevOps Services에서 복사한 Git HTTPS 복제 URL을 입력합니다.
   3. **분기**. 정의를 가져오려면 분기를 입력합니다.
   4. **개인 액세스 토큰**. 이전에 GitHub 또는 Azure DevOps Services에서 가져온 개인 액세스 토큰을 입력합니다.
   5. **폴더 경로**. 아티팩트 또는 Resource Manager 템플릿 정의가 포함된 복제 URL에 상대적인 폴더 경로를 하나 이상 입력합니다. 하위 디렉터리를 지정하는 경우 폴더 경로에 슬래시를 포함해야 합니다.

        ![리포지토리 영역](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **저장**을 선택합니다.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Azure 리소스 관리 (Azure Resource Manager) 템플릿 JSON 파일을 만들려는 Azure에서에서 리소스를 설명 하는 경우 이러한 템플릿에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)합니다.

이 섹션에서는 Azure Resource Manager 템플릿을 사용 하 여 랩에 아티팩트 리포지토리를 추가 하는 단계를 제공 합니다.  이미 존재 하지 않는 경우 템플릿은 랩을 만듭니다. 

### <a name="template"></a>Template
이 문서에 사용 되는 샘플 템플릿은 매개 변수를 통해 다음 정보를 수집 합니다. 대부분의 매개 변수는 스마트 기본값을 갖지도 지정 해야 하는 몇 개의 값 있습니다. 랩 이름, 아티팩트 저장소에 대 한 URI 및 리포지토리에 대 한 보안 토큰을 지정 해야 합니다. 

- 랩 이름입니다.
- DevTest Labs 사용자 인터페이스 (UI)에서 아티팩트 리포지토리에 대 한 표시 이름입니다. 기본값은: `Team Repository`합니다.
- 저장소에 대 한 URI (예: `https://github.com/<myteam>/<nameofrepo>.git` 또는 `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`합니다.
- 아티팩트를 포함 하는 리포지토리에 분기입니다. 기본값은: `master`합니다.
- 아티팩트를 포함 하는 폴더의 이름입니다. 기본값은: `/Artifacts`합니다.
- 저장소의 형식입니다. 허용 되는 값은 `VsoGit` 또는 `GitHub`합니다.
- 리포지토리에 대 한 액세스 토큰입니다. 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>템플릿 배포
없는 경우 Azure에 템플릿을 배포할 수 있고 존재 하지 않는 경우 생성 되거나 업데이트 된 리소스를 몇 가지가 있습니다. 자세한 내용은 다음 문서를 참조 합니다.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Resource Manager 템플릿과 Azure Portal로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Resource Manager 템플릿과 Resource Manager REST API로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-rest.md)

계속 해 서 PowerShell에서 템플릿을 배포 하는 방법을 참조 하세요. 템플릿을 배포 하는 데 사용 되는 Cmdlet 상황에 맞는 되므로 현재 테 넌 트 및 현재 구독 사용 됩니다. 사용 하 여 [집합 AzContext](/powershell/module/az.accounts/set-azcontext) 컨텍스트를 변경 하려면 필요한 경우 템플릿을 배포 하기 전에 합니다.

먼저 사용 하 여 리소스 그룹을 만듭니다 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다. 이미 사용 하려는 리소스 그룹이 있는 경우이 단계를 건너뜁니다.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

다음으로 사용 하 여 리소스 그룹 배포를 만듭니다 [새로 만들기-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)합니다. 이 cmdlet은 Azure 리소스 변경 내용을 적용 합니다. 지정 된 리소스 그룹에 여러 리소스 배포를 만들 수 있습니다. 동일한 리소스 그룹에 여러 번을 배포 하는 경우 각 배포의 이름은 고유 해야 합니다.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

새로 만들기-AzResourceGroupDeployment 성공적으로 실행 한 후 명령을 프로 비전 상태 (성공 이어야 합니다) 등 템플릿에 대 한 출력 중요 한 정보를 출력 합니다.
 
## <a name="use-azure-powershell"></a>Azure PowerShell 사용 
이 섹션에서는 랩에 아티팩트 리포지토리를 추가할 수 있는 샘플 PowerShell 스크립트를 제공 합니다. Azure PowerShell가 없는 경우 [Azure PowerShell 설치 및 구성 하는 방법을](/powershell/azure/overview?view=azps-1.2.0) 설치 하는 자세한 지침에 대 한 합니다.

### <a name="full-script"></a>전체 스크립트
몇 가지 자세한 정보 메시지 및 주석 등 전체 스크립트는 다음과 같습니다.

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>PowerShell 스크립트 실행
다음 예제에서는 스크립트를 실행 하는 방법을 보여 줍니다. 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>매개 변수
이 문서의 샘플 PowerShell 스크립트는 다음 매개 변수를 사용합니다.

| 매개 변수 | 설명 | 
| --------- | ----------- | 
| LabName | 랩의 이름입니다. |
| ArtifactRepositoryName | 새 아티팩트 리포지토리의 이름입니다. 스크립트를 지정 하지 않으면 리포지토리에 대 한 임의 이름을 만듭니다. |
| ArtifactRepositoryDisplayName | 아티팩트 리포지토리에 대 한 표시 이름입니다. Azure portal에서 표시 되는 이름입니다 (https://portal.azure.com) 랩에 대 한 모든 아티팩트 리포지토리를 볼 때. |
| RepositoryUri | 저장소에 대 한 Uri입니다. 예: `https://github.com/<myteam>/<nameofrepo>.git` 또는 `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`합니다.| 
| RepositoryBranch | 분기는 아티팩트에 파일을 찾을 수 있습니다. 'Master' 기본값으로 사용 됩니다. | 
| FolderPath | 폴더는 아티팩트를 찾을 수 있습니다. 기본값은 ' / 아티팩트 |
| PersonalAccessToken | GitHub 또는 VSOGit 저장소에 액세스 하기 위한 보안 토큰입니다. 개인 액세스 토큰을 가져오는 지침에 대 한 전제 조건 섹션을 참조 하세요 |
| SourceType | 아티팩트 리포지토리 VSOGit 또는 GitHub 인지 여부입니다. |

리포지토리 자체 필요한 내부 이름을 다른 id에 대 한 Azure portal에서 표시 되는 표시 이름입니다. Azure portal을 사용 하 여 내부 이름을 보이지 있지만 Azure REST Api 또는 Azure PowerShell을 사용 하는 경우 표시 됩니다. 스크립트는 스크립트의 사용자가 지정 되지 않은 경우 이름을 제공 합니다.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>스크립트에 사용 되는 PowerShell 명령

| PowerShell 명령 | 메모 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 이 명령은 해당 위치와 같은 랩에 대 한 정보를 가져오는 데 사용 됩니다. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 아티팩트 리포지토리 추가 대 한 특정 명령이 없기 때문입니다. 제네릭 [새로 만들기-AzResource](/powershell/module/az.resources/new-azresource) cmdlet는 작업을 수행 합니다. 이 cmdlet은 필요한를 **ResourceId** 또는 **ResourceName** 하 고 **ResourceType** 쌍 만들려는 리소스의 종류를 알아야 합니다. 이 샘플 스크립트는 리소스 이름 및 리소스 형식 쌍을 사용합니다. <br/><br/>랩으로 동일한 리소스 그룹 및 동일한 위치에서 아티팩트 리포지토리의 소스를 만드는 것을 확인 합니다.|

스크립트는 현재 구독에 새 리소스를 추가합니다. 사용 하 여 [Get AzContext](/powershell/module/az.accounts/get-azcontext) 이 정보를 확인 합니다. 사용 하 여 [집합 AzContext](/powershell/module/az.accounts/set-azcontext) 현재 테 넌 트 및 구독을 설정할 수 있습니다.

리소스 이름 및 리소스 형식 정보를 검색 하는 가장 좋은 방법은 사용 하는 것은 [테스트 드라이브 Azure REST Api](https://azure.github.io/projects/apis/) 웹 사이트입니다. 체크 아웃 합니다 [DevTest Labs – 15-05-2016](https://aka.ms/dtlrestapis) DevTest Labs 공급자에 대 한 사용 가능한 REST Api를 참조 하는 공급자입니다. 스크립트 사용자는 다음 리소스 id입니다. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
리소스 유형 '공급자' uri에서 중괄호에 있는 항목을 제외 하 고 뒤에 나열 된 모든 항목입니다. 리소스 이름은 중괄호 나온 모든 항목입니다. 리소스 이름에 항목이 둘 이상 필요한 경우와 같이 각 항목에 슬래시를 구분 합니다. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>다음 단계
- [Azure DevTest Labs에서 랩에 대 한 필수 아티팩트를 지정 합니다.](devtest-lab-mandatory-artifacts.md)
- [DevTest Labs 가상 머신에 대 한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)
- [랩에서 아티팩트 실패 진단](devtest-lab-troubleshoot-artifact-failure.md)

