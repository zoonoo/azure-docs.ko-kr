---
title: Linux VM용 이미지 갤러리가 있는 Azure 이미지 빌더 사용(미리 보기)
description: Azure 이미지 빌더 및 공유 이미지 갤러리를 사용하여 Linux VM 이미지를 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945003"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>미리 보기: Linux 이미지를 만들고 공유 이미지 갤러리에 배포합니다. 

이 문서에서는 Azure 이미지 빌더 및 Azure CLI를 사용하여 [공유 이미지 갤러리에서](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)이미지 버전을 만든 다음 이미지를 전역으로 배포하는 방법을 보여 주며 이 문서에서는 [Azure PowerShell](../windows/image-builder-gallery.md)을 사용하여 이 작업을 수행할 수도 있습니다.


샘플 .json 템플릿을 사용하여 이미지를 구성합니다. 우리가 사용하는 .json 파일은 여기 : [helloImageTemplateForSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

공유 이미지 갤러리에 이미지를 배포하려면 템플릿은 [sharedImage를](image-builder-json.md#distribute-sharedimage) 템플릿 `distribute` 섹션의 값으로 사용합니다.

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

## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정 

일부 정보를 반복적으로 사용하므로 해당 정보를 저장하는 몇 가지 변수를 만듭니다.

미리 보기의 경우 이미지 작성기는 소스 관리 이미지와 동일한 리소스 그룹에서 사용자 지정 이미지 만들기만 지원합니다. 이 예제에서 리소스 그룹 이름을 원본 관리 이미지와 동일한 리소스 그룹으로 업데이트합니다.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

구독 ID에 대한 변수를 만듭니다. 을 사용하여 `az account show | grep id`이 것을 얻을 수 있습니다.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Azure 이미지 빌더에게 해당 리소스 그룹에서 리소스를 만들 수 있는 권한을 부여합니다. 값은 `--assignee` 이미지 빌더 서비스의 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>이미지 정의 및 갤러리 만들기

공유 이미지 갤러리에서 이미지 빌더를 사용하려면 기존 이미지 갤러리 와 이미지 정의가 있어야 합니다. 이미지 빌더는 이미지 갤러리 및 이미지 정의를 만들지 않습니다.

사용할 갤러리 및 이미지 정의가 아직 없는 경우 먼저 갤러리를 만듭니다. 먼저 이미지 갤러리를 만듭니다.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

그런 다음 이미지 정의를 만듭니다.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>.json을 다운로드하고 구성합니다.

.json 템플릿을 다운로드하여 변수로 구성합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>이미지 버전 만들기

이 다음 부분에서는 갤러리에서 이미지 버전을 만듭니다. 

Azure 이미지 빌더 서비스에 이미지 구성을 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

이미지를 만들고 두 리전에 복제하는 데 시간이 걸릴 수 있습니다. VM 을 만들기 로 이동하기 전에 이 부품이 완료될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

Azure 이미지 빌더에서 만든 이미지 버전에서 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM에 SSH 연결합니다.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

SSH 연결이 설정되는 즉시 이미지가 *오늘의 메시지로* 사용자 정의 된 것을 볼 수 있습니다!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>리소스 정리

이제 이미지 버전을 다시 사용자 지정하여 동일한 이미지의 새 버전을 만들려면 다음 단계를 건너뛰고 [Azure 이미지 빌더 사용으로 이동하여 다른 이미지 버전을 만듭니다.](image-builder-gallery-update-image-version.md)


그러면 생성된 이미지가 다른 모든 리소스 파일과 함께 삭제됩니다. 리소스를 삭제하기 전에 이 배포를 완료했는지 확인합니다.

이미지 갤러리 리소스를 삭제할 때 이미지 버전을 만드는 데 사용된 이미지 정의를 삭제하려면 모든 이미지 버전을 삭제해야 합니다. 갤러리를 삭제하려면 먼저 갤러리의 모든 이미지 정의를 삭제해야 합니다.

이미지 빌더 템플릿을 삭제합니다.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

이미지 빌더가 만든 이미지 버전을 가져옵니다. `0.`

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


이미지 정의를 삭제합니다.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

갤러리를 삭제합니다.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

해당 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>다음 단계

[Azure 공유 이미지 갤러리에](shared-image-galleries.md)대해 자세히 알아봅니다.