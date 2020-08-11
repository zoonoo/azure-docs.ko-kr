---
title: Linux Vm 용 Azure 이미지 작성기를 사용 하 여 기존 Azure VNET (미리 보기)에 대 한 액세스 허용
description: Azure 이미지 작성기를 사용 하 여 기존 Azure VNET에 액세스할 수 있는 Linux VM 이미지 만들기
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: f216b6fa3a0e43c1c0313baa4f8414546a74d8f0
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068234"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Linux Vm에 Azure 이미지 작성기를 사용 하 여 기존 Azure VNET에 액세스 허용

이 문서에서는 Azure 이미지 작성기를 사용 하 여 VNET의 기존 리소스에 대 한 액세스 권한이 있는 기본 사용자 지정 Linux 이미지를 만드는 방법을 보여 줍니다. 만든 빌드 VM은 구독에서 지정 하는 새 VNET 또는 기존 VNET에 배포 됩니다. 기존 Azure VNET을 사용 하는 경우 Azure 이미지 작성기 서비스는 공용 네트워크 연결이 필요 하지 않습니다.

> [!IMPORTANT]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>기능 등록

먼저, Azure 이미지 작성기 서비스에 등록 해야 합니다. 등록은 스테이징 리소스 그룹을 만들고, 관리 하 고, 삭제할 수 있는 권한을 서비스에 부여 합니다. 또한이 서비스에는 이미지 빌드에 필요한 그룹에 리소스를 추가할 수 있는 권한이 있습니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정 

일부 정보를 반복 해 서 사용 하 게 됩니다. 해당 정보를 저장할 변수를 만듭니다.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>네트워킹 구성

기존 VNET\Subnet\NSG 없는 경우 다음 스크립트를 사용 하 여 새로 만듭니다.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>네트워크 보안 그룹 규칙 추가

이 규칙은 Azure 이미지 작성기 부하 분산 장치에서 프록시 VM으로의 연결을 허용 합니다. 포트 60001은 Linux OSs 용 이며 포트 6만는 Windows OSs 용입니다. 프록시 VM은 Linux OSs의 경우 포트 22, Windows OSs의 경우 포트 5986을 사용 하 여 빌드 VM에 연결 합니다.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>서브넷에서 개인 서비스 정책 사용 안 함

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

이미지 작성기 네트워킹에 대 한 자세한 내용은 [Azure 이미지 작성기 서비스 네트워킹 옵션](image-builder-networking.md)을 참조 하세요.

## <a name="modify-the-example-template-and-create-role"></a>예제 템플릿 수정 및 역할 만들기

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>리소스 그룹에 대 한 사용 권한 설정

Image Builder는 제공된 [user-identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)를 사용하여 Azure SIG(Shared Image Gallery)에 이미지를 삽입합니다. 이 예제에서는 SIG에 이미지를 배포하는 세분화된 작업을 포함하는 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

이미지 작성기에 더 낮은 세분성과 증가 된 권한을 부여 하는 대신 두 가지 역할을 만들 수 있습니다. 하나는 이미지를 만들기 위한 작성기 권한을 제공 하 고, 다른 하나는 빌드 VM 및 부하 분산 장치를 VNET에 연결 하는 것을 허용 합니다.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

사용 권한에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-cli.md) 또는 [PowerShell을 사용 하 여 Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-powershell.md)을 참조 하세요.

## <a name="create-the-image"></a>이미지 만들기

Azure Image Builder 서비스에 이미지 구성을 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

이미지를 만들고 두 지역에 복제하는 데 다소 시간이 걸릴 수 있습니다. VM 만들기를 계속 진행하기 전에 이 단계가 완료될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

Azure Image Builder에서 만든 이미지 버전에서 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM에 SSH합니다.

```bash
ssh aibuser@<publicIpAddress>
```

SSH 연결이 설정되는 즉시 오늘의 메시지로 이미지가 사용자 지정된 것을 볼 수 있을 것입니다.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>리소스 정리

이제 이미지 버전을 recustomizing 하 여 동일한 이미지의 새 버전을 만들려면 다음 단계를 건너뛰고 [Azure 이미지 작성기를 사용 하 여 다른 이미지 버전을 만듭니다](image-builder-gallery-update-image-version.md).로 이동 하세요.


다음은 다른 모든 리소스 파일과 함께 생성 된 이미지를 삭제 합니다. 리소스를 삭제하기 전에 이 배포를 완료했는지 확인합니다.

이미지 갤러리 리소스를 삭제하는 경우 먼저 모든 이미지 버전을 삭제해야 해당 이미지 버전을 만드는 데 사용된 이미지 정의를 삭제할 수 있습니다. 갤러리를 삭제하려면 먼저 갤러리에서 이미지 정의를 모두 삭제해야 합니다.

Image Builder 템플릿을 삭제합니다.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

권한 할당, 역할 및 id 삭제
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

해당 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

이 빠른 시작에 대 한 VNET을 만든 경우 더 이상 사용 되지 않는 VNET을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Shared Image Gallery](shared-image-galleries.md)에 대해 자세히 알아보세요.
