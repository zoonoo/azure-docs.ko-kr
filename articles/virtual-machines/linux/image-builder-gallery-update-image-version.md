---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 기존 이미지 버전에서 새 VM 이미지 버전 만들기
description: Linux에서 Azure 이미지 작성기를 사용 하 여 기존 이미지 버전에서 새 VM 이미지 버전을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: 142b4a85826b9e9a95b17dc631045aab603f3859
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307333"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>미리 보기: Linux에서 Azure 이미지 작성기를 사용 하 여 기존 이미지 버전에서 새 VM 이미지 버전 만들기

이 문서에서는 [공유 이미지 갤러리](shared-image-galleries.md)에서 기존 이미지 버전을 가져와서 업데이트 하 고 갤러리에 새 이미지 버전으로 게시 하는 방법을 보여 줍니다.

이미지를 구성하는 데 샘플 .json 템플릿을 사용합니다. 사용 중인. json 파일은 [helloImageTemplateforSIGfromSIG.js에](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)있습니다. 


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


## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정

[이미지 만들기 및 공유 이미지 갤러리에 배포](image-builder-gallery.md) 를 사용 하 여 공유 이미지 갤러리를 만든 경우 필요한 일부 변수를 이미 만들었습니다. 그렇지 않은 경우이 예제에 사용할 일부 변수를 설정 하세요.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

구독 ID에 대한 변수를 만듭니다. `az account show | grep id`를 사용하여 만들 수 있습니다.

```console
subscriptionID=<Subscription ID>
```

업데이트 하려는 이미지 버전을 가져옵니다.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>사용자 할당 ID 만들기 및 리소스 그룹에 대한 사용 권한 설정
이전 예제에서 사용자 id를 설정 하 고 나면 리소스 ID를 가져와야 합니다. 그런 다음 템플릿에 추가 됩니다.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

사용자 고유의 공유 이미지 갤러리가 이미 있고 이전 예제를 따르지 않은 경우 리소스 그룹에 액세스할 수 있도록 이미지 작성기에 대 한 사용 권한을 할당 하 여 갤러리에 액세스할 수 있도록 해야 합니다. [이미지 만들기 및 공유 이미지 갤러리에 배포](image-builder-gallery.md) 예제의 단계를 검토 하세요.


## <a name="modify-helloimage-example"></a>HelloImage 예제 수정
에서 json 파일을 열어 사용 하려는 예제는 [이미지 작성기 템플릿 참조](image-builder-json.md)와 함께 [helloImageTemplateforSIGfromSIG.js에서](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) 검토할 수 있습니다. 


. Json 예제를 다운로드 하 고 변수로 구성 합니다. 

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>이미지 만들기

이미지 구성을 VM 이미지 빌더 서비스에 제출 합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

다음 단계로 이동 하기 전에 이미지가 빌드되고 복제가 완료 될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM의 공용 IP 주소를 사용 하 여 VM에 대 한 SSH 연결을 만듭니다.

```console
ssh azureuser@<pubIp>
```

SSH 연결이 설정 되는 즉시 이미지는 "하루 메시지"로 사용자 지정 된 것을 볼 수 있습니다.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

`exit`을 입력 하 여 SSH 연결을 닫습니다.

갤러리에서 현재 사용할 수 있는 이미지 버전을 나열할 수도 있습니다.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>다음 단계

이 문서에 사용 된 json 파일의 구성 요소에 대해 자세히 알아보려면 [이미지 작성기 템플릿 참조](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.
