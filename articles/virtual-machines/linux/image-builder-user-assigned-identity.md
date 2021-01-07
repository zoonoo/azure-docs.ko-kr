---
title: 가상 머신 이미지를 만들고 사용자 할당 관리 id를 사용 하 여 Azure Storage의 파일에 액세스 (미리 보기)
description: 사용자 할당 관리 id를 사용 하 여 Azure Storage에 저장 된 파일에 액세스할 수 있는 Azure 이미지 작성기를 사용 하 여 가상 머신 이미지를 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: f5734d4b1871dd285fc83a72631f7d645e0b72ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307265"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>이미지를 만들고 사용자 할당 관리 id를 사용 하 여 Azure Storage의 파일에 액세스 

Azure 이미지 작성기는 스크립트를 사용 하거나 GitHub, Azure storage 등의 여러 위치에서 파일을 복사 하도록 지원 합니다. 이를 사용 하려면 Azure 이미지 작성기에서 외부에 액세스할 수 있어야 하지만 SAS 토큰을 사용 하 여 Azure Storage blob을 보호할 수 있습니다.

이 문서에서는 Azure VM 이미지 작성기를 사용 하 여 사용자 지정 이미지를 만드는 방법을 보여 줍니다. 여기서 서비스는 [사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 파일을 공개적으로 액세스할 수 있도록 하거나 SAS 토큰을 설정 하지 않고도 이미지 사용자 지정을 위해 azure storage의 파일에 액세스 합니다.

아래 예제에서는 두 개의 리소스 그룹을 만들고, 하나는 사용자 지정 이미지에 사용 되 고, 다른 하나는 스크립트 파일을 포함 하는 Azure Storage 계정을 호스팅합니다. 이는 이미지 작성기 외부의 다른 저장소 계정에 빌드 아티팩트 또는 이미지 파일이 있을 수 있는 실제 시나리오를 시뮬레이션 합니다. 사용자 할당 id를 만든 다음 스크립트 파일에 대 한 읽기 권한을 부여 합니다. 하지만 해당 파일에 대 한 공용 액세스는 설정 하지 않습니다. 그런 다음 셸 사용자 지정자를 사용 하 여 저장소 계정에서 해당 스크립트를 다운로드 하 고 실행 합니다.


> [!IMPORTANT]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-features"></a>기능 등록
미리 보기 중에 Azure Image Builder를 사용하려면 이 새 기능을 등록해야 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

기능 등록 상태를 확인합니다.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

등록을 확인합니다.


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

등록되지 않은 경우 다음을 실행합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

일부 정보를 반복해서 사용하게 되며, 해당 정보를 저장하기 위해 몇 가지 변수를 만듭니다.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

구독 ID에 대한 변수를 만듭니다. `az account show | grep id`를 사용하여 만들 수 있습니다.

```console
subscriptionID=<Your subscription ID>
```

이미지 및 스크립트 저장소에 대 한 리소스 그룹을 만듭니다.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

사용자 할당 id를 만들고 리소스 그룹에 대 한 사용 권한을 설정 합니다.

이미지 작성기는 제공 된 [사용자 id](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) 를 사용 하 여 리소스 그룹에 이미지를 삽입 합니다. 이 예제에서는 이미지 배포를 수행 하는 세분화 된 작업을 포함 하는 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

저장소를 만들고 GitHub에서 샘플 스크립트를 복사 합니다.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

이미지 작성기에 이미지 리소스 그룹에서 리소스를 만들 수 있는 권한을 부여 합니다. `--assignee`값은 사용자 ID id입니다.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>예제 수정

예제 json 파일을 다운로드 하 고 사용자가 만든 변수를 사용 하 여 구성 합니다.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>이미지 만들기

Azure Image Builder 서비스에 이미지 구성을 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

빌드가 완료될 때까지 기다립니다. 이는 약 15 분 정도 걸릴 수 있습니다.

## <a name="create-a-vm"></a>VM 만들기

이미지에서 VM을 만듭니다. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM을 만든 후 VM을 사용 하 여 SSH 세션을 시작 합니다.

```console
ssh aibuser@<publicIp>
```

SSH 연결이 설정되는 즉시 오늘의 메시지로 이미지가 사용자 지정된 것을 볼 수 있을 것입니다.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>정리

작업이 완료 되 면 리소스가 더 이상 필요 하지 않은 경우 삭제할 수 있습니다.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure 이미지 작성기를 사용 하 여 작업 하는 데 문제가 있는 경우 [문제 해결](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)을 참조 하세요.
