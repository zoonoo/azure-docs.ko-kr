---
title: 기존 VNET (미리 보기)을 사용 하 여 Azure 이미지 작성기를 사용 하 여 Windows VM 만들기
description: 기존 VNET을 사용 하 여 Azure 이미지 작성기를 사용 하 여 Windows VM 만들기
author: cynthn
ms.author: cynthn
ms.date: 05/29/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: 07339ea6c53b2abe959e8e0f164412e502bb06b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88068190"
---
# <a name="use-azure-image-builder-for-windows-vms-allowing-access-to-an-existing-azure-vnet"></a>Windows Vm 용 Azure 이미지 작성기를 사용 하 여 기존 Azure VNET에 액세스 허용

이 문서에서는 Azure 이미지 작성기를 사용 하 여 VNET의 기존 리소스에 대 한 액세스 권한이 있는 기본 사용자 지정 Windows 이미지를 만드는 방법을 보여 줍니다. 만든 빌드 VM은 구독에서 지정 하는 새 VNET 또는 기존 VNET에 배포 됩니다. 기존 Azure VNET을 사용 하는 경우 Azure 이미지 작성기 서비스는 공용 네트워크 연결이 필요 하지 않습니다.

> [!IMPORTANT]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>기능 등록

먼저, Azure 이미지 작성기 서비스에 등록 해야 합니다. 등록은 스테이징 리소스 그룹을 만들고, 관리 하 고, 삭제할 수 있는 권한을 서비스에 부여 합니다. 또한이 서비스에는 이미지 빌드에 필요한 그룹에 리소스를 추가할 수 있는 권한이 있습니다.

```powershell-interactive
# Register for Azure Image Builder Feature

Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'
```
## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정 

일부 정보를 반복 해 서 사용 하 게 됩니다. 해당 정보를 저장할 변수를 만듭니다.

```powershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="aibImageRG"

# location (see possible locations in main docs)
$location="westus2"

## if you need to change your subscription: Get-AzSubscription / Select-AzSubscription -SubscriptionName 

# get subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# name of the image to be created
$imageName="win2019image01"

# image distribution metadata reference name
$runOutputName="win2019ManImg02ro"

# image template name
$imageTemplateName="window2019VnetTemplate03"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="winSvrSigR01"

# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
$vnetName="myexistingvnet01"
# subnet name
$subnetName="subnet01"
# VNET resource group name
$vnetRgName="existingVnetRG"
# Existing Subnet NSG Name or the demo will create it
$nsgName="aibdemoNsg"
# NOTE! The VNET must always be in the same region as the AIB service region.
```

리소스 그룹을 만듭니다.

```powershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="configure-networking"></a>네트워킹 구성

기존 VNET\Subnet\NSG 없는 경우 다음 스크립트를 사용 하 여 새로 만듭니다.

```powershell-interactive
New-AzResourceGroup -Name $vnetRgName -Location $location

## Create base NSG to simulate an existing NSG
New-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName -location $location

$nsg = Get-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName 

$subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.1.0/24" -PrivateLinkServiceNetworkPoliciesFlag "Disabled" -NetworkSecurityGroup $nsg

New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $vnetRgName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $subnet

## NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>네트워크 보안 그룹 규칙 추가

이 규칙은 Azure 이미지 작성기 부하 분산 장치에서 프록시 VM으로의 연결을 허용 합니다. 포트 60001은 Linux OSs 용 이며 포트 6만는 Windows OSs 용입니다. 프록시 VM은 Linux OSs의 경우 포트 22, Windows OSs의 경우 포트 5986을 사용 하 여 빌드 VM에 연결 합니다.

```powershell-interactive
Get-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName  | Add-AzNetworkSecurityRuleConfig -Name AzureImageBuilderAccess -Description "Allow Image Builder Private Link Access to Proxy VM" -Access Allow -Protocol Tcp -Direction Inbound -Priority 400 -SourceAddressPrefix AzureLoadBalancer -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 60000-60001 | Set-AzNetworkSecurityGroup
```

### <a name="disable-private-service-policy-on-subnet"></a>서브넷에서 개인 서비스 정책 사용 안 함

```powershell-interactive
$virtualNetwork= Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $vnetRgName 
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $subnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork
```

이미지 작성기 네트워킹에 대 한 자세한 내용은 [Azure 이미지 작성기 서비스 네트워킹 옵션](../linux/image-builder-networking.md)을 참조 하세요.

## <a name="modify-the-example-template-and-create-role"></a>예제 템플릿 수정 및 역할 만들기

```powershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Win_Image_on_Existing_VNET/existingVNETWindows.json"
$templateFilePath = "existingVNETWindows.json"

$aibRoleNetworkingUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$aibRoleNetworkingPath = "aibRoleNetworking.json"

$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download configs
Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

Invoke-WebRequest -Uri $aibRoleNetworkingUrl -OutFile $aibRoleNetworkingPath -UseBasicParsing

Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

# update AIB image config template
((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imageName>',$imageName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<vnetName>',$vnetName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<subnetName>',$subnetName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<vnetRgName>',$vnetRgName) | Set-Content -Path $templateFilePath
```
## <a name="create-a-user-assigned-identity-and-set-permissions"></a>사용자 할당 id 만들기 및 사용 권한 설정

```powershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

# create user identity
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

# update template with identity
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

# update the role defintion names
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role',$imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $aibRoleNetworkingPath

# update role definitions
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleNetworkingPath
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace '<vnetRgName>',$vnetRgName) | Set-Content -Path $aibRoleNetworkingPath

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath

# create role definitions from role configurations examples, this avoids granting contributor to the SPN
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json
New-AzRoleDefinition -InputFile  ./aibRoleNetworking.json

# grant role definition to image builder user identity
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $networkRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$vnetRgName"
```

사용 권한에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 Azure 이미지 작성기 서비스 권한 구성](../linux/image-builder-permissions-cli.md) 또는 [PowerShell을 사용 하 여 Azure 이미지 작성기 서비스 권한 구성](../linux/image-builder-permissions-powershell.md)을 참조 하세요.

## <a name="create-the-image"></a>이미지 만들기

Azure Image Builder 서비스에 이미지 구성을 제출합니다.

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# note this will take minute, as validation is run (security / dependencies etc.)
```

이미지 빌드를 시작합니다.

```powershell-interactive
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```

## <a name="get-image-build-status-and-properties"></a>이미지 빌드 상태 및 속성 가져오기

### <a name="query-the-image-template-for-current-or-last-run-status-and-image-template-settings"></a>현재 또는 마지막 실행 상태 및 이미지 템플릿 설정에 대해 이미지 템플릿 쿼리
```powerShell
$managementEp = $currentAzureContext.Environment.ResourceManagerUrl

$urlBuildStatus = [System.String]::Format("{0}subscriptions/{1}/resourceGroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/{2}?api-version=2020-02-14", $managementEp, $currentAzureContext.Subscription.Id,$imageTemplateName)

$buildStatusResult = Invoke-WebRequest -Method GET  -Uri $urlBuildStatus -UseBasicParsing -Headers  @{"Authorization"= ("Bearer " + $accessToken)} -ContentType application/json 
$buildJsonStatus =$buildStatusResult.Content
$buildJsonStatus

```

이 예제의 이미지 빌드에는 약 50 분 (여러 재부팅, windows 업데이트 설치/다시 부팅)이 소요 됩니다. 상태를 쿼리하면 *Lastrunstatus*를 확인 해야 합니다. 아래에는 빌드가 아직 실행 중 이라고 표시 되 고 성공적으로 완료 되 면 ' 성공 '이 표시 됩니다.

```text
  "lastRunStatus": {
   "startTime": "2019-08-21T00:39:40.61322415Z",
   "endTime": "0001-01-01T00:00:00Z",
   "runState": "Running",
   "runSubState": "Building",
   "message": ""
  },
```

### <a name="query-the-distribution-properties"></a>분포 속성 쿼리
VHD 위치에 배포 하는 경우 관리 되는 이미지 위치 속성 또는 공유 이미지 갤러리 복제 상태가 필요 합니다. ' runOutput '을 쿼리해야 합니다. 배포 대상이 있을 때마다 배포 유형의 속성을 설명 하는 고유한 runOutput이 있습니다.

```powerShell
$managementEp = $currentAzureContext.Environment.ResourceManagerUrl
$urlRunOutputStatus = [System.String]::Format("{0}subscriptions/{1}/resourceGroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/{2}?api-version=2020-02-14", $managementEp, $currentAzureContext.Subscription.Id, $runOutputName)

$runOutStatusResult = Invoke-WebRequest -Method GET  -Uri $urlRunOutputStatus -UseBasicParsing -Headers  @{"Authorization"= ("Bearer " + $accessToken)} -ContentType application/json 
$runOutJsonStatus =$runOutStatusResult.Content
$runOutJsonStatus
```
## <a name="create-a-vm"></a>VM 만들기

이제 빌드가 완료 되 면 이미지에서 VM을 빌드할 수 있습니다. [PowerShell New-AzVM 설명서](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.5.0#description)의 예제를 사용 합니다.

## <a name="clean-up"></a>정리

### <a name="delete-image-template-artifact"></a>이미지 템플릿 아티팩트 삭제
```powerShell
# Get ResourceID of the Image Template
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"

### Delete Image Template Artifact
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force

```
### <a name="delete-role-assignment"></a>역할 할당 삭제
```powerShell
## remove role assignments
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId  -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $networkRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$vnetRgName"

## remove definitions
Remove-AzRoleDefinition -Id $imageRoleDefObjId -Force
Remove-AzRoleDefinition -Id $networkRoleObjId -Force

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

### <a name="delete-resource-groups"></a>리소스 그룹 삭제
```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force


# delete VNET created
# BEWARE!!!!! In this example, you have either used an existing VNET or created one for this example. Do not delete your existing VNET. If you want to delete the VNET Resource group used in this example '$vnetRgName', modify the above code.
```

## <a name="next-steps"></a>다음 단계

[Azure Shared Image Gallery](shared-image-galleries.md)에 대해 자세히 알아보세요.

