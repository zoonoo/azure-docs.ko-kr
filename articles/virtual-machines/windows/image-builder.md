---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 Windows VM 만들기
description: Azure 이미지 작성기를 사용 하 여 Windows VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: fec6d83870e20b7622f37c52847803d4f03cbba5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722684"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여는 Windows VM 만들기

이 문서는 Azure VM 이미지 작성기를 사용 하 여 사용자 지정된 Windows 이미지를 만드는 방법을 보여 줍니다. 이 문서의 예제에서는 세 가지 [커스터마이저가](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) 이미지를 사용자 지정 합니다.
- PowerShell (ScriptUri)-다운로드 및 실행을 [PowerShell 스크립트](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)합니다.
- Windows 다시 시작-VM을 다시 시작 합니다.
- PowerShell (인라인)-특정 명령을 실행 합니다. 이 예에서 사용 하 여 VM에 디렉터리를 만듭니다 `mkdir c:\\buildActions`합니다.
- 파일-VM에는 GitHub에서 파일을 복사 합니다. 이 예제에서는 복사 [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 에 `c:\buildArtifacts\index.html` vm.

샘플.json 템플릿 이미지를 구성 하려면 사용 합니다. 사용 하 여.json 파일은 여기: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)합니다. 


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
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

구독 ID에 대 한 변수 만들기 사용 하 여이 가져올 수 있습니다 `az account show | grep id`합니다.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

해당 리소스 그룹에 리소스를 만들 이미지 작성기 권한을 부여 합니다. `--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Json 예제를 다운로드 합니다.

예제.json 파일을 다운로드 하 고 만든 변수를 사용 하 여 구성 합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>이미지 만들기

이미지 구성을 VM 이미지 작성기 서비스에 제출

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

빌드가 완료 될 때까지 기다립니다. 약 15 분 정도 걸릴 수 있습니다.

## <a name="create-the-vm"></a>VM 만들기

만든 이미지를 사용 하 여 VM을 만듭니다. 대체 *<password>* 에 대 한 사용자 고유의 암호로 `aibuser` vm.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>사용자 지정 확인

사용자 이름 및 VM을 만들 때 설정한 암호를 사용 하 여 VM에 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트 및 종류를 엽니다.

```console
dir c:\
```

이미지 사용자 지정 하는 동안 생성 된 이러한 두 디렉터리에 표시 됩니다.
- buildActions
- buildArtifacts

## <a name="clean-up"></a>정리

완료 되 면 리소스를 삭제 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용 하는.json 파일의 구성 요소에 대 한 자세한 내용은 참조 하세요 [작성기 템플릿 참조 이미지](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)합니다.

