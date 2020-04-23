---
title: Azure 이미지 빌더(미리 보기)를 사용하여 기존 이미지 버전에서 새 이미지 버전 만들기
description: Azure 이미지 빌더를 사용하여 기존 이미지 버전에서 새 VM 이미지 버전을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 766e7d5c4151000a582bcf07d80b89af3b7d8a65
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869532"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>미리 보기: Azure 이미지 빌더를 사용하여 기존 이미지 버전에서 새 VM 이미지 버전 만들기

이 문서에서는 [공유 이미지 갤러리에서](shared-image-galleries.md)기존 이미지 버전을 가져 와서 업데이트하고 갤러리에 새 이미지 버전으로 게시하는 방법을 보여 주며 이 문서에서는 해당 이미지 버전을 게시합니다.

샘플 .json 템플릿을 사용하여 이미지를 구성합니다. 우리가 사용하는 .json 파일은 여기 : [안녕하세요이미지 템플릿SIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

등록을 하지 않는 경우 다음을 실행합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정

이미지 만들기를 사용하고 [공유 이미지 갤러리에 배포하여](image-builder-gallery.md) 공유 이미지 갤러리를 만든 경우 필요한 변수를 이미 만들었습니다. 그렇지 않은 경우 이 예제에 사용할 몇 가지 변수를 설정하십시오.

미리 보기의 경우 이미지 작성기는 소스 관리 이미지와 동일한 리소스 그룹에서 사용자 지정 이미지 만들기만 지원합니다. 이 예제에서 리소스 그룹 이름을 원본 관리 이미지와 동일한 리소스 그룹으로 업데이트합니다.

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

구독 ID에 대한 변수를 만듭니다. 을 사용하여 `az account show | grep id`이 것을 얻을 수 있습니다.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

업데이트할 이미지 버전을 가져옵니다.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


이미 자신의 공유 이미지 갤러리가 있고 이전 예제를 따르지 않은 경우 이미지 작성기에서 리소스 그룹에 액세스하여 갤러리에 액세스할 수 있도록 권한을 할당해야 합니다.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>helloImage 예제 수정
여기에서 .json 파일을 열어 사용하려는 예제를 검토할 수 [있습니다.](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) [Image Builder template reference](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 


.json 예제를 다운로드하여 변수로 구성합니다. 

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

이미지 구성을 VM 이미지 빌더 서비스에 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

다음 단계로 넘어가기 전에 이미지가 빌드되고 복제될 때까지 기다립니다.


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
VM을 만들 때 설정한 사용자 이름과 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내부에서 cmd 프롬프트를 열고 다음을 입력합니다.

```console
dir c:\
```

이제 두 개의 디렉터리가 표시됩니다.
- `buildActions`첫 번째 이미지 버전에서 만들어졌습니다.
- `buildActions2`두 번째 이미지 버전을 만들기 위해 첫 번째 이미지 버전을 업데이트하는 부분으로 작성되었습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서 사용되는 .json 파일의 구성 요소에 대해 자세히 알아보려면 [이미지 빌더 템플릿 참조를](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)참조하십시오.