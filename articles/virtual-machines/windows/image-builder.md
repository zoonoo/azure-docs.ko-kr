---
title: Azure 이미지 작성기 (미리 보기)를 사용 하 여 Windows VM 만들기
description: Azure 이미지 작성기를 사용 하 여 Windows VM을 만듭니다.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 62d80426dec6f5d63d8fa5d67d64d6aafb881110
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320016"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>미리 보기: Azure 이미지 작성기를 사용 하 여 Windows VM 만들기

이 문서에서는 Azure VM 이미지 작성기를 사용 하 여 사용자 지정 된 Windows 이미지를 만드는 방법을 보여 줍니다. 이 문서의 예제에서는 [이미지를 사용자 지정 하기 위해](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) 사용자 지정을 사용 합니다.
- PowerShell (ScriptUri)- [powershell 스크립트](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)를 다운로드 하 여 실행 합니다.
- Windows 다시 시작-VM을 다시 시작 합니다.
- PowerShell (인라인)-특정 명령을 실행 합니다. 이 예제에서는를 사용 하 여 VM에 디렉터리를 만듭니다 `mkdir c:\\buildActions` .
- 파일-GitHub의 파일을 VM에 복사 합니다. 이 예제에서는 VM의 [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 를에 복사 `c:\buildArtifacts\index.html` 합니다.
- buildTimeoutInMinutes-빌드 시간을 늘려 더 긴 빌드를 허용 하 고, 기본값은 240 분 이며, 빌드 시간을 더 오래 실행 하면 빌드 시간을 늘릴 수 있습니다.
- vmProfile-Vmprofile 및 네트워크 속성 지정
- osDiskSizeGB-이미지 크기를 늘릴 수 있습니다.
- id-빌드 중에 사용할 Azure 이미지 작성기의 id 제공


를 지정할 수도 있습니다 `buildTimeoutInMinutes` . 기본값은 240 분 이며 빌드 시간을 더 오래 실행 하면 빌드 시간을 늘릴 수 있습니다.

이미지를 구성하는 데 샘플 .json 템플릿을 사용합니다. 사용 중인. json 파일은 [helloImageTemplateWin.js에](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)있습니다. 


> [!IMPORTANT]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="register-the-features"></a>기능 등록

미리 보기 중에 Azure Image Builder를 사용하려면 이 새 기능을 등록해야 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

기능 등록 상태를 확인합니다.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

등록을 확인합니다.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

등록되지 않은 경우 다음을 실행합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables"></a>변수 설정

일부 정보를 반복해서 사용하게 되며, 해당 정보를 저장하기 위해 몇 가지 변수를 만듭니다.


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

구독 ID에 대한 변수를 만듭니다. `az account show | grep id`를 사용하여 만들 수 있습니다.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기
이 리소스 그룹은 이미지 구성 템플릿 아티팩트와 이미지를 저장 하는 데 사용 됩니다.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>사용자 할당 ID 만들기 및 리소스 그룹에 대한 사용 권한 설정
이미지 작성기는 제공 된 [사용자 id](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) 를 사용 하 여 리소스 그룹에 이미지를 삽입 합니다. 이 예제에서는 이미지 배포를 수행 하는 세분화 된 작업을 포함 하는 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>사용자 할당 관리 id 만들기 및 사용 권한 부여 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
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
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

터미널에서와 같은 텍스트 편집기를 사용 하 여이 예제를 수정할 수 있습니다 `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> 원본 이미지의 경우 항상 [버전을 지정](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)해야 합니다 .를 사용할 수 없습니다 `latest` .
> 이미지가 배포 되는 리소스 그룹을 추가 하거나 변경 하는 경우 리소스 그룹에 대 한 [사용 권한을 설정](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) 해야 합니다.
 
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

완료 되 면 성공 메시지를 다시 콘솔로 반환 하 고에서를 만듭니다 `Image Builder Configuration Template` `$imageResourceGroup` . ' 숨겨진 형식 표시 '를 사용 하도록 설정 하는 경우 Azure Portal의 리소스 그룹에서이 리소스를 볼 수 있습니다.

배경에서 이미지 작성기는 구독에 준비 리소스 그룹도 만듭니다. 이 리소스 그룹은 이미지 빌드에 사용 됩니다. 다음 형식이 됩니다. `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 스테이징 리소스 그룹을 직접 삭제 하면 안 됩니다. 먼저 이미지 템플릿 아티팩트를 삭제 합니다. 이렇게 하면 준비 리소스 그룹이 삭제 됩니다.

이미지 구성 템플릿 전송 중에 서비스에서 오류를 보고 하면 다음을 수행 합니다.
-  이러한 [문제 해결](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 단계를 검토 합니다. 
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

오류가 발생 하는 경우 다음 [문제 해결](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) 단계를 검토 하세요.


## <a name="create-the-vm"></a>VM 만들기

빌드한 이미지를 사용 하 여 VM을 만듭니다. 를 *\<password>* VM의에 대 한 사용자 고유의 암호로 바꿉니다 `aibuser` .

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

VM을 만들 때 설정한 사용자 이름 및 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트를 열고 다음을 입력합니다.

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

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>역할 할당, 역할 정의 및 사용자 id를 삭제 합니다.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>이미지 리소스 그룹을 삭제 합니다.

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에 사용 된 json 파일의 구성 요소에 대해 자세히 알아보려면 [이미지 작성기 템플릿 참조](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.
