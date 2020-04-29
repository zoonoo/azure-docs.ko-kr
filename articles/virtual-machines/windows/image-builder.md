---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 Windows VM 만들기
description: Azure 이미지 작성기를 사용 하 여 Windows VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869505"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여 Windows VM 만들기

이 문서에서는 Azure VM 이미지 작성기를 사용 하 여 사용자 지정 된 Windows 이미지를 만드는 방법을 보여 줍니다. 이 문서의 예제에서는 [이미지를 사용자 지정 하기 위해](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) 사용자 지정을 사용 합니다.
- PowerShell (ScriptUri)- [powershell 스크립트](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)를 다운로드 하 여 실행 합니다.
- Windows 다시 시작-VM을 다시 시작 합니다.
- PowerShell (인라인)-특정 명령을 실행 합니다. 이 예제에서는를 사용 하 여 `mkdir c:\\buildActions`VM에 디렉터리를 만듭니다.
- 파일-GitHub의 파일을 VM에 복사 합니다. 이 예제에서는 [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) VM의 `c:\buildArtifacts\index.html` index.md를에 복사 합니다.

를 `buildTimeoutInMinutes`지정할 수도 있습니다. 기본값은 240 분 이며 빌드 시간을 더 오래 실행 하면 빌드 시간을 늘릴 수 있습니다.

샘플. json 템플릿을 사용 하 여 이미지를 구성 합니다. 사용 중인. json 파일은 [helloImageTemplateWin](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)입니다. 


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

## <a name="set-variables"></a>변수 설정

일부 정보를 반복 해 서 사용 하 게 되며,이 정보를 저장 하는 몇 가지 변수를 만듭니다.


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

구독 ID에 대 한 변수를 만듭니다. 을 사용 하 여 `az account show | grep id`이를 가져올 수 있습니다.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기
이 리소스 그룹은 이미지 구성 템플릿 아티팩트와 이미지를 저장 하는 데 사용 됩니다.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>리소스 그룹에 대 한 사용 권한 설정

이미지 작성기에서 리소스 그룹에 이미지를 만들 수 있는 권한을 부여 합니다. 이를 사용 하지 않으면 이미지 빌드가 실패 합니다. 

`--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>이미지 구성 템플릿 예제 다운로드

매개 변수가 있는 이미지 구성 템플릿을 사용해 볼 수 있습니다. 예제 json 파일을 다운로드 하 고 이전에 설정한 변수를 사용 하 여 구성 합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

터미널에서와 같은 `vi`텍스트 편집기를 사용 하 여이 예제를 수정할 수 있습니다.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 원본 이미지의 경우 항상 [버전을 지정](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)해야 합니다 .를 사용할 `latest`수 없습니다.
> 이미지가 배포 되는 리소스 그룹을 추가 하거나 변경 하는 경우 리소스 그룹에 대 한 [사용 권한을 설정](#set-permissions-on-the-resource-group) 해야 합니다.
 
## <a name="create-the-image"></a>이미지 만들기

VM 이미지 작성기 서비스에 이미지 구성 제출

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

완료 되 면 성공 메시지를 다시 콘솔로 반환 하 고 `Image Builder Configuration Template` 에서를 만듭니다. `$imageResourceGroup` ' 숨겨진 형식 표시 '를 사용 하도록 설정 하는 경우 Azure Portal의 리소스 그룹에서이 리소스를 볼 수 있습니다.

배경에서 이미지 작성기는 구독에 준비 리소스 그룹도 만듭니다. 이 리소스 그룹은 이미지 빌드에 사용 됩니다. 다음 형식이 됩니다.`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 스테이징 리소스 그룹을 직접 삭제 하면 안 됩니다. 먼저 이미지 템플릿 아티팩트를 삭제 합니다. 이렇게 하면 준비 리소스 그룹이 삭제 됩니다.

이미지 구성 템플릿 전송 중에 서비스에서 오류를 보고 하면 다음을 수행 합니다.
-  이러한 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 단계를 검토 합니다. 
- 제출을 다시 시도 하기 전에 다음 코드 조각을 사용 하 여 템플릿을 삭제 해야 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>이미지 빌드를 시작 합니다.
[Az resource invoke 작업](/cli/azure/resource#az-resource-invoke-action)을 사용 하 여 이미지 작성 프로세스를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

빌드가 완료 될 때까지 기다립니다. 이는 약 15 분 정도 걸릴 수 있습니다.

오류가 발생 하는 경우 다음 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) 단계를 검토 하세요.


## <a name="create-the-vm"></a>VM 만들기

빌드한 이미지를 사용 하 여 VM을 만듭니다. * \<암호>* 를 VM `aibuser` 의에 대 한 사용자 고유의 암호로 바꿉니다.

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

VM을 만들 때 설정한 사용자 이름 및 암호를 사용 하 여 VM에 대 한 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트를 열고 다음을 입력 합니다.

```console
dir c:\
```

이미지를 사용자 지정 하는 동안 생성 되는 두 개의 디렉터리를 확인 해야 합니다.
- buildActions
- buildArtifacts

## <a name="clean-up"></a>정리

작업이 완료 되 면 리소스를 삭제 합니다.

### <a name="delete-the-image-builder-template"></a>이미지 작성기 템플릿 삭제

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>이미지 리소스 그룹을 삭제 합니다.

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에 사용 된 json 파일의 구성 요소에 대해 자세히 알아보려면 [이미지 작성기 템플릿 참조](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.
