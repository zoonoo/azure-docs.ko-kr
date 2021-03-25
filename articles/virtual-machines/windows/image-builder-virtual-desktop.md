---
title: 이미지 작성기-Windows 가상 데스크톱 이미지 만들기
description: PowerShell에서 Azure 이미지 작성기를 사용 하 여 Windows 가상 데스크톱의 Azure VM 이미지를 만듭니다.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 69718b219d239ac13e5d932b05a7dd29619adaa3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045589"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Azure VM 이미지 작성기 및 PowerShell을 사용 하 여 Windows 가상 데스크톱 이미지 만들기

이 문서에서는 다음과 같은 사용자 지정으로 Windows 가상 데스크톱 이미지를 만드는 방법을 보여 줍니다.

* [Fslogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1)를 설치 하 고 있습니다.
* 커뮤니티 리포지토리에서 [Windows 가상 데스크톱 최적화 스크립트](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) 를 실행 합니다.
* [Microsoft 팀](../../virtual-desktop/teams-on-wvd.md)을 설치 합니다.
* [다시 시작](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* [Windows 업데이트](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer) 실행

Azure VM 이미지 작성기를 사용 하 여이를 자동화 하 고, [공유 이미지 갤러리](../shared-image-galleries.md)에 이미지를 배포 하 여 다른 지역에 복제 하 고, 크기를 제어 하 고, 조직 내부 및 외부에서 이미지를 공유할 수 있는 방법을 보여 줍니다.


이미지 작성기 구성의 배포를 간소화 하기 위해이 예제에서는 안에 중첩 된 이미지 작성기 템플릿이 있는 Azure Resource Manager 템플릿을 사용 합니다. 이를 통해 변수 및 매개 변수 입력과 같은 다른 이점이 있습니다. 명령줄에서 매개 변수를 전달할 수도 있습니다.

이 문서는 복사 및 붙여넣기 연습을 위한 것입니다.

> [!NOTE]
> 앱을 설치 하는 스크립트는 [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD)에 있습니다. 이는 설명 및 테스트 전용 이며 프로덕션 워크 로드에는 적합 하지 않습니다. 

## <a name="tips-for-building-windows-images"></a>Windows 이미지를 빌드하기 위한 팁 

- VM 크기-기본 VM 크기는 `Standard_D1_v2` Windows에 적합 하지 않은입니다. 이상을 사용 `Standard_D2_v2` 합니다.
- 이 예제에서는 [PowerShell 사용자 지정자 스크립트](../linux/image-builder-json.md)를 사용 합니다. 이러한 설정을 사용 해야 합니다. 그렇지 않으면 빌드가 응답 하지 않습니다.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    예를 들어:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- 코드 주석-AIB 빌드 로그 (사용자 지정. 로그)는 매우 자세한 정보를 표시 합니다. ' 쓰기-호스트 '를 사용 하 여 스크립트를 주석으로 처리 하면 로그에 전송 되 고 문제 해결이 쉬워집니다.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- 종료 코드-AIB는 모든 스크립트에서 0 종료 코드를 반환 하는 것으로 예상 합니다. 0이 아닌 종료 코드는 AIB 사용자 지정이 실패 하 고 빌드를 중지 합니다. 복잡 한 스크립트가 있는 경우 계측을 추가 하 고 종료 코드를 내보내면 사용자 지정 .log에 표시 됩니다.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- 테스트: 독립 실행형 VM에서 코드를 테스트 및 테스트 하 고, 사용자 프롬프트가 없는지, 올바른 권한을 사용 하 고 있는지 확인 하세요.

- 네트워킹- `Set-NetAdapterAdvancedProperty` . 이는 최적화 스크립트에서 설정 되지만 네트워크 연결을 끊을 때 AIB 빌드에 실패 합니다 .이는 주석 처리 된 것입니다. 조사 중에 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

최신 Azure PowerShell Cmdlet이 설치 되어 있어야 합니다. 설치 세부 정보는 [여기](/powershell/azure/overview) 를 참조 하세요.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>환경 및 변수 설정

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>권한, 사용자 id 및 역할 


 사용자 id를 만듭니다.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

이미지를 배포 하기 위한 id에 사용 권한을 할당 합니다. 이 명령은 앞에서 지정한 매개 변수를 사용 하 여 템플릿을 다운로드 하 고 업데이트 합니다.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> 이 오류가 표시 되는 경우: ' AzRoleDefinition: 역할 정의 한도가 초과 되었습니다. 더 이상 역할 정의를 만들 수 없습니다. ' 문제를 해결 하려면이 문서를 참조 하세요 https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Shared Image Gallery 만들기 

공유 이미지 갤러리가 아직 없는 경우 새로 만들어야 합니다.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>이미지 템플릿 구성

이 예제에서는 앞에서 지정한 매개 변수를 사용 하 여 템플릿을 다운로드 하 고 업데이트 하는 템플릿을 준비 하 고, FsLogix, OS 최적화, Microsoft 팀을 설치 하 고, 끝에 Windows 업데이트를 실행 합니다.

사용할 이미지를 원본 속성에서 볼 수 있습니다 .이 예제에서는 Win 10 다중 세션 이미지를 사용 합니다. 

### <a name="windows-10-images"></a>Windows 10 이미지
사용자가 알아야 하는 두 가지 주요 유형은 다중 세션 및 단일 세션입니다.

다중 세션 이미지는 풀링된 사용을 위한 것입니다. Azure의 이미지 세부 정보에 대 한 예제는 다음과 같습니다.

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

단일 세션 이미지는 개별 사용을 위한 것입니다. Azure의 이미지 세부 정보에 대 한 예제는 다음과 같습니다.

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

사용 가능한 Win10 이미지를 변경할 수도 있습니다.

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>템플릿 다운로드 및 구성

이제 템플릿을 다운로드 하 고 사용을 위해 구성 해야 합니다.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

언제 든 지 자유롭게 [템플릿을](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)볼 수 있으며 모든 코드를 볼 수 있습니다.


## <a name="submit-the-template"></a>템플릿 제출

템플릿을 서비스에 제출 해야 합니다. 그러면 모든 종속 아티팩트 (예: 스크립트)를 다운로드 하 고, 유효성을 검사 하 고, 사용 권한을 확인 하 고, 준비 리소스 그룹 (접두, *IT_* 에 저장 합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>이미지 빌드
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> 이 명령은 이미지 작성기 서비스가 이미지 빌드를 완료할 때까지 기다리지 않으며 아래 상태를 쿼리할 수 있습니다.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>VM 만들기
이제 빌드가 완료 되었으므로 이미지에서 VM을 빌드할 수 있습니다. [여기](/powershell/module/az.compute/new-azvm#examples)의 예제를 사용 합니다.

## <a name="clean-up"></a>정리

리소스 그룹 템플릿을 삭제 한 후에는 전체 리소스 그룹을 삭제 하면 안 됩니다. 그렇지 않으면 AIB에서 사용 하는 스테이징 리소스 그룹 (*IT_*)이 정리 되지 않습니다.

이미지 템플릿을 제거 합니다.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

역할 할당을 삭제 합니다.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

해당 리소스 그룹을 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

[GitHub에서](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)더 많은 예제를 시험해 볼 수 있습니다.