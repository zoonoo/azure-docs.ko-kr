---
title: Windows VM용 이미지 갤러리에서 Azure 이미지 빌더 사용(미리 보기)
description: Azure 이미지 빌더 및 Azure PowerShell을 사용하여 Azure 공유 갤러리 이미지 버전을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263356"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>미리 보기: Windows 이미지를 만들고 공유 이미지 갤러리에 배포합니다. 

이 문서에서는 Azure 이미지 빌더 및 Azure PowerShell을 사용하여 [공유 이미지 갤러리에서](shared-image-galleries.md)이미지 버전을 만든 다음 이미지를 전역으로 배포하는 방법을 보여 주도록 합니다. [Azure CLI를](../linux/image-builder-gallery.md)사용하여 이 작업을 수행할 수도 있습니다.

.json 템플릿을 사용하여 이미지를 구성합니다. 우리가 사용하는 .json 파일은 여기 : [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). 템플릿의 로컬 버전을 다운로드하고 편집할 예정이므로 이 문서는 로컬 PowerShell 세션을 사용하여 작성됩니다.

공유 이미지 갤러리에 이미지를 배포하려면 템플릿은 [sharedImage를](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) 템플릿 `distribute` 섹션의 값으로 사용합니다.

Azure Image Builder는 이미지를 일반화하기 위해 sysprep을 자동으로 실행하며, 필요한 경우 [재정의할](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) 수 있는 일반 sysprep 명령입니다. 

사용자 지정을 계층화하는 횟수를 알고 있어야 합니다. 단일 Windows 이미지에서 최대 8회 Sysprep 명령을 실행할 수 있습니다. Sysprep을 8번 실행한 후 Windows 이미지를 다시 만들어야 합니다. 자세한 내용은 [Sysprep을 실행할 수 있는 횟수에 대한 제한을](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)참조하십시오. 

> [!IMPORTANT]
> Azure 이미지 빌더는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-features"></a>피처 등록
미리 보기 중에 Azure 이미지 빌더를 사용하려면 새 기능을 등록해야 합니다.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

피처 등록 상태를 확인합니다.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

다음 `RegistrationState` 단계로 `Registered` 이동하기 전에 때까지 기다립니다.

공급자 등록을 확인합니다. 각 반환 `Registered`을 확인합니다.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

반환하지 `Registered`않는 경우 다음을 사용하여 공급자를 등록합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>변수 만들기

일부 정보를 반복적으로 사용하므로 해당 정보를 저장하는 몇 가지 변수를 만듭니다. 변수의 값을 처럼 `username` 바꾸고 `vmpassword`.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들고 Azure Image Builder에 해당 리소스 그룹에서 리소스를 만들 수 있는 권한을 부여합니다.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Shared Image Gallery 만들기

공유 이미지 갤러리에서 이미지 빌더를 사용하려면 기존 이미지 갤러리 와 이미지 정의가 있어야 합니다. 이미지 빌더는 이미지 갤러리 및 이미지 정의를 만들지 않습니다.

사용할 갤러리 및 이미지 정의가 아직 없는 경우 먼저 갤러리를 만듭니다. 먼저 이미지 갤러리를 만듭니다.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>템플릿 다운로드 및 구성

.json 템플릿을 다운로드하여 변수로 구성합니다.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>이미지 버전 만들기

템플릿을 서비스에 제출해야 하며 스크립트와 같은 종속 아티팩트를 다운로드하여 *IT_* 접두번으로 고정된 스테이징 리소스 그룹에 저장합니다.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

이미지를 빌드하려면 템플릿에서 '실행'을 호출해야합니다.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

이미지를 만들고 두 리전에 복제하는 데 시간이 걸릴 수 있습니다. VM 을 만들기 로 이동하기 전에 이 부품이 완료될 때까지 기다립니다.

이미지 빌드 상태를 가져오는 자동화 옵션에 대한 자세한 내용은 GitHub에서 이 템플릿에 대한 [Readme를](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) 참조하십시오.


## <a name="create-the-vm"></a>VM 만들기

Azure 이미지 빌더에서 만든 이미지 버전에서 VM을 만듭니다.

만든 이미지 버전을 가져옵니다.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

이미지가 복제된 두 번째 영역에서 VM을 만듭니다.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>사용자 지정 확인
VM을 만들 때 설정한 사용자 이름과 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내부에서 cmd 프롬프트를 열고 다음을 입력합니다.

```console
dir c:\
```

이미지 사용자 지정 중에 `buildActions` 만들어진 디렉터리이름이 표시됩니다.


## <a name="clean-up-resources"></a>리소스 정리
이제 이미지 버전을 다시 사용자 지정하여 동일한 이미지의 새 버전을 만들려면 **이 단계를 건너뛰고** [Azure 이미지 빌더 를 사용하여 다른 이미지 버전을 만듭니다.](image-builder-gallery-update-image-version.md)


그러면 생성된 이미지가 다른 모든 리소스 파일과 함께 삭제됩니다. 리소스를 삭제하기 전에 이 배포를 완료했는지 확인합니다.

리소스 그룹 템플릿을 먼저 삭제하면 AIB에서 사용하는 준비 리소스*그룹(IT_)이*정리되지 않습니다.

이미지 템플릿의 ResourceID를 가져옵니다. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

이미지 템플릿을 삭제합니다.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

리소스 그룹을 삭제합니다.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

만든 이미지 버전을 업데이트하는 방법을 알아보려면 [Azure 이미지 빌더 사용을 참조하여 다른 이미지 버전을 만듭니다.](image-builder-gallery-update-image-version.md)
