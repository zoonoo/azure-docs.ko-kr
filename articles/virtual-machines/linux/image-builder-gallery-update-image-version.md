---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 기존 이미지 버전에서 새 이미지 버전을 만들려면
description: Azure 이미지 작성기를 사용 하 여 기존 이미지 버전에서 새 이미지 버전을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 31ef53abcf9b416500ee70e42cc3cbd12cb11f35
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159542"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여 기존 이미지 버전에서 새 이미지 버전을 만들려면

이 문서 기존 이미지 버전을 사용 하는 방법을 보여 줍니다.는 [공유 이미지 갤러리](shared-image-galleries.md), 업데이트 및 새 이미지 버전을 갤러리에 게시 합니다.

샘플.json 템플릿 이미지를 구성 하려면 사용 합니다. 사용 하 여.json 파일은 여기: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)합니다. 


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


## <a name="set-variables-and-permissions"></a>변수 설정 및 사용 권한

사용 하는 경우 [이미지를 만들고 공유 이미지 갤러리에 배포할](image-builder-gallery.md) 공유 이미지 갤러리를 만들려면 이미 만든 필요한 변수만의 일부입니다. 그렇지 않은 경우이 예제에 사용 되는 몇 가지 변수를 설정 하세요.

미리 보기의 경우 이미지 작성기는 관리 되는 원본 이미지로 동일한 리소스 그룹에 사용자 지정 이미지 만들기를 지원만. 이 예제는 관리 되는 원본 이미지로 동일한 리소스 그룹에서 리소스 그룹 이름을 업데이트 합니다.


```azurecli-interactive
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

구독 ID에 대 한 변수 만들기 사용 하 여이 가져올 수 있습니다 `az account show | grep id`합니다.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

업데이트 하려는 이미지 버전을 가져옵니다.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


공유 이미지 갤러리를 이미 있고 앞의 예제를 따르지 않는 경우에 이미지 갤러리에 액세스할 수 있도록 리소스 그룹을 액세스 하는 작성기에 대 한 사용 권한을 할당 해야 합니다.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>HelloImage 예제를 수정 합니다.
.Json 파일을 열어 사용 하는 예제를 검토할 수 있습니다: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) 함께 합니다 [이미지 작성기 템플릿 참조](image-builder-json.md). 


Json 예제를 다운로드 하 고 변수를 사용 하 여 구성 합니다. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>이미지 만들기

이미지 구성 VM 이미지 작성기 서비스에 제출 합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

이미지에 빌드될 때까지 대기 하 고 단계로 넘어가기 전에 복제 합니다.


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

VM의 공용 IP 주소를 사용 하 여 VM에 SSH 연결을 만듭니다.

```azurecli-interactive
ssh azureuser@<pubIp>
```

SSH 연결이 설정 되는 즉시 이미지를 "메시지의 the Day"를 사용 하 여 사용자 지정 하는 것이 나타납니다.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

형식 `exit` SSH 연결을 닫습니다.

갤러리에서 이제 사용할 수 있는 이미지 버전을 나열할 수도 있습니다.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>다음 단계

이 문서에서 사용 하는.json 파일의 구성 요소에 대 한 자세한 내용은 참조 하세요 [작성기 템플릿 참조 이미지](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)합니다.