---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 기존 이미지 버전에서 새 이미지 버전을 만들려면
description: Azure 이미지 작성기를 사용 하 여 기존 이미지 버전에서 새 이미지 버전을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: f1bce4c2e5c7ae9dc7ec5917fbc5ac115eecdffa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160097"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여 기존 이미지 버전에서 새 이미지 버전을 만들려면

이 문서 기존 이미지 버전을 사용 하는 방법을 보여 줍니다.는 [공유 이미지 갤러리](shared-image-galleries.md), 업데이트 및 새 이미지 버전을 갤러리에 게시 합니다.

샘플.json 템플릿 이미지를 구성 하려면 사용 합니다. 사용 하 여.json 파일은 여기: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json)합니다. 

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
az provider show -n Microsoft.Compute | grep registrationState
```

말은 하지 마십시오 등록을 하는 경우 다음을 실행 합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>변수 설정 및 사용 권한

사용 하는 경우 [이미지를 만들고 공유 이미지 갤러리에 배포할](image-builder-gallery.md) 공유 이미지 갤러리를 만들려면 이미 만든 변수가 필요 합니다. 그렇지 않은 경우이 예제에 사용 되는 몇 가지 변수를 설정 하세요.

미리 보기의 경우 이미지 작성기는 관리 되는 원본 이미지로 동일한 리소스 그룹에 사용자 지정 이미지 만들기를 지원만. 이 예제는 관리 되는 원본 이미지로 동일한 리소스 그룹에서 리소스 그룹 이름을 업데이트 합니다.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

구독 ID에 대 한 변수 만들기 사용 하 여이 가져올 수 있습니다 `az account show | grep id`합니다.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

업데이트 하려는 이미지 버전을 가져옵니다.

```azurecli-interactive
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
.Json 파일을 열어 사용 하는 예제를 검토할 수 있습니다: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) 함께 합니다 [이미지 작성기 템플릿 참조](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Json 예제를 다운로드 하 고 변수를 사용 하 여 구성 합니다. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>이미지 만들기

이미지 구성 VM 이미지 작성기 서비스에 제출 합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

이미지에 빌드될 때까지 대기 하 고 단계로 넘어가기 전에 복제 합니다.


## <a name="create-the-vm"></a>VM 만들기

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>사용자 지정 확인
사용자 이름 및 VM을 만들 때 설정한 암호를 사용 하 여 VM에 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트 및 종류를 엽니다.

```console
dir c:\
```

이제 두 개의 디렉터리가 표시 됩니다.
- `buildActions` 첫 번째 이미지 버전에서 만든 합니다.
- `buildActions2` 두 번째 이미지 버전을 만드는 첫 번째 이미지 버전을 업데이트 하는 일환으로 만들어진 메시지가 나타납니다.


## <a name="next-steps"></a>다음 단계

이 문서에서 사용 하는.json 파일의 구성 요소에 대 한 자세한 내용은 참조 하세요 [작성기 템플릿 참조 이미지](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)합니다.