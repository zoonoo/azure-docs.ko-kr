---
title: 이미지 갤러리를 사용 하 여 Azure 이미지 작성기를 사용 하 여 Linux 가상 머신의 (미리 보기)
description: Azure 이미지 작성기 및 공유 이미지 갤러리를 사용 하 여 Linux 이미지를 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: a47c7fd60ec9ddd3fd5e5accae8849bd62bf894c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671488"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>미리 보기: Linux 이미지를 만들고 공유 이미지 갤러리에 배포 

이 아티클에서 Azure 이미지 작성기에 이미지 버전을 만들어 사용 하는 방법을 [공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), 전역적으로 이미지를 배포 합니다.


샘플.json 템플릿 이미지를 구성 하려면 사용 합니다. 사용 하 여.json 파일은 여기: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json)합니다. 

템플릿을 사용 하 여 공유 이미지 갤러리에 이미지를 배포 하려면 [sharedImage](image-builder-json.md#distribute-sharedimage) 값으로는 `distribute` 템플릿의 섹션입니다.

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

## <a name="set-variables-and-permissions"></a>변수 설정 및 사용 권한 

사용 합니다 일부의 정보를 반복적으로 해당 정보를 저장 하는 일부 변수를 만들어 됩니다.

미리 보기의 경우 이미지 작성기는 관리 되는 원본 이미지로 동일한 리소스 그룹에 사용자 지정 이미지 만들기를 지원만. 이 예제는 관리 되는 원본 이미지로 동일한 리소스 그룹에서 리소스 그룹 이름을 업데이트 합니다.

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

구독 ID에 대 한 변수 만들기 사용 하 여이 가져올 수 있습니다 `az account show | grep id`합니다.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


해당 리소스 그룹에 리소스를 만드는 Azure 이미지 작성기 권한을 제공 합니다. `--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>이미지 정 및 갤러리 만들기

이미지 갤러리를 만듭니다. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

이미지 정을 만듭니다.

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


## <a name="download-and-configure-the-json"></a>다운로드 하 고는.json 구성

.Json 템플릿을 다운로드 하 고 변수를 사용 하 여 구성 합니다.

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

이 다음 파트 갤러리에서 이미지 버전을 만듭니다. 

이미지 구성을 Azure 이미지 작성기 서비스를 제출 합니다.

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

이미지를 만들고 두 지역 모두에 복제 하는 시간이 걸릴 수 있습니다. 이 부분 VM을 이동 하기 전에 완료 될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

Azure 이미지 작성기가 만든 이미지 버전에서 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM에 SSH 합니다.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

이미지를 사용 하 여 사용자 지정 하는 것이 표시 된 *오늘의 메시지* SSH 연결이 설정 되는 즉시!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>리소스 정리

이제 동일한 이미지의 새 버전을 만들려면 이미지 버전을 사용자 지정 다시 시도 하려는 경우 다음 단계를 건너뛰고에 이동 [다른 이미지 버전을 만들려면 사용 하 여 Azure 이미지 작성기](image-builder-gallery-update-image-version.md)합니다.


다른 리소스 파일을 모두 함께 만든 이미지를 삭제 합니다. 이 배포 된 리소스를 삭제 하기 전에 완료 해야 합니다.

이미지 갤러리 리소스를 삭제 하는 경우 만드는 데 사용 하는 이미지 정의 삭제 하려면 먼저 모든 이미지 버전을 삭제할 해야 있습니다. 갤러리를 삭제 하려면 먼저 갤러리의 이미지 정의 모두 삭제 해야 합니다.

이미지 작성기 템플릿을 삭제 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

이미지 작성기로 만든 이미지 버전을 가져오려면, 항상 시작이 `0.`, 다음 이미지 버전을 삭제 하 고

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


이미지 정의 삭제 합니다.

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

에 대해 자세히 알아보세요 [Azure 공유 이미지 갤러리](shared-image-galleries.md)합니다.