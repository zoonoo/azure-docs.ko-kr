---
title: Azure CLI를 사용 하 여 Azure 이미지 작성기 서비스 사용 권한 구성
description: Azure CLI를 사용 하 여 사용 권한 및 권한을 포함 하는 Azure VM 이미지 작성기 서비스에 대 한 요구 사항 구성
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 58bbe01c8de0bbe606f4fc428032cd213f05d386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88068239"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 이미지 작성기 서비스 사용 권한 구성

Azure 이미지 작성기 서비스를 사용 하려면 이미지를 빌드하기 전에 권한 및 권한을 구성 해야 합니다. 다음 섹션에서는 Azure CLI를 사용 하 여 가능한 시나리오를 구성 하는 방법에 대해 자세히 설명 합니다.

> [!IMPORTANT]
> Azure Image Builder는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>기능 등록

먼저, Azure 이미지 작성기 서비스에 등록 해야 합니다. 등록은 스테이징 리소스 그룹을 만들고, 관리 하 고, 삭제할 수 있는 권한을 서비스에 부여 합니다. 또한이 서비스에는 이미지 빌드에 필요한 그룹에 리소스를 추가할 수 있는 권한이 있습니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure 사용자 할당 관리 id 만들기

Azure 이미지 작성기를 사용 하려면 [azure 사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 만들어야 합니다. Azure 이미지 작성기는 사용자 할당 관리 id를 사용 하 여 이미지를 읽고, 이미지를 쓰고, Azure storage 계정에 액세스 합니다. 구독에서 특정 작업을 수행할 수 있는 id 권한을 부여 합니다.

> [!NOTE]
> 이전에는 Azure 이미지 작성기에서 Azure 이미지 작성기 SPN (서비스 사용자 이름)을 사용 하 여 이미지 리소스 그룹에 대 한 사용 권한을 부여 했습니다. SPN을 사용 하는 것은 더 이상 사용 되지 않습니다. 사용자 할당 관리 id를 대신 사용 합니다.

다음 예제에서는 Azure 사용자 할당 관리 id를 만드는 방법을 보여 줍니다. 자리 표시자 설정을 대체 하 여 변수를 설정 합니다.

| 설정 | Description |
|---------|-------------|
| \<Resource group\> | 사용자 할당 관리 id를 만들 리소스 그룹입니다. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Azure 사용자 할당 id에 대 한 자세한 내용은 id를 만드는 방법에 대 한 [azure 사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 설명서를 참조 하세요.

## <a name="allow-image-builder-to-distribute-images"></a>이미지 작성기에서 이미지를 배포 하도록 허용

Azure 이미지 작성기에서 이미지 (관리 되는 이미지/공유 이미지 갤러리)를 배포 하는 경우 Azure 이미지 작성기 서비스는 이러한 리소스 그룹에 이미지를 삽입할 수 있도록 허용 해야 합니다. 필요한 사용 권한을 부여 하려면 사용자 할당 관리 id를 만들고 이미지가 작성 된 리소스 그룹에 대 한 권한을 부여 해야 합니다. Azure 이미지 작성기에는 구독의 다른 리소스 그룹에 있는 리소스에 액세스할 수 있는 권한이 **없습니다** . 빌드에 실패 하는 것을 방지 하려면 액세스를 허용 하기 위해 명시적인 작업을 수행 해야 합니다.

리소스 그룹에 대 한 사용자 할당 관리 id 참가자 권한을 부여 하지 않아도 이미지를 배포할 수 있습니다. 그러나 사용자 할당 관리 id의 경우 `Actions` 배포 리소스 그룹에서 다음 Azure 권한이 필요 합니다.

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

공유 이미지 갤러리에 배포 하는 경우 다음도 필요 합니다.

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>기존 이미지를 사용자 지정할 수 있는 권한

Azure 이미지 작성기가 원본 사용자 지정 이미지 (관리 되는 이미지/공유 이미지 갤러리)에서 이미지를 빌드하려면 Azure 이미지 작성기 서비스에서 이러한 리소스 그룹으로 이미지를 읽을 수 있어야 합니다. 필요한 사용 권한을 부여 하려면 사용자 할당 관리 id를 만들고 이미지가 있는 리소스 그룹에 대 한 권한을 부여 해야 합니다.

기존 사용자 지정 이미지에서 빌드:

```Actions
Microsoft.Compute/galleries/read
```

기존 공유 이미지 갤러리 버전에서 빌드:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Vnet 이미지를 사용자 지정할 수 있는 권한

Azure 이미지 작성기에는 구독에서 기존 VNET을 배포 하 고 사용 하 여 사용자 지정이 연결 된 리소스에 액세스할 수 있도록 허용 하는 기능이 있습니다.

리소스 그룹에 대 한 사용자 할당 관리 id 참가자 권한을 부여 하지 않아도 기존 VNET에 VM을 배포할 수 있습니다. 그러나 사용자 할당 관리 id에는 `Actions` VNET 리소스 그룹에 대 한 다음 Azure 권한이 필요 합니다.

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure 역할 정의 만들기

다음 예제에서는 이전 섹션에서 설명한 작업에서 Azure 역할 정의를 만듭니다. 예제는 리소스 그룹 수준에서 적용 됩니다. 사용자 요구 사항에 따라 예가 충분 한지 평가 하 고 테스트 합니다. 시나리오의 경우 특정 공유 이미지 갤러리에 구체화 해야 할 수 있습니다.

이미지 작업은 읽기 및 쓰기를 허용 합니다. 사용자 환경에 적합 한 항목을 결정 합니다. 예를 들어 Azure 이미지 작성기가 리소스 그룹 *예제-rg-1* 에서 이미지를 읽고 리소스 그룹에 이미지를 쓰도록 허용 하는 역할을 만듭니다 (예: *rg-2*).

### <a name="custom-image-azure-role-example"></a>사용자 지정 이미지 Azure 역할 예제

다음 예제에서는 원본 사용자 지정 이미지를 사용 하 고 배포 하는 Azure 역할을 만듭니다. 그런 다음 Azure 이미지 작성기의 사용자 할당 관리 id에 사용자 지정 역할을 부여 합니다.

예제의 값을 간단 하 게 바꾸기 위해 다음 변수를 먼저 설정 합니다. 자리 표시자 설정을 대체 하 여 변수를 설정 합니다.

| 설정 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | 사용자 지정 이미지에 대 한 리소스 그룹 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>기존 VNET Azure 역할 예제

다음 예제에서는 기존 VNET 이미지를 사용 하 고 배포 하는 Azure 역할을 만듭니다. 그런 다음 Azure 이미지 작성기의 사용자 할당 관리 id에 사용자 지정 역할을 부여 합니다.

예제의 값을 간단 하 게 바꾸기 위해 다음 변수를 먼저 설정 합니다. 자리 표시자 설정을 대체 하 여 변수를 설정 합니다.

| 설정 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | VNET 리소스 그룹 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Azure Storage 액세스에 관리 id 사용

Azure Storage 인증을 원활 하 고 개인 컨테이너를 사용 하려는 경우 azure에 사용자 할당 관리 id가 필요 합니다. Azure 이미지 작성기는 id를 사용 하 여 Azure Storage 인증 합니다.

> [!NOTE]
> Azure 이미지 작성기는 이미지 템플릿 전송 시간에만 id를 사용 합니다. 빌드 VM은 이미지 빌드 중에 id에 액세스할 수 없습니다.

Azure CLI를 사용 하 여 사용자 할당 관리 id를 만듭니다.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

이미지 작성기 템플릿에서 사용자 할당 관리 id를 제공 해야 합니다.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

다음 자리 표시자 설정을 바꿉니다.

| 설정 | Description |
|---------|-------------|
| \<Region\> | 템플릿 영역 |
| \<Resource group\> | Resource group |
| \<Storage account container\> | 스토리지 계정 컨테이너 이름 |
| \<Subscription ID\> | Azure 구독 |

사용자 할당 관리 id를 사용 하는 방법에 대 한 자세한 내용은 [Azure User-Assigned 관리 id를 사용 하 여 Azure Storage 액세스 파일을 원활 하는 사용자 지정 이미지 만들기](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage)를 참조 하세요. 빠른 시작은 사용자 할당 관리 id를 만들고 구성 하 여 저장소 계정에 액세스 하는 방법을 안내 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 이미지 작성기 개요](image-builder-overview.md)를 참조 하세요.