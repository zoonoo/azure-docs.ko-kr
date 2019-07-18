---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 Linux VM 만들기
description: Azure 이미지 작성기를 사용 하 여 Linux VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667519"
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

## <a name="setup-example-variables"></a>예제에서는 변수를 설정 합니다.

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

## <a name="create-the-resource-group"></a>리소스 그룹을 만듭니다.
이미지 구성 템플릿 아티팩트 및 이미지 저장에 사용 됩니다.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>리소스 그룹에 권한 설정
리소스 그룹에서 이미지 만들기 이미지 작성기 '참가자' 권한을 부여 합니다. 적절 한 권한이 없으면 이미지 빌드 실패 합니다. 

`--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>템플릿 예제를 다운로드 합니다.

매개 변수가 있는 샘플 이미지 구성 템플릿은 사용 하 여에 대 한 작성 되었습니다. 샘플.json 파일을 다운로드 하 고 앞에서 설정한 변수를 사용 하 여 구성 합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

이 예제에서는.json 필요에 따라 수정할 수 있습니다. 예를 들어,의 값을 늘리려면 `buildTimeoutInMinutes` 오래 실행 중인 빌드 수 있도록 합니다. 다음을 사용 하 여 Cloud Shell에서 파일을 편집할 수 `vi`입니다.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 원본 이미지를 사용 해야 항상 [버전을 지정](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)를 사용할 수 없습니다 `latest`합니다.
>
> 를 추가 하거나 리소스 그룹 이미지를 배포할 위치를 변경 하는 경우 확인 해야 합니다 [리소스 그룹에 대 한 권한이 설정 된](#set-permissions-on-the-resource-group)합니다.


## <a name="submit-the-image-configuration"></a>이미지 구성을 제출합니다
이미지 구성을 VM 이미지 작성기 서비스에 제출

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

성공적으로 완료 되 면 성공 메시지가 반환 되며는 $imageResourceGroup에서 이미지 작성기 구성 템플릿 아티팩트를 만듭니다. '숨겨진된 형식 표시'를 사용 하는 경우 포털에서 리소스 그룹을 볼 수 있습니다.

또한 백그라운드 이미지 작성기 구독의 준비 리소스 그룹을 만듭니다. 이미지 작성기 이미지 빌드에 대 한 준비 리소스 그룹을 사용합니다. 리소스 그룹의 이름을이 형식이 됩니다. `IT_<DestinationResourceGroup>_<TemplateName>`합니다.

> [!IMPORTANT]
> 준비 리소스 그룹을 직접 삭제 하지 마세요. 이미지 서식 파일 아티팩트를 삭제 하면 준비 리소스 그룹을 자동으로 삭제 됩니다. 자세한 내용은 참조는 [정리](#clean-up) 이 문서의 끝에 있는 섹션입니다.

이미지 구성 템플릿 제출 하는 동안 오류를 보고 하는 서비스를 표시 합니다 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 단계입니다. 또한 빌드를 제출 합니다. 다시 시도 하기 전에 템플릿을 삭제 해야 합니다. 템플릿을 삭제:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>이미지 빌드를 시작 합니다.

이미지 빌드를 시작 합니다.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

대기할 빌드를이 예제에 대 한 완료 될 때까지 10-15 분 정도 걸릴 수 있습니다.

오류가 발생할 경우 살펴보시기 이러한 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) 단계입니다.


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

완료 되 면 리소스를 삭제할 수 있습니다.

이미지 작성기 템플릿을 삭제 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

이미지 리소스 그룹을 삭제 합니다.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에서 사용 하는.json 파일의 구성 요소에 대 한 자세한 내용은 참조 하세요 [Image Builder 템플릿 참조](image-builder-json.md)합니다.
