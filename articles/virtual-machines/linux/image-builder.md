---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 Linux VM 만들기
description: Azure 이미지 작성기를 사용 하 여 Linux VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159512"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여 Linux VM 만들기

이 문서에서는 Azure Image Builder 및 Azure CLI를 사용 하 여 사용자 지정된 Linux 이미지를 만드는 방법을 보여 줍니다. 이 문서의 예제에서는 세 가지 [커스터마이저가](image-builder-json.md#properties-customize) 이미지를 사용자 지정 합니다.

- 셸 (ScriptUri)-다운로드 및 실행을 [셸 스크립트](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)합니다.
- 셸 (인라인)-특정 명령 실행 합니다. 이 예제에서는 인라인 명령에는 디렉터리를 만들고 OS를 업데이트 합니다.
- 파일 복사본을 [GitHub에서 파일](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 디렉터리 VM에 있습니다.

샘플.json 템플릿 이미지를 구성 하려면 사용 합니다. 사용 하 여.json 파일은 여기: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json)합니다. 

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
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
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
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>이미지 만들기
이미지 구성을 VM 이미지 작성기 서비스에 제출

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

이미지 빌드를 시작 합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

빌드가 완료 될 때까지 기다립니다. 약 15 분 정도 걸릴 수 있습니다.


## <a name="create-the-vm"></a>VM 만들기

만든 이미지를 사용 하 여 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM 만들기의 출력에서 IP 주소를 가져오고 VM에 SSH를 사용 하 여 합니다.

```azurecli-interactive
ssh azureuser@<pubIp>
```

하루에 SSH 연결이 설정 되는 즉시 메시지를 사용 하 여 이미지를 사용자 지정한 표시 됩니다.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

형식 `exit` 했으면 SSH 연결을 닫습니다.

## <a name="check-the-source"></a>원본을 확인 합니다.

이미지 작성기 템플릿에서 '속성'에서 소스 이미지 표시 됩니다, 사용자 지정 스크립트 실행 및 배포 됩니다.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

이.json 파일에 대 한 정보를 자세한 참조 [이미지 작성기 템플릿 참조](image-builder-json.md)

## <a name="clean-up"></a>정리

완료 되 면 리소스를 삭제 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에서 사용 하는.json 파일의 구성 요소에 대 한 자세한 내용은 참조 하세요 [Image Builder 템플릿 참조](image-builder-json.md)합니다.