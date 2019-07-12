---
title: 이미지 갤러리를 사용 하 여 Azure 이미지 작성기를 사용 하 여 Windows 가상 컴퓨터 (미리 보기)
description: Azure 이미지 작성기 및 공유 이미지 갤러리를 사용 하 여 Windows 이미지를 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 164fc4d8ad567c75ed5029aaf26af260398f80ba
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722686"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>미리 보기: Windows 이미지를 만들고 공유 이미지 갤러리에 배포 

이 문서에 이미지 버전을 만들어 Azure 이미지 작성기를 사용 하는 방법을 보여 줍니다는 [공유 이미지 갤러리](shared-image-galleries.md), 전역적으로 이미지를 배포 합니다.

.Json 템플릿 이미지를 구성 하려면 사용 합니다. 사용 하 여.json 파일은 여기: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json)합니다. 

템플릿을 사용 하 여 공유 이미지 갤러리에 이미지를 배포 하려면 [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) 값으로는 `distribute` 템플릿의 섹션입니다.

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

사용 합니다 일부의 정보를 반복적으로 해당 정보를 저장 하는 일부 변수를 만들어 됩니다. 같은 변수를 값을 바꿉니다 `username` 및 `vmpassword`, 사용자 고유의 정보로 합니다.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

구독 ID에 대 한 변수 만들기 사용 하 여이 가져올 수 있습니다 `az account show | grep id`합니다.

```azurecli-interactive
subscriptionID="Subscription ID"
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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>다운로드 하 고는.json 구성

.Json 템플릿을 다운로드 하 고 변수를 사용 하 여 구성 합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>이미지 버전 만들기

이 다음 파트 갤러리에서 이미지 버전을 만듭니다. 

이미지 구성을 Azure 이미지 작성기 서비스를 제출 합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

이미지를 만들고 두 지역 모두에 복제 하는 시간이 걸릴 수 있습니다. 이 부분 VM을 이동 하기 전에 완료 될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

Azure 이미지 작성기가 만든 이미지 버전에서 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
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

명명 된 디렉터리가 보일 `buildActions` 이미지 사용자 지정 하는 동안 생성 된 합니다.


## <a name="clean-up-resources"></a>리소스 정리
이제 동일한 이미지의 새 버전을 만들려면 이미지 버전을 사용자 지정 다시 시도 하려는 경우 **이 단계를 건너뜁니다** 으로 이동 하 고 [다른 이미지 버전을 만들려면 사용 하 여 Azure 이미지 작성기](image-builder-gallery-update-image-version.md)합니다.


다른 리소스 파일을 모두 함께 만든 이미지를 삭제 합니다. 이 배포 된 리소스를 삭제 하기 전에 완료 해야 합니다.

이미지 갤러리 리소스를 삭제 하는 경우 만드는 데 사용 하는 이미지 정의 삭제 하려면 먼저 모든 이미지 버전을 삭제할 해야 있습니다. 갤러리를 삭제 하려면 먼저 갤러리의 이미지 정의 모두 삭제 해야 합니다.

이미지 작성기 템플릿을 삭제 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
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

사용자가 만든 이미지 버전을 업데이트 하는 방법에 알아보려면 참조 [다른 이미지 버전을 만들려면 사용 하 여 Azure 이미지 작성기](image-builder-gallery-update-image-version.md)합니다.