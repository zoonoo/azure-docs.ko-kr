---
title: PowerShell을 사용 하 여 Azure 이미지 작성기 서비스 권한 구성
description: PowerShell을 사용 하 여 권한 및 권한을 포함 하 여 Azure VM 이미지 작성기 서비스에 대 한 요구 사항 구성
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: cfe3efc77e065ac3685b72d0eab501034609b59b
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068264"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>PowerShell을 사용 하 여 Azure 이미지 작성기 서비스 권한 구성

Azure 이미지 작성기 서비스를 사용 하려면 이미지를 빌드하기 전에 권한 및 권한을 구성 해야 합니다. 다음 섹션에서는 PowerShell을 사용 하 여 가능한 시나리오를 구성 하는 방법에 대해 자세히 설명 합니다.

> [!IMPORTANT]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>기능 등록

먼저, Azure 이미지 작성기 서비스에 등록 해야 합니다. 등록은 스테이징 리소스 그룹을 만들고, 관리 하 고, 삭제할 수 있는 권한을 서비스에 부여 합니다. 또한이 서비스에는 이미지 빌드에 필요한 그룹에 리소스를 추가할 수 있는 권한이 있습니다.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure 사용자 할당 관리 id 만들기

Azure 이미지 작성기를 사용 하려면 [azure 사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 만들어야 합니다. Azure 이미지 작성기는 사용자 할당 관리 id를 사용 하 여 이미지를 읽고, 이미지를 쓰고, Azure storage 계정에 액세스 합니다. 구독에서 특정 작업을 수행할 수 있는 id 권한을 부여 합니다.

> [!NOTE]
> 이전에는 Azure 이미지 작성기에서 Azure 이미지 작성기 SPN (서비스 사용자 이름)을 사용 하 여 이미지 리소스 그룹에 대 한 사용 권한을 부여 했습니다. SPN을 사용 하는 것은 더 이상 사용 되지 않습니다. 사용자 할당 관리 id를 대신 사용 합니다.

다음 예제에서는 Azure 사용자 할당 관리 id를 만드는 방법을 보여 줍니다. 자리 표시자 설정을 대체 하 여 변수를 설정 합니다.

| 설정 | 설명 |
|---------|-------------|
| \<Resource group\> | 사용자 할당 관리 id를 만들 리소스 그룹입니다. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Azure 사용자 할당 id에 대 한 자세한 내용은 id를 만드는 방법에 대 한 [azure 사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 설명서를 참조 하세요.

## <a name="allow-image-builder-to-distribute-images"></a>이미지 작성기에서 이미지를 배포 하도록 허용

Azure 이미지 작성기에서 이미지 (관리 되는 이미지/공유 이미지 갤러리)를 배포 하는 경우 Azure 이미지 작성기 서비스는 이러한 리소스 그룹에 이미지를 삽입할 수 있도록 허용 해야 합니다. 필요한 사용 권한을 부여 하려면 사용자 할당 관리 id를 만들고 이미지가 작성 된 리소스 그룹에 대 한 권한을 부여 해야 합니다. Azure 이미지 작성기에는 구독의 다른 리소스 그룹에 있는 리소스에 액세스할 수 있는 권한이 **없습니다** . 빌드에 실패 하는 것을 방지 하려면 액세스를 허용 하기 위해 명시적인 작업을 수행 해야 합니다.

리소스 그룹에 대 한 사용자 할당 관리 id 참가자 권한을 부여 하지 않아도 이미지를 배포할 수 있습니다. 그러나 사용자 할당 관리 id의 경우 `Actions` 배포 리소스 그룹에서 다음 Azure 권한이 필요 합니다.

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

공유 이미지 갤러리에 배포 하는 경우 다음도 필요 합니다.

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>기존 이미지를 사용자 지정할 수 있는 권한

Azure 이미지 작성기가 원본 사용자 지정 이미지 (관리 되는 이미지/공유 이미지 갤러리)에서 이미지를 빌드하려면 Azure 이미지 작성기 서비스에서 이러한 리소스 그룹으로 이미지를 읽을 수 있어야 합니다. 필요한 사용 권한을 부여 하려면 사용자 할당 관리 id를 만들고 이미지가 있는 리소스 그룹에 대 한 권한을 부여 해야 합니다.

기존 사용자 지정 이미지에서 빌드:

```Actions
Microsoft.Compute/galleries/read
```

기존 공유 이미지 갤러리 버전에서 빌드:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Vnet 이미지를 사용자 지정할 수 있는 권한

Azure 이미지 작성기에는 구독에서 기존 VNET을 배포 하 고 사용 하 여 사용자 지정이 연결 된 리소스에 액세스할 수 있도록 허용 하는 기능이 있습니다.

리소스 그룹에 대 한 사용자 할당 관리 id 참가자 권한을 부여 하지 않아도 기존 VNET에 VM을 배포할 수 있습니다. 그러나 사용자 할당 관리 id에는 `Actions` VNET 리소스 그룹에 대 한 다음 Azure 권한이 필요 합니다.

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure 역할 정의 만들기

다음 예제에서는 이전 섹션에서 설명한 작업에서 Azure 역할 정의를 만듭니다. 예제는 리소스 그룹 수준에서 적용 됩니다. 사용자 요구 사항에 따라 예가 충분 한지 평가 하 고 테스트 합니다. 시나리오의 경우 특정 공유 이미지 갤러리에 구체화 해야 할 수 있습니다.

이미지 작업은 읽기 및 쓰기를 허용 합니다. 사용자 환경에 적합 한 항목을 결정 합니다. 예를 들어 Azure 이미지 작성기가 리소스 그룹 *예제-rg-1* 에서 이미지를 읽고 리소스 그룹에 이미지를 쓰도록 허용 하는 역할을 만듭니다 (예: *rg-2*).

### <a name="custom-image-azure-role-example"></a>사용자 지정 이미지 Azure 역할 예제

다음 예제에서는 원본 사용자 지정 이미지를 사용 하 고 배포 하는 Azure 역할을 만듭니다. 그런 다음 Azure 이미지 작성기의 사용자 할당 관리 id에 사용자 지정 역할을 부여 합니다.

예제의 값을 간단 하 게 바꾸기 위해 다음 변수를 먼저 설정 합니다. 자리 표시자 설정을 대체 하 여 변수를 설정 합니다.

| 설정 | 설명 |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | 사용자 지정 이미지에 대 한 리소스 그룹 |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>기존 VNET Azure 역할 예제

다음 예제에서는 기존 VNET 이미지를 사용 하 고 배포 하는 Azure 역할을 만듭니다. 그런 다음 Azure 이미지 작성기의 사용자 할당 관리 id에 사용자 지정 역할을 부여 합니다.

예제의 값을 간단 하 게 바꾸기 위해 다음 변수를 먼저 설정 합니다. 자리 표시자 설정을 대체 하 여 변수를 설정 합니다.

| 설정 | 설명 |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | VNET 리소스 그룹 |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 이미지 작성기 개요](image-builder-overview.md)를 참조 하세요.