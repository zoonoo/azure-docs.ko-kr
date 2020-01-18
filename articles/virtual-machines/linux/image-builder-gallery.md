---
title: Linux Vm에 대 한 이미지 갤러리와 함께 Azure 이미지 작성기 사용 (미리 보기)
description: Azure 이미지 작성기 및 공유 이미지 갤러리를 사용 하 여 Linux VM 이미지를 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 08441a98d9104109b4cfc130ab6adb31dc4fce45
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260517"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>미리 보기: Linux 이미지를 만들어 공유 이미지 갤러리에 배포 

이 문서에서는 Azure 이미지 작성기와 Azure CLI를 사용 하 여 [공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)에서 이미지 버전을 만든 다음 전체적으로 이미지를 배포 하는 방법을 보여 줍니다. [Azure PowerShell](../windows/image-builder-gallery.md)를 사용 하 여이 작업을 수행할 수도 있습니다.


샘플. json 템플릿을 사용 하 여 이미지를 구성 합니다. 사용 중인. json 파일은 [helloImageTemplateforSIG](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json)입니다. 

공유 이미지 갤러리에 이미지를 배포 하기 위해 템플릿에서 [sharedImage](image-builder-json.md#distribute-sharedimage) 를 템플릿의 `distribute` 섹션 값으로 사용 합니다.

> [!IMPORTANT]
> Azure 이미지 작성기는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-features"></a>기능 등록
미리 보기 중에 Azure 이미지 작성기를 사용 하려면 새 기능을 등록 해야 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

기능 등록의 상태를 확인 합니다.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

등록을 확인 하세요.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

등록 되지 않은 경우 다음을 실행 합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정 

일부 정보를 반복 해 서 사용 하 게 되며,이 정보를 저장 하는 몇 가지 변수를 만듭니다.

미리 보기의 경우 이미지 작성기는 원본 관리 이미지와 동일한 리소스 그룹에서 사용자 지정 이미지 만들기만 지원 합니다. 이 예제의 리소스 그룹 이름을 원본 관리 이미지와 동일한 리소스 그룹으로 업데이트 합니다.

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

구독 ID에 대 한 변수를 만듭니다. `az account show | grep id`를 사용 하 여이를 가져올 수 있습니다.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


해당 리소스 그룹에서 리소스를 만들 수 있는 Azure 이미지 작성기 권한을 부여 합니다. `--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>이미지 정의 및 갤러리 만들기

공유 이미지 갤러리에서 이미지 작성기를 사용 하려면 기존 이미지 갤러리 및 이미지 정의가 있어야 합니다. 이미지 작성기는 이미지 갤러리 및 이미지 정의를 만들지 않습니다.

사용할 갤러리 및 이미지 정의가 아직 없는 경우 만들기를 시작 합니다. 먼저 이미지 갤러리를 만듭니다.

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


## <a name="download-and-configure-the-json"></a>Json 다운로드 및 구성

Json 템플릿을 다운로드 하 고 변수로 구성 합니다.

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

이 다음 파트는 갤러리에서 이미지 버전을 만듭니다. 

이미지 구성을 Azure 이미지 작성기 서비스에 제출 합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

이미지를 만들고 두 영역에 복제 하는 데 다소 시간이 걸릴 수 있습니다. VM 만들기로 이동 하기 전에이 부분이 완료 될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

Azure 이미지 작성기에서 만든 이미지 버전에서 VM을 만듭니다.

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

SSH 연결이 설정 되는 즉시 이미지는 *하루 메시지* 와 함께 사용자 지정 된 것을 볼 수 있습니다.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>리소스 정리

이제 이미지 버전을 다시 사용자 지정 하 여 동일한 이미지의 새 버전을 만들려면 다음 단계를 건너뛰고 [Azure 이미지 작성기를 사용 하 여 다른 이미지 버전 만들기](image-builder-gallery-update-image-version.md)로 이동 하세요.


그러면 생성 된 이미지와 함께 다른 모든 리소스 파일이 삭제 됩니다. 리소스를 삭제 하기 전에이 배포를 완료 했는지 확인 합니다.

이미지 갤러리 리소스를 삭제 하는 경우 이미지를 만드는 데 사용 된 이미지 정의를 삭제 하려면 먼저 모든 이미지 버전을 삭제 해야 합니다. 갤러리를 삭제 하려면 먼저 갤러리에서 이미지 정의를 모두 삭제 해야 합니다.

이미지 작성기 템플릿을 삭제 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

이미지 작성기에서 만든 이미지 버전을 가져옵니다 .이는 항상 `0.`로 시작 하 고 이미지 버전을 삭제 합니다.

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


이미지 정의를 삭제 합니다.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

갤러리를 삭제 합니다.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

해당 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>다음 단계

[Azure 공유 이미지 갤러리](shared-image-galleries.md)에 대해 자세히 알아보세요.