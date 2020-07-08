---
title: PowerShell을 사용 하 여 Azure 이미지 작성기를 사용 하 여 Windows VM 만들기
description: Azure 이미지 작성기 PowerShell 모듈을 사용 하 여 Windows VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: c8a5e1b1324ca49d8b540998a82ebf125b3c5364
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84975863"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>미리 보기: PowerShell을 사용 하 여 Azure 이미지 작성기를 사용 하 여 Windows VM 만들기

이 문서에서는 Azure VM 이미지 작성기 PowerShell 모듈을 사용 하 여 사용자 지정 된 Windows 이미지를 만드는 방법을 보여 줍니다.

> [!CAUTION]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 됩니다. 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> **Az builder** 및 **az. ManagedServiceIdentity** PowerShell 모듈은 미리 보기 상태 이지만 `Install-Module` 매개 변수와 함께 cmdlet을 사용 하 여 별도로 설치 해야 합니다 `AllowPrerelease` . 이러한 PowerShell 모듈은 일반적으로 사용할 수 있게 되 면 이후 Az PowerShell module 릴리스의 일부가 되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) cmdlet을 사용 하 여 특정 구독을 선택 합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>기능 등록

미리 보기 중에 Azure 이미지 작성기를 처음 사용 하는 경우 새 **VirtualMachineTemplatePreview** 기능을 등록 합니다.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

기능 등록 상태를 확인합니다.

> [!NOTE]
> 로 변경 하기 전에 **Registrationstate** 는 `Registering` 몇 분 동안 상태일 수 있습니다 `Registered` . 계속 하기 전에 상태가 **등록** 될 때까지 기다립니다.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

아직 등록 되지 않은 경우 Azure 구독에 사용할 다음 리소스 공급자를 등록 합니다.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>변수 정의

여러 가지 정보를 반복 해 서 사용 하 게 됩니다. 정보를 저장할 변수를 만듭니다.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Azure 구독 ID에 대 한 변수를 만듭니다. `subscriptionID`변수에 구독 ID가 포함 되어 있는지 확인 하려면 다음 예제에서 두 번째 줄을 실행할 수 있습니다.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예에서는 `$imageResourceGroup` 변수에 지정 된 지역에 있는 변수의 이름을 기반으로 하 여 리소스 그룹을 만듭니다 `$location` . 이 리소스 그룹은 이미지 구성 템플릿 아티팩트와 이미지를 저장 하는 데 사용 됩니다.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>사용자 id 만들기 및 역할 권한 설정

다음 예제를 사용 하 여 지정 된 리소스 그룹에서 이미지를 만들 수 있는 Azure 이미지 작성기 권한을 부여 합니다. 이 권한이 없으면 이미지 빌드 프로세스가 성공적으로 완료 되지 않습니다.

역할 정의 및 id 이름에 대 한 변수를 만듭니다. 이 기본값은 고유해야 합니다.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

사용자 id를 만듭니다.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

변수에 id 리소스 및 보안 주체 Id를 저장 합니다.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>ID에서 이미지를 배포하기 위한 권한 할당

Json 구성 파일을 다운로드 하 고이 문서에 정의 된 설정에 따라 수정 합니다.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

역할 정의를 만듭니다.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

이미지 작성기 서비스 주체에 역할 정의를 부여 합니다.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> "_AzRoleDefinition: 역할 정의 제한이 초과 되었습니다. 더 이상 역할 정의를 만들 수 없습니다._" [Azure RBAC 문제 해결](https://docs.microsoft.com/azure/role-based-access-control/troubleshooting)을 참조 하세요.

## <a name="create-a-shared-image-gallery"></a>Shared Image Gallery 만들기

갤러리를 만듭니다.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

갤러리 정의를 만듭니다.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>이미지 만들기

Azure 이미지 작성기 원본 개체를 만듭니다. 유효한 매개 변수 값 [은 Azure PowerShell를 사용 하 여 Azure Marketplace에서 WINDOWS VM 이미지 찾기](https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage) 를 참조 하세요.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Azure 이미지 작성기 배포자 개체를 만듭니다.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Azure 이미지 작성기 사용자 지정 개체를 만듭니다.

```azurepowershell-interactive
$ImgCustomParams = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer = New-AzImageBuilderCustomizerObject @ImgCustomParams
```

Azure 이미지 작성기 템플릿을 만듭니다.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

완료 되 면 메시지가 반환 되 고에 이미지 작성기 구성 템플릿이 생성 됩니다 `$imageResourceGroup` .

템플릿 생성 프로세스가 성공 했는지 확인 하기 위해 다음 예제를 사용할 수 있습니다.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

배경에서 이미지 작성기는 구독에 준비 리소스 그룹도 만듭니다. 이 리소스 그룹은 이미지 빌드에 사용 됩니다. 형식 `IT_<DestinationResourceGroup>_<TemplateName>` 입니다.

> [!WARNING]
> 스테이징 리소스 그룹을 직접 삭제 하지 마십시오. 이미지 템플릿 아티팩트를 삭제 하면 준비 리소스 그룹이 삭제 됩니다.

이미지 구성 템플릿 전송 중에 서비스에서 오류를 보고 하면 다음을 수행 합니다.

- [AZURE VM 이미지 빌드 (AIB) 오류 문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)을 참조 하세요.
- 다음 예제를 사용 하 여 템플릿을 삭제 한 후 다시 시도 하십시오.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>이미지 빌드를 시작 합니다.

이미지 구성을 VM 이미지 빌더 서비스에 제출 합니다.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

이미지 빌드 프로세스가 완료 될 때까지 기다립니다. 이 단계는 최대 한 시간 정도 걸릴 수 있습니다.

오류가 발생 하는 경우 [AZURE AIB (VM 이미지 빌드) 오류 문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)을 검토 하세요.

## <a name="create-a-vm"></a>VM 만들기

변수에 VM에 대 한 로그인 자격 증명을 저장 합니다. 암호는 복잡 해야 합니다.

```azurepowershell-interactive
$Cred = Get-Credential
```

만든 이미지를 사용 하 여 VM을 만듭니다.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customization"></a>사용자 지정 확인

VM을 만들 때 설정한 사용자 이름 및 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내에서 PowerShell을 열고 `Get-Content` 다음 예제와 같이를 실행 합니다.

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

이미지 사용자 지정 프로세스 중에 만들어진 파일의 내용에 따라 출력이 표시 됩니다.

```Output
Azure-Image-Builder-Was-Here
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 필요 하지 않은 경우 다음 예제를 실행 하 여 삭제할 수 있습니다.

### <a name="delete-the-image-builder-template"></a>이미지 작성기 템플릿 삭제

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>이미지 리소스 그룹을 삭제 합니다.

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 이 문서의 범위를 벗어난 리소스가 지정 된 리소스 그룹에 있는 경우에도 삭제 됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에 사용 된 json 파일의 구성 요소에 대해 자세히 알아보려면 [이미지 작성기 템플릿 참조](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.
