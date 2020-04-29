---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 Linux VM 만들기
description: Azure 이미지 작성기를 사용 하 여 Linux VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066679"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여 Linux VM 만들기

이 문서에서는 Azure 이미지 작성기 및 Azure CLI를 사용 하 여 사용자 지정 된 Linux 이미지를 만드는 방법을 보여 줍니다. 이 문서의 예제에서는 이미지를 사용자 지정 하는 세 [가지 다른 사용자 지정을 사용](image-builder-json.md#properties-customize) 합니다.

- Shell (ScriptUri)- [셸 스크립트](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)를 다운로드 하 여 실행 합니다.
- Shell (인라인)-특정 명령을 실행 합니다. 이 예제에서 인라인 명령에는 디렉터리를 만들고 OS를 업데이트 하는 작업이 포함 됩니다.
- 파일- [GitHub의 파일](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 을 VM의 디렉터리로 복사 합니다.

를 `buildTimeoutInMinutes`지정할 수도 있습니다. 기본값은 240 분 이며 빌드 시간을 더 오래 실행 하면 빌드 시간을 늘릴 수 있습니다.

샘플. json 템플릿을 사용 하 여 이미지를 구성 합니다. 사용 중인. json 파일은 [helloImageTemplateLinux](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json)입니다. 

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

## <a name="setup-example-variables"></a>설정 예제 변수

일부 정보를 반복 해 서 사용 하 게 되며,이 정보를 저장 하는 몇 가지 변수를 만듭니다.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

구독 ID에 대 한 변수를 만듭니다. 을 사용 하 여 `az account show | grep id`이를 가져올 수 있습니다.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>리소스 그룹을 만듭니다.
이미지 구성 템플릿 아티팩트와 이미지를 저장 하는 데 사용 됩니다.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>리소스 그룹에 대 한 사용 권한 설정
이미지 작성기에서 리소스 그룹에 이미지를 만들 수 있는 권한을 부여 합니다. 적절 한 권한이 없으면 이미지 빌드가 실패 합니다. 

`--assignee` 값은 이미지 작성기 서비스에 대 한 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>템플릿 예제 다운로드

매개 변수가 있는 샘플 이미지 구성 템플릿을 사용할 수 있도록 만들었습니다. 샘플 json 파일을 다운로드 하 고 이전에 설정한 변수로 구성 합니다.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

필요에 따라이 예제의 json을 수정할 수 있습니다. 예를 들어의 `buildTimeoutInMinutes` 값을 늘려 더 오래 실행 되는 빌드를 허용할 수 있습니다. 와 같은 `vi`텍스트 편집기를 사용 하 여 Cloud Shell에서 파일을 편집할 수 있습니다.

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 원본 이미지의 경우 항상 버전을 [지정](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)해야 합니다 .를 사용할 `latest`수 없습니다.
>
> 이미지가 배포 되는 리소스 그룹을 추가 하거나 변경 하는 경우 [리소스 그룹에 대 한 사용 권한이 설정 되어](#set-permissions-on-the-resource-group)있는지 확인 해야 합니다.


## <a name="submit-the-image-configuration"></a>이미지 구성 제출
VM 이미지 작성기 서비스에 이미지 구성 제출

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

성공적으로 완료 되 면 성공 메시지를 반환 하 고 $imageResourceGroup에 이미지 작성기 구성 템플릿 아티팩트를 만듭니다. ' 숨겨진 형식 표시 '를 사용 하도록 설정 하면 포털에서 리소스 그룹을 볼 수 있습니다.

또한 백그라운드에서 이미지 작성기는 구독에 준비 리소스 그룹을 만듭니다. 이미지 작성기는 이미지 빌드에 대 한 준비 리소스 그룹을 사용 합니다. 리소스 그룹의 이름은 형식으로 지정 됩니다 `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> 스테이징 리소스 그룹을 직접 삭제 하지 마십시오. 이미지 템플릿 아티팩트를 삭제 하면 스테이징 리소스 그룹이 자동으로 삭제 됩니다. 자세한 내용은이 문서의 끝에 있는 [정리](#clean-up) 섹션을 참조 하세요.

이미지 구성 템플릿 전송 중에 서비스에서 오류를 보고 하는 경우 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 단계를 참조 하세요. 또한 빌드 제출을 다시 시도 하기 전에 템플릿을 삭제 해야 합니다. 템플릿을 삭제 하려면:

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

빌드가 완료 될 때까지 대기 합니다 .이 예제에서는 10-15 분 정도 걸릴 수 있습니다.

오류가 발생 하는 경우 다음 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) 단계를 검토 하세요.


## <a name="create-the-vm"></a>VM 만들기

빌드한 이미지를 사용 하 여 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM을 만들 때 출력에서 IP 주소를 가져와 VM에 대 한 SSH에 사용 합니다.

```bash
ssh azureuser@<pubIp>
```

SSH 연결이 설정 되는 즉시 이미지는 하루 메시지와 함께 사용자 지정 된 것을 볼 수 있습니다.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

SSH `exit` 연결을 닫을 때를 입력 합니다.

## <a name="check-the-source"></a>원본 확인

이미지 작성기 템플릿의 ' 속성 '에는 원본 이미지, 실행 하는 사용자 지정 스크립트 및 배포 되는 위치가 표시 됩니다.

```bash
cat helloImageTemplateLinux.json
```

이 json 파일에 대 한 자세한 내용은 [이미지 작성기 템플릿 참조](image-builder-json.md) 를 참조 하세요.

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

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에 사용 된 json 파일의 구성 요소에 대해 자세히 알아보려면 [이미지 작성기 템플릿 참조](image-builder-json.md)를 참조 하세요.
