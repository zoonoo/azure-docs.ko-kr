---
title: Azure DevTest 랩의 랩에 아티팩트 리포지토리 추가 | 마이크로 소프트 문서
description: Azure DevTest 랩에서 랩에 아티팩트 리포지토리를 추가하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: a0dbd92533703a56f1ec2478fab8944656129247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295503"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>개발자 테스트 연구소의 랩에 아티팩트 리포지토리 추가
DevTest Labs를 사용하면 VM을 만들 때 또는 VM을 만든 후 VM에 추가할 아티팩트를 지정할 수 있습니다. 이 아티팩트는 VM에 설치하려는 도구 또는 응용 프로그램일 수 있습니다. 아티팩트는 GitHub 또는 Azure DevOps Git 리포지토리에서 로드된 JSON 파일에 정의됩니다.

DevTest 연구소에서 관리하는 [공용 아티팩트 리포지토리는](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)Windows 및 Linux 모두에 대한 많은 공통 도구를 제공합니다. 이 리포지토리에 대한 링크가 랩에 자동으로 추가됩니다. 공용 아티팩트 리포지토리에서 사용할 수 없는 특정 도구를 사용하여 고유한 아티팩트 리포지토리를 만들 수 있습니다. 사용자 지정 아티팩트를 만드는 방법에 대해 알아보려면 [사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)를 참조하십시오.

이 문서에서는 Azure 포털, Azure 리소스 관리 템플릿 및 Azure PowerShell을 사용하여 사용자 지정 아티팩트 리포지토리를 추가하는 방법에 대한 정보를 제공합니다. PowerShell 또는 CLI 스크립트를 작성하여 랩에 아티팩트 리포지토리 추가를 자동화할 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항
랩에 리포지토리를 추가하려면 먼저 리포지토리에서 특정 정보를 가져옵니다. 다음 섹션에서는 **GitHub** 또는 **Azure DevOps에서**호스팅되는 리포지토리에 필요한 정보를 얻는 방법을 설명합니다.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub 리포지토리 복제 URL 및 개인 액세스 토큰 가져오기

1. 아티팩트 또는 Resource Manager 템플릿 정의를 포함하는 GitHub 리포지토리의 홈페이지로 이동합니다.
2. **복제 또는 다운로드**를 선택합니다.
3. URL을 클립보드에 복사하려면 **HTTPS 복제 url** 단추를 선택합니다. 나중에 사용할 수 있도록 URL을 저장합니다.
4. GitHub의 오른쪽 위에서 프로필 이미지를 선택한 다음 **설정**을 선택합니다.
5. 왼쪽의 **개인 설정** 메뉴에서 **개발자 설정을**선택합니다.
6. 왼쪽 메뉴에서 **개인 액세스 토큰을** 선택합니다.
7. **Generate new token**(새 토큰 생성)을 탭합니다.
8. **새 개인 액세스 토큰** 페이지의 **토큰 설명** 아래에 설명을 입력합니다. **범위 선택** 아래의 기본 항목에 동의한 다음 **토큰 생성**을 선택합니다.
9. 생성된 토큰을 저장합니다. 토큰은 나중에 사용합니다.
10. GitHub를 닫습니다.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos 복제 URL 및 개인 액세스 토큰 가져오기
1. 팀 컬렉션의 홈 페이지(예: `https://contoso-web-team.visualstudio.com`)로 이동한 다음 프로젝트를 선택합니다.
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
10. 리포지토리에 랩 연결 섹션을 계속 진행합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure 포털의 랩에 아티팩트 리포지토리를 추가하는 단계를 제공합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. **추가 서비스**를 선택한 후 서비스 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 랩을 선택합니다.
4. 왼쪽 메뉴에서 **구성 및 정책을** 선택합니다.
5. 왼쪽 메뉴의 **외부 리소스** 섹션에서 **리포지토리를** 선택합니다.
6. 도구 모음에 **+ 추가를** 선택합니다.

    ![리포지토리 추가 버튼](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. **리포지토리** 페이지에서 다음 정보를 지정합니다.
   1. **이름**. 리포지토리의 이름을 입력합니다.
   2. **Git Clone Url**. 이전에 GitHub 또는 Azure DevOps Services에서 복사한 Git HTTPS 복제 URL을 입력합니다.
   3. **분기**. 정의를 가져오려면 분기를 입력합니다.
   4. **개인 액세스 토큰**. 이전에 GitHub 또는 Azure DevOps Services에서 가져온 개인 액세스 토큰을 입력합니다.
   5. **폴더 경로**. 아티팩트 또는 Resource Manager 템플릿 정의가 포함된 복제 URL에 상대적인 폴더 경로를 하나 이상 입력합니다. 하위 디렉터리를 지정하는 경우 폴더 경로에 슬래시를 포함해야 합니다.

        ![리포지토리 영역](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **저장**을 선택합니다.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Azure 리소스 관리(Azure 리소스 관리자) 템플릿은 만들려는 Azure의 리소스를 설명하는 JSON 파일입니다. 이러한 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성을](../azure-resource-manager/templates/template-syntax.md)참조하십시오.

이 섹션에서는 Azure 리소스 관리자 템플릿을 사용하여 랩에 아티팩트 리포지토리를 추가하는 단계를 제공합니다.  템플릿이 아직 존재하지 않는 경우 랩을 만듭니다.

### <a name="template"></a>템플릿
이 문서에 사용된 샘플 템플릿은 매개 변수를 통해 다음 정보를 수집합니다. 대부분의 매개 변수에는 스마트 기본값이 있지만 지정해야 하는 몇 가지 값이 있습니다. 랩 이름, 아티팩트 리포지토리에 대한 URI 및 리포지토리에 대한 보안 토큰을 지정해야 합니다.

- 랩 이름입니다.
- DevTest Labs 사용자 인터페이스(UI)에서 아티팩트 리포지토리의 이름을 표시합니다. 기본값은 다음과 `Team Repository`입니다.
- 리포지토리에 대한 URI(예: `https://github.com/<myteam>/<nameofrepo>.git` 또는 `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- 아티팩트를 포함하는 리포지토리의 분기입니다. 기본값은 다음과 `master`입니다.
- 아티팩트가 포함된 폴더의 이름입니다. 기본값은 다음과 `/Artifacts`입니다.
- 저장소의 형식. 허용된 `VsoGit` 값은 또는 `GitHub`.
- 리포지토리에 대한 토큰에 액세스합니다.

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
템플릿을 Azure에 배포하고 리소스가 존재하지 않는 경우 또는 업데이트된 리소스가 있는 경우 여러 가지 방법으로 만들 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md)
- [리소스 관리자 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)
- [리소스 관리자 템플릿과 Azure 포털로 리소스 배포](../azure-resource-manager/templates/deploy-portal.md)
- [리소스 관리자 템플릿과 리소스 관리자 REST API로 리소스 배포](../azure-resource-manager/templates/deploy-rest.md)

PowerShell에서 템플릿을 배포하는 방법을 살펴보겠습니다. 템플릿을 배포하는 데 사용되는 Cmdlet은 컨텍스트에 따라 다르므로 현재 테넌트와 현재 구독이 사용됩니다. 필요한 경우 템플릿을 배포하기 전에 [Set-AzContext를](/powershell/module/az.accounts/set-azcontext) 사용하여 컨텍스트를 변경합니다.

먼저 [New-AzResourceGroup을](/powershell/module/az.resources/new-azresourcegroup)사용하여 리소스 그룹을 만듭니다. 사용하려는 리소스 그룹이 이미 있는 경우 이 단계를 건너뜁니다.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

다음으로 [New-AzResourceGroupDeploy](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 리소스 그룹에 배포를 만듭니다. 이 cmdlet은 리소스 변경 내용을 Azure에 적용합니다. 지정된 리소스 그룹에 여러 리소스 배포를 할 수 있습니다. 동일한 리소스 그룹에 여러 번 배포하는 경우 각 배포의 이름이 고유한지 확인합니다.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

New-AzResourceGroupDeployment가 성공적으로 실행되면 명령은 프로비저닝 상태(성공해야 있음)와 같은 중요한 정보와 템플릿의 출력을 출력합니다.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 랩에 아티팩트 리포지토리를 추가하는 데 사용할 수 있는 샘플 PowerShell 스크립트를 제공합니다. Azure PowerShell이 없는 경우 Azure [PowerShell을 설치하도록 구성하고 구성하는 방법을](/powershell/azure/overview?view=azps-1.2.0) 참조하세요.

### <a name="full-script"></a>전체 스크립트
다음은 자세한 메시지와 주석을 포함한 전체 스크립트입니다.

**새로운 데브테스트랩 유물 리포지토리.ps1**:

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

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

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
다음 예제에서는 스크립트를 실행하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주시면 됩니다.

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>매개 변수
이 문서의 샘플 PowerShell 스크립트는 다음과 같은 매개 변수를 사용합니다.

| 매개 변수 | 설명 |
| --------- | ----------- |
| 랩 네임 | 랩의 이름입니다. |
| 아티팩트리포지토리이름 | 새 아티팩트 리포지토리의 이름입니다. 스크립트는 지정되지 않은 경우 저장소에 대한 임의의 이름을 만듭니다. |
| 아티팩트리포지토리디스플레이네임 | 아티팩트 리포지토리의 이름을 표시합니다. 이 이름은 Azure Portal에 표시되는 이름입니다(랩의https://portal.azure.com) 모든 아티팩트 리포지토리를 볼 때). |
| 저장소우리 | 저장소에 Uri. 예: `https://github.com/<myteam>/<nameofrepo>.git` 또는 `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.|
| RepositoryBranch | 아티팩트 파일을 찾을 수 있는 분기입니다. 기본값은 '마스터'입니다. |
| FolderPath | 아티팩트를 찾을 수 있는 폴더입니다. 기본값은 '/아티팩트'입니다. |
| 개인 액세스 토큰 | GitHub 또는 VSOGit 리포지토리에 액세스하기 위한 보안 토큰입니다. 개인 액세스 토큰을 얻으려면 필수 구성 조건 섹션을 참조하십시오. |
| SourceType | 아티팩트가 VSOGit 인지 GitHub 리포지토리인지 여부입니다. |

리포지토리 자체에는 Azure 포털에 표시되는 표시 이름과 다른 ID를 위한 내부 이름이 필요합니다. Azure 포털을 사용 하 여 내부 이름이 표시 되지 않습니다 하지만 Azure REST API 또는 Azure PowerShell을 사용 하는 경우 표시 됩니다. 스크립트는 스크립트 사용자가 지정하지 않은 경우 이름을 제공합니다.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>스크립트에 사용되는 PowerShell 명령

| PowerShell 명령 | 메모 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 이 명령은 랩의 위치와 같은 세부 정보를 얻는 데 사용됩니다. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 아티팩트 리포지토리를 추가하기 위한 특정 명령은 없습니다. 일반 [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet이 작업을 수행합니다. 이 cmdlet에는 만들 리소스 유형을 알기 위해 **ResourceId** 또는 **ResourceName** 및 **ResourceType** 쌍이 필요합니다. 이 샘플 스크립트는 리소스 이름 및 리소스 유형 쌍을 사용합니다. <br/><br/>아티팩트 리포지토리 원본을 랩과 동일한 위치와 동일한 리소스 그룹에 만들고 있습니다.|

스크립트는 현재 구독에 새 리소스를 추가합니다. [Get-AzContext를](/powershell/module/az.accounts/get-azcontext) 사용하여 이 정보를 확인합니다. [Set-AzContext를](/powershell/module/az.accounts/set-azcontext) 사용하여 현재 테넌트 및 구독을 설정합니다.

리소스 이름 및 리소스 유형 정보를 검색하는 가장 좋은 방법은 [테스트 드라이브 Azure REST API](https://azure.github.io/projects/apis/) 웹 사이트를 사용하는 것입니다. [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) 제공업체를 확인하여 DevTest Labs 공급자에 사용할 수 있는 REST API를 확인하십시오. 스크립트 사용자는 다음 리소스 ID를 사용합니다.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

리소스 유형은 곱슬 대괄호에 나열된 항목을 제외하고 URI의 '공급자' 다음으로 나열된 모든 항목입니다. 리소스 이름은 곱슬 대괄호에 있는 모든 것입니다. 리소스 이름에 대해 두 개 이상의 항목이 필요한 경우 수행한 것처럼 각 항목을 슬래시로 구분합니다.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>다음 단계
- [Azure DevTest Labs의 랩에 대한 필수 아티팩트 지정](devtest-lab-mandatory-artifacts.md)
- [DevTest Labs 가상 머신에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)
- [랩에서 아티팩트 실패 진단](devtest-lab-troubleshoot-artifact-failure.md)
