---
title: 가상 컴퓨터 이미지를 만들고 사용자가 할당한 관리 되는 ID를 사용하여 Azure Storage의 파일에 액세스(미리 보기)
description: Azure Image Builder를 사용하여 Azure Image Builder를 사용하여 가상 시스템 이미지를 생성하여 사용자가 할당한 관리되는 ID를 사용하여 Azure Storage에 저장된 파일에 액세스할 수 있습니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060743"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>이미지를 만들고 사용자가 할당한 관리되는 ID를 사용하여 Azure Storage의 파일에 액세스합니다. 

Azure Image Builder는 스크립트를 사용하거나 GitHub 및 Azure 저장소 등과 같은 여러 위치에서 파일을 복사하는 것을 지원합니다. 이러한 정보를 사용하려면 Azure 이미지 빌더에서 외부로 액세스할 수 있어야 하지만 SAS 토큰을 사용하여 Azure Storage Blob을 보호할 수 있습니다.

이 문서에서는 Azure VM 이미지 빌더를 사용하여 사용자 지정 이미지를 만드는 방법을 보여 주며, 여기서 서비스는 [사용자가 할당한 관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 사용하여 파일을 공개적으로 액세스하거나 SAS 토큰을 설정할 필요 없이 Azure 저장소의 파일에 액세스하여 이미지 사용자 지정을 위해 파일에 액세스합니다.

아래 예제에서는 두 개의 리소스 그룹을 만들고 다른 하나는 사용자 지정 이미지에 사용 되며 다른 하나는 스크립트 파일이 포함 된 Azure 저장소 계정을 호스트 합니다. 이렇게 하면 이미지 작성기 외부에서 다른 저장소 계정에 아티팩트 또는 이미지 파일을 빌드할 수 있는 실제 시나리오를 시뮬레이션합니다. 사용자 할당된 ID를 만든 다음 스크립트 파일에 대한 읽기 권한을 부여하지만 해당 파일에 대한 공용 액세스는 설정하지 않습니다. 그런 다음 Shell 사용자 지정자를 사용하여 저장소 계정에서 해당 스크립트를 다운로드하고 실행합니다.


> [!IMPORTANT]
> Azure 이미지 빌더는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-features"></a>피처 등록
미리 보기 중에 Azure 이미지 빌더를 사용하려면 새 기능을 등록해야 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

피처 등록 상태를 확인합니다.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

등록을 확인하십시오.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

등록을 하지 않는 경우 다음을 실행합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

일부 정보를 반복적으로 사용하므로 해당 정보를 저장하는 몇 가지 변수를 만듭니다.


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

구독 ID에 대한 변수를 만듭니다. 을 사용하여 `az account show | grep id`이 것을 얻을 수 있습니다.

```console
subscriptionID=<Your subscription ID>
```

이미지와 스크립트 저장소 모두에 대한 리소스 그룹을 만듭니다.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


저장소를 만들고 GitHub에서 샘플 스크립트를 복사합니다.

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



이미지 작성자에게 이미지 리소스 그룹에 리소스를 만들 수 있는 권한을 부여합니다. 값은 `--assignee` 이미지 빌더 서비스의 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>사용자 할당된 관리되는 ID 만들기

ID를 만들고 스크립트 저장소 계정에 대한 권한을 할당합니다. 자세한 내용은 [사용자 할당된 관리되는 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)를 참조하십시오.

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


## <a name="modify-the-example"></a>예제 수정

예제 .json 파일을 다운로드하고 만든 변수로 구성합니다.

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

Azure 이미지 빌더 서비스에 이미지 구성을 제출합니다.

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

빌드가 완료될 때까지 기다립니다. 이 경우 약 15분이 소요될 수 있습니다.

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

VM을 만든 후 VM을 통해 SSH 세션을 시작합니다.

```console
ssh aibuser@<publicIp>
```

SSH 연결이 설정되는 즉시 이미지가 오늘의 메시지로 사용자 정의 된 것을 볼 수 있습니다!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>정리

완료되면 더 이상 필요하지 않은 리소스를 삭제할 수 있습니다.

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

Azure 이미지 빌더로 작업하는 데 문제가 있는 경우 [문제 해결을](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)참조하십시오.