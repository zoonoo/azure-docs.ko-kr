---
title: 블록 Blob 스토리지 계정 만들기 - Azure Storage | Microsoft Docs
description: 프리미엄 성능 특성을 사용하여 Azure BlockBlobStorage 계정을 만드는 방법을 보여 줍니다.
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/30/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9350f9aeff90b75a4e1362f6fa2fa1b0d07f20cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997089"
---
# <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage 계정 만들기

BlockBlobStorage 계정 종류를 사용하면 프리미엄 성능 특성을 포함하는 블록 Blob을 만들 수 있습니다. 이 유형의 스토리지 계정은 트랜잭션 속도가 높거나 매우 빠른 액세스 시간이 필요한 워크로드에 최적화되어 있습니다. 이 문서에서는 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 BlockBlobStorage 계정을 만드는 방법을 보여 줍니다.

BlockBlobStorage 계정에 대한 자세한 내용은 [Azure 스토리지 계정 개요](../common/storage-account-overview.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

없음

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 방법 문서에는 Azure PowerShell 모듈 Az 버전 1.2.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

- Azure Portal 내에서 Azure Cloud Shell을 사용하여 CLI 명령을 실행합니다.
- CLI를 설치하고 로컬로 CLI를 실행합니다.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성됩니다. Azure Portal의 오른쪽 위 섹션에 있는 메뉴에서 **Cloud Shell** 단추를 클릭합니다.

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

이 단추를 누르면 이 방법 문서에 간략히 설명된 단계를 실행하는 데 사용할 수 있는 대화형 셸이 시작됩니다.

[![포털에서 Cloud Shell 창을 보여 주는 스크린샷](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

Azure CLI를 로컬에서 설치하여 사용할 수도 있습니다. 이 방법 문서를 사용하려면 Azure CLI 버전 2.0.46 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

---

## <a name="sign-in-to-azure"></a>Azure에 로그인

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)에 로그인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

CLI의 로컬 설치에 로그인하려면 [az login](/cli/azure/reference-index#az-login) 명령을 실행합니다.

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage 계정 만들기

## <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal에서 BlockBlobStorage 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모든 서비스** > **스토리지** 범주 > **스토리지 계정** 을 선택합니다.

2. **스토리지 계정** 에서 **추가** 를 선택합니다.

3. **구독** 필드에서 스토리지 계정을 만들 구독을 선택합니다.

4. **리소스 그룹** 필드에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하고 새 리소스 그룹의 이름을 입력합니다.

5. **스토리지 계정 이름** 필드에 계정 이름을 입력합니다. 다음 지침을 참조하세요.

   - 이름은 Azure에서 고유해야 합니다.
   - 이름은 3~24자여야 합니다.
   - 이름은 숫자와 소문자만 포함할 수 있습니다.

6. **위치** 필드에서 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.

7. 나머지 설정에 대해 다음을 구성합니다.

   |필드     |값  |
   |---------|---------|
   |**성능**    |  **프리미엄** 을 선택합니다.   |
   |**계정 종류**    | **BlockBlobStorage** 를 선택합니다.      |
   |**복제**    |  **LRS(로컬 중복 스토리지)** 의 기본 설정을 그대로 둡니다.      |

   ![블록 Blob 스토리지 계정을 만들기 위한 포털 UI를 표시](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. **고급** 탭을 선택합니다.

9. 데이터 분석을 위해 스토리지 계정을 최적화하려면 **계층 구조 네임스페이스** 를 **사용** 으로 설정합니다. 그러지 않으면 이 옵션을 기본값으로 설정된 채로 둡니다. BlockBlobStorage 계정으로 이 설정을 사용하도록 설정하면 [Data Lake Storage 프리미엄 계층](premium-tier-for-data-lake-storage.md)이 제공됩니다.  Data Lake Storage에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)를 참조하세요.

8. **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토합니다.

9. **만들기** 를 선택합니다.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 관리자 권한 Windows PowerShell 세션을 엽니다(관리자 권한으로 실행).

2. 다음 명령을 실행하여 최신 버전의 `Az` PowerShell 모듈이 설치되어 있는지 확인합니다.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. 새 PowerShell 콘솔을 열고 Azure 계정으로 로그인합니다.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. 필요한 경우 새 리소스 그룹을 만듭니다. 따옴표 안의 값을 바꾸고 다음 명령을 실행합니다.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. BlockBlobStorage 계정을 만듭니다. 따옴표 안의 값을 바꾸고 다음 명령을 실행합니다.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   데이터 분석을 위해 스토리지 계정을 최적화하려면 명령에 `-EnableHierarchicalNamespace $True`를 추가합니다. BlockBlobStorage 계정으로 이 설정을 사용하도록 설정하면 [Data Lake Storage 프리미엄 계층](premium-tier-for-data-lake-storage.md)이 제공됩니다.  Data Lake Storage에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)를 참조하세요.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 블록 Blob 계정을 만들려면 먼저 Azure CLI 버전 2.0.46 이상을 설치해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. Azure 구독에 로그인합니다.

   ```azurecli
   az login
   ```

2. 필요한 경우 새 리소스 그룹을 만듭니다. 대괄호 안의 값(대괄호 포함)을 바꾸고 다음 명령을 실행합니다.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. BlockBlobStorage 계정을 만듭니다. 대괄호 안의 값(대괄호 포함)을 바꾸고 다음 명령을 실행합니다.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   데이터 분석을 위해 스토리지 계정을 최적화하려면 명령에 `--hierarchical-namespace true`를 추가합니다. BlockBlobStorage 계정으로 이 설정을 사용하도록 설정하면 [Data Lake Storage 프리미엄 계층](premium-tier-for-data-lake-storage.md)이 제공됩니다.  Data Lake Storage에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

- 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

- 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.