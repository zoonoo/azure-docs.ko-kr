---
title: Azure 이미지 빌더를 사용하여 Linux VM 만들기(미리 보기)
description: Azure 이미지 빌더를 사용하여 Linux VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066679"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>미리 보기: Azure 이미지 빌더를 사용하여 Linux VM 만들기

이 문서에서는 Azure 이미지 빌더 및 Azure CLI를 사용하여 사용자 지정된 Linux 이미지를 만드는 방법을 보여 주며 있습니다. 이 문서의 예제에서는 이미지를 사용자 지정하기 위해 세 가지 다른 [사용자 지정자를](image-builder-json.md#properties-customize) 사용합니다.

- 셸(ScriptUri) - [셸 스크립트를](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)다운로드하고 실행합니다.
- 셸(인라인) - 특정 명령을 실행합니다. 이 예제에서 인라인 명령에는 디렉터리 만들기 및 OS 업데이트가 포함됩니다.
- 파일 - [GitHub에서 VM의](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 디렉터리로 파일을 복사합니다.

`buildTimeoutInMinutes`을 지정할 수도 있습니다. 기본값은 240분이며 빌드 시간을 늘려 빌드를 더 오래 실행할 수 있도록 할 수 있습니다.

샘플 .json 템플릿을 사용하여 이미지를 구성합니다. 우리가 사용하는 .json 파일은 여기 : [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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
```

등록을 하지 않는 경우 다음을 실행합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>설정 예제 변수

일부 정보를 반복적으로 사용하므로 해당 정보를 저장하는 몇 가지 변수를 만듭니다.


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

구독 ID에 대한 변수를 만듭니다. 을 사용하여 `az account show | grep id`이 것을 얻을 수 있습니다.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>리소스 그룹을 만듭니다.
이미지 구성 템플릿 아티팩트와 이미지를 저장하는 데 사용됩니다.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>리소스 그룹에 대한 사용 권한 설정
이미지 작성기 '기여자' 권한을 부여하여 리소스 그룹에서 이미지를 만들 수 있습니다. 적절한 사용 권한이 없으면 이미지 빌드가 실패합니다. 

값은 `--assignee` 이미지 빌더 서비스의 앱 등록 ID입니다. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>템플릿 예제 다운로드

사용할 매개 변수화된 샘플 이미지 구성 템플릿이 만들어졌습니다. 샘플 .json 파일을 다운로드하고 이전에 설정한 변수로 구성합니다.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

필요에 따라 이 예제 .json을 수정할 수 있습니다. 예를 들어 더 긴 실행 `buildTimeoutInMinutes` 빌드를 허용하도록 값을 늘릴 수 있습니다. 와 같은 `vi`텍스트 편집기에서 클라우드 셸에서 파일을 편집할 수 있습니다.

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 원본 이미지의 경우 항상 [버전을 지정해야](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure) `latest`합니다.
>
> 이미지가 배포되는 리소스 그룹을 추가하거나 변경하는 경우 [리소스 그룹에 대한 사용 권한이 설정되어](#set-permissions-on-the-resource-group)있는지 확인해야 합니다.


## <a name="submit-the-image-configuration"></a>이미지 구성 제출
VM 이미지 빌더 서비스에 이미지 구성 제출

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

성공적으로 완료되면 성공 메시지를 반환하고 $imageResourceGroup 이미지 빌더 구성 템플릿 아티팩트를 만듭니다. '숨겨진 형식 표시'를 사용하도록 설정하면 포털에서 리소스 그룹을 볼 수 있습니다.

또한 백그라운드에서 이미지 빌더는 구독에서 준비 리소스 그룹을 만듭니다. 이미지 작성기는 이미지 빌드에 스테이징 리소스 그룹을 사용합니다. 리소스 그룹의 이름은 이 `IT_<DestinationResourceGroup>_<TemplateName>`형식입니다.

> [!IMPORTANT]
> 스테이징 리소스 그룹을 직접 삭제하지 마십시오. 이미지 템플릿 아티팩트를 삭제하면 스테이징 리소스 그룹이 자동으로 삭제됩니다. 자세한 내용은 이 문서의 끝에 있는 [정리](#clean-up) 섹션을 참조하십시오.

서비스에서 이미지 구성 템플릿 제출 중에 오류가 보고되면 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 단계를 참조하세요. 빌드를 다시 제출하기 전에 템플릿을 삭제해야 합니다. 템플릿을 삭제하려면 다음을 수행합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>이미지 빌드 시작

이미지 빌드를 시작합니다.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

빌드가 완료될 때까지 기다립니다(이 예제에서는 10-15분)가 걸릴 수 있습니다.

오류가 발생하면 다음 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) 단계를 검토하십시오.


## <a name="create-the-vm"></a>VM 만들기

작성한 이미지를 사용하여 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM을 만드는 출력에서 IP 주소를 가져옵니다 및 VM에 SSH에 사용.

```bash
ssh azureuser@<pubIp>
```

SSH 연결이 설정되는 즉시 이미지가 오늘의 메시지로 사용자 정의 된 것을 볼 수 있습니다!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

SSH 연결을 닫으려면 입력합니다. `exit`

## <a name="check-the-source"></a>소스 확인

이미지 빌더 템플릿에서 '속성'에는 소스 이미지, 실행되는 사용자 지정 스크립트 및 배포위치가 표시됩니다.

```bash
cat helloImageTemplateLinux.json
```

이 .json 파일에 대한 자세한 내용은 [이미지 빌더 템플릿 참조를](image-builder-json.md) 참조하십시오.

## <a name="clean-up"></a>정리

완료되면 리소스를 삭제할 수 있습니다.

이미지 빌더 템플릿을 삭제합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

이미지 리소스 그룹을 삭제합니다.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에서 사용되는 .json 파일의 구성 요소에 대한 자세한 내용은 [이미지 작성기 템플릿 참조를](image-builder-json.md)참조하십시오.
