---
title: Azure Storage-블록 blob storage 계정 만들기 | Microsoft Docs
description: 프리미엄 성능 특성을 사용 하 여 Azure 블록 blob 저장소 계정을 만드는 방법을 보여 줍니다.
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
author: twooley
ms.author: twooley
ms.subservice: blobs
ms.openlocfilehash: 16ecade7a8d0049e098bb06bd14828b19934f5f5
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373047"
---
# <a name="create-a-block-blob-storage-account"></a>블록 blob 저장소 계정 만들기

블록 blob storage 계정 유형을 premium 성능 특성을 사용 하 여 블록 blob을 만들 수 있습니다. 이 유형의 저장소 계정이 높은 트랜잭션 속도 사용 하 여 워크 로드에 최적화 된 또는 필요로 하는 매우 빠른 액세스 시간입니다. 이 문서에서는 Azure portal, Azure CLI 또는 Azure PowerShell을 사용 하 여 블록 blob 저장소 계정을 만드는 방법을 보여 줍니다.

블록 blob 저장소 계정에 대 한 자세한 내용은 참조 [Azure storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)합니다.

## <a name="create-account-in-the-azure-portal"></a>Azure portal에서 계정 만들기

Azure portal에서 블록 blob 저장소 계정을 만들려면 다음이 단계를 수행 합니다.

1. Azure portal에서 선택 **모든 서비스** >를 **저장소** 범주 > **저장소 계정**합니다.

1. 아래 **Storage 계정**를 선택 **추가**합니다.

1. 에 **구독** 필드, 저장소 계정을 만들 구독을 선택 합니다.

1. 에 **리소스 그룹** 필드에서는 기존 리소스 그룹을 선택 하거나 선택 **새로 만들기**, 새 리소스 그룹에 대 한 이름을 입력 합니다.

1. 에 **저장소 계정 이름** 필드에 계정의 이름을 입력 합니다. 다음 지침을 note:

   - 이름을은 Azure 전체에서 고유 해야 합니다.
   - 이름을 3 ~ 24 사이 여야 합니다. 자입니다.
   - 이름에는 숫자 및 소문자만 포함할 수 있습니다.

1. 에 **위치** 필드, 저장소 계정에 대 한 위치를 선택 하거나 기본 위치를 사용 합니다.

1. 나머지 설정에 대 한 다음 구성 합니다.

   |필드     |값  |
   |---------|---------|
   |**성능**    |  선택 **Premium**합니다.   |
   |**계정 종류**    | 선택 **BlockBlobStorage**합니다.      |
   |**복제**    |  기본 설정을 그대로 **로컬 중복 저장소 (LRS)** 합니다.      |

   ![포털 UI 블록 blob 저장소 계정 만들기를 보여 줍니다.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. 선택 **검토 + 만들기** 저장소 계정 설정을 검토 합니다.

1. **만들기**를 선택합니다.

### <a name="create-account-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 계정 만들기

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 관리자 권한 Windows PowerShell 세션 (관리자 권한으로 실행)를 엽니다.

1. 최신 버전이 있는지 확인 하려면 다음 명령을 실행 하는 `Az` PowerShell 모듈이 설치 됩니다.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 새 PowerShell 콘솔을 열고 로그인 Azure 계정으로 로그인 합니다.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 필요한 경우 새 리소스 그룹을 만듭니다. 다음 명령을 실행 하 고 따옴표를 값을 대체 합니다.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. 블록 blob 저장소 계정을 만듭니다. 다음 명령을 실행 하 고 따옴표를 값을 대체 합니다.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Azure CLI를 사용 하 여 계정 만들기

Azure CLI를 사용 하 여 블록 blob 계정을 만들려면 먼저 Azure CLI v를 설치 해야 합니다. 2.0.46 또는 이후 버전입니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. Azure 구독에 로그인합니다.

   ```azurecli
   az login
   ```

1. 필요한 경우 새 리소스 그룹을 만듭니다. 다음 명령을 실행 하 고 대괄호 (대괄호 포함)에서 값을 대체 합니다.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. 블록 blob 저장소 계정을 만듭니다. 다음 명령을 실행 하 고 대괄호 (대괄호 포함)에서 값을 대체 합니다.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>다음 단계

- 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)를 참조하세요.

- 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.
