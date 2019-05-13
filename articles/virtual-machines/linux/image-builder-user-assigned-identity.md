---
title: 가상 머신 이미지 만들기 및 사용자 할당 관리 되는 id를 사용 하 여 Azure Storage (미리 보기)에서 파일에 액세스 하려면
description: 사용자 할당 관리 되는 id를 사용 하 여 Azure Storage에 저장 된 파일에 액세스할 수 있는 Azure 이미지 작성기를 사용 하 여 가상 머신 이미지를 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b0a6c016b2be12ac6686b3748b4b16281899323e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511068"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>이미지 만들기 및 사용자 할당 관리 되는 id를 사용 하 여 Azure Storage에서 파일에 액세스 하려면 

Azure 이미지 작성기 지원 스크립트를 사용 하 여 또는 GitHub와 Azure storage 등와 같은 여러 위치에서 파일을 복사 합니다. 이 사용 하려면 이러한 받아야 Azure 이미지 작성기 외부에서 액세스할 수 있지만 SAS 토큰을 사용 하 여 Azure Storage blob을 보호할 수 있습니다.

이 문서에는 서비스를 사용 하 여 Azure VM 이미지 작성기를 사용 하 여 사용자 지정된 이미지를 만드는 방법을 보여 줍니다는 [사용자 할당 관리 Id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 파일에 액세스 하려면 Azure storage에 이미지 사용자 지정 하지 않고 확인 필요 공개적으로 액세스할 수 또는 SAS 토큰 설정 파일입니다.

아래 예제에서는 두 리소스 그룹을 만들려는, 하나는 사용자 지정 이미지 및 스크립트 파일을 포함 하는 Azure Storage 계정에 호스트 하는 다른 이 있는 빌드 아티팩트 또는 이미지 작성기 외부에서 다른 저장소 계정에서 이미지 파일 실제 시나리오를 시뮬레이트합니다. 사용자 할당 id 다음에 스크립트 파일에 읽기 권한 부여 만들기는 하지만 해당 파일에 대 한 모든 공용 액세스를 설정 하지 않습니다. 셸 사용자 지정 다운로드 하 고 저장소 계정에서 해당 스크립트를 실행 한 다음 사용 합니다.


> [!IMPORTANT]
> Azure 이미지 작성기는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-features"></a>기능 등록
미리 보기 중 Azure 이미지 작성기를 사용 하려면 새 기능을 등록 해야 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

기능 등록 상태를 확인 합니다.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

등록을 확인 합니다.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

말은 하지 마십시오 등록을 하는 경우 다음을 실행 합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

사용 합니다 일부의 정보를 반복적으로 해당 정보를 저장 하는 일부 변수를 만들어 됩니다.


```azurecli-interactive
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

구독 ID에 대 한 변수 만들기 사용 하 여이 가져올 수 있습니다 `az account show | grep id`합니다.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

이미지와 스크립트 저장소 모두에 대 한 리소스 그룹을 만듭니다.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


저장소 만들기 및 GitHub에서 샘플 스크립트를 복사 합니다.

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



이미지 리소스 그룹에서 리소스를 만들 이미지 작성기 권한을 부여 합니다. `--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>사용자 할당 관리 되는 id 만들기

Id를 만들고 스크립트 저장소 계정에 대 한 사용 권한을 할당 합니다. 자세한 내용은 [사용자 할당 관리 Id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)합니다.

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>예제를 수정 합니다.

예제.json 파일을 다운로드 하 고 만든 변수를 사용 하 여 구성 합니다.

```azurecli-interactive
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

이미지 구성을 Azure 이미지 작성기 서비스를 제출 합니다.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

빌드가 완료 될 때까지 기다립니다. 약 15 분 정도 걸릴 수 있습니다.

## <a name="create-a-vm"></a>VM 만들기

이미지에서 VM을 만듭니다. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM을 만든 후 VM을 사용 하 여 SSH 세션을 시작 합니다.

```azurecli-interactive
ssh aibuser@<publicIp>
```

하루에 SSH 연결이 설정 되는 즉시 메시지를 사용 하 여 이미지를 사용자 지정한 표시 됩니다.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>정리

작업을 완료 하는 경우에 더 이상 필요 없는 경우 리소스를 삭제할 수 없습니다.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure 이미지 작성기를 사용 하는 데 문제가 있으면 확인할 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)합니다.