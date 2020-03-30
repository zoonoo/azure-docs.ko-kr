---
title: 블록 Blob 저장소 계정 만들기 - Azure 저장소 | 마이크로 소프트 문서
description: 프리미엄 성능 특성을 가진 Azure BlockBlobStorage 계정을 만드는 방법을 보여 주면 됩니다.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536907"
---
# <a name="create-a-blockblobstorage-account"></a>블록블블스토리지 계정 만들기

BlockBlobStorage 계정 종류를 사용하면 성능 특성이 높은 블록 Blob을 만들 수 있습니다. 이러한 유형의 저장소 계정은 트랜잭션 속도가 높거나 액세스 시간이 매우 빠른 워크로드에 최적화되어 있습니다. 이 문서에서는 Azure 포털, Azure CLI 또는 Azure PowerShell을 사용하여 BlockBlobStorage 계정을 만드는 방법을 보여 주며 있습니다.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

BlockBlobStorage 계정에 대한 자세한 내용은 [Azure 저장소 계정 개요를](https://docs.microsoft.com/azure/storage/common/storage-account-overview)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

없음

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

이 방법 문서에는 Azure PowerShell 모듈 Az 버전 1.2.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

- Azure 클라우드 셸에서 Azure 포털 내에서 CLI 명령을 실행할 수 있습니다.
- CLI를 설치하고 CLI 명령을 로컬로 실행할 수 있습니다.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI는 사전 설치되고 계정과 함께 사용하도록 구성됩니다. Azure 포털의 오른쪽 위 섹션의 메뉴에서 **클라우드 셸** 단추를 클릭합니다.

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

단추는 이 방법 문서에서 설명한 단계를 실행하는 데 사용할 수 있는 대화형 셸을 시작합니다.

[![포털에서 클라우드 셸 창을 보여주는 스크린샷](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

Azure CLI를 로컬에서 설치하여 사용할 수도 있습니다. 이 방법 문서에서는 Azure CLI 버전 2.0.46 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

---

## <a name="sign-in-to-azure"></a>Azure에 로그인

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure 포털에](https://portal.azure.com)로그인합니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

`Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure 클라우드 셸을 시작하려면 [Azure 포털에](https://portal.azure.com)로그인합니다.

CLI의 로컬 설치에 로그인하려면 [az 로그인](/cli/azure/reference-index#az-login) 명령을 실행합니다.

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>블록블블스토리지 계정 만들기

## <a name="portal"></a>[포털](#tab/azure-portal)
Azure 포털에서 BlockBlobStorage 계정을 만들려면 다음 단계를 따르십시오.

1. Azure 포털에서 **저장소** 범주 > 저장소 **계정에**> **모든 서비스를** 선택합니다.

1. **저장소 계정에서** **추가를**선택합니다.

1. **구독** 필드에서 저장소 계정을 만들 구독을 선택합니다.

1. 리소스 **그룹** 필드에서 기존 리소스 그룹을 선택하거나 새 정보 **만들기를**선택하고 새 리소스 그룹에 대한 이름을 입력합니다.

1. 저장소 **계정 이름** 필드에 계정의 이름을 입력합니다. 다음 지침을 참고하십시오.

   - 이름은 Azure에서 고유해야 합니다.
   - 이름은 3자에서 24자 사이여야 합니다.
   - 이름에는 숫자와 소문자만 포함될 수 있습니다.

1. **위치** 필드에서 저장소 계정의 위치를 선택하거나 기본 위치를 사용합니다.

1. 나머지 설정의 경우 다음을 구성합니다.

   |필드     |값  |
   |---------|---------|
   |**성능**    |  **프리미엄**을 선택합니다.   |
   |**계정 종류**    | **블록BlobStorage를**선택합니다.      |
   |**복제**    |  **LRS(로컬 중복 저장소)의**기본 설정을 그대로 둡니다.      |

   ![포털 UI를 표시하여 블록 Blob 저장소 계정을 만듭니다.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. **검토 + 만들기를** 선택하여 저장소 계정 설정을 검토합니다.

1. **만들기**를 선택합니다.

## <a name="azure-powershell"></a>[Azure 전원 셸](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 높은 Windows PowerShell 세션을 엽니다(관리자로 실행).

1. 다음 명령을 실행하여 최신 버전의 `Az` PowerShell 모듈이 설치되어 있는지 확인합니다.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 새 PowerShell 콘솔을 열고 Azure 계정으로 로그인합니다.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 필요한 경우 새 리소스 그룹을 만듭니다. 따옴표의 값을 바꾸고 다음 명령을 실행합니다.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. BlockBlobStorage 계정을 만듭니다. 따옴표의 값을 바꾸고 다음 명령을 실행합니다.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 블록 Blob 계정을 만들려면 먼저 Azure CLI v를 설치해야 합니다. 2.0.46 이상 버전. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. Azure 구독에 로그인합니다.

   ```azurecli
   az login
   ```

1. 필요한 경우 새 리소스 그룹을 만듭니다. 대괄호(대괄호 포함)의 값을 바꾸고 다음 명령을 실행합니다.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. BlockBlobStorage 계정을 만듭니다. 대괄호(대괄호 포함)의 값을 바꾸고 다음 명령을 실행합니다.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>다음 단계

- 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)를 참조하세요.

- 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)참조하십시오.
