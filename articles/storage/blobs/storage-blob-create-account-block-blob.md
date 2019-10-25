---
title: 블록 blob 저장소 계정 만들기-Azure Storage | Microsoft Docs
description: 프리미엄 성능 특성을 사용 하 여 Azure BlockBlobStorage 계정을 만드는 방법을 보여 줍니다.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881859"
---
# <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage 계정 만들기

BlockBlobStorage 계정 종류를 사용 하면 프리미엄 성능 특성을 포함 하는 블록 blob을 만들 수 있습니다. 이 유형의 저장소 계정은 트랜잭션 속도가 많고 매우 빠른 액세스 시간이 필요한 워크 로드에 최적화 되어 있습니다. 이 문서에서는 Azure Portal, Azure CLI 또는 Azure PowerShell를 사용 하 여 BlockBlobStorage 계정을 만드는 방법을 보여 줍니다.

BlockBlobStorage 계정에 대 한 자세한 내용은 [Azure storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)를 참조 하세요.

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure Portal에서 BlockBlobStorage 계정을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **저장소 범주 > 저장소** **계정**> **모든 서비스** 를 선택 합니다.

1. **저장소 계정**에서 **추가**를 선택 합니다.

1. **구독** 필드에서 저장소 계정을 만들 구독을 선택 합니다.

1. **리소스 그룹** 필드에서 기존 리소스 그룹을 선택 하거나 **새로 만들기**를 선택 하 고 새 리소스 그룹의 이름을 입력 합니다.

1. **저장소 계정 이름** 필드에 계정 이름을 입력 합니다. 다음 지침에 유의 하세요.

   - 이름은 Azure에서 고유해야 합니다.
   - 이름은 3 자에서 24 자 사이 여야 합니다.
   - 이름에는 숫자 및 소문자만 포함할 수 있습니다.

1. **위치** 필드에서 저장소 계정의 위치를 선택 하거나 기본 위치를 사용 합니다.

1. 나머지 설정에 대해 다음을 구성 합니다.

   |필드     |Value  |
   |---------|---------|
   |**성능**    |  **프리미엄**을 선택 합니다.   |
   |**계정 종류**    | **Blockblobstorage**를 선택 합니다.      |
   |**복제**    |  **LRS (로컬 중복 저장소)** 의 기본 설정을 그대로 둡니다.      |

   ![블록 blob storage 계정을 만들기 위한 포털 UI를 표시 합니다.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. **검토 + 만들기** 를 선택 하 여 저장소 계정 설정을 검토 합니다.

1. **만들기**를 선택합니다.

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 관리자 권한으로 Windows PowerShell 세션을 엽니다 (관리자 권한으로 실행).

1. 다음 명령을 실행 하 여 `Az` PowerShell 모듈의 최신 버전이 설치 되어 있는지 확인 합니다.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 새 PowerShell 콘솔을 열고 Azure 계정으로 로그인 합니다.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 필요한 경우 새 리소스 그룹을 만듭니다. 따옴표에서 값을 바꾸고 다음 명령을 실행 합니다.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. BlockBlobStorage 계정을 만듭니다. 따옴표에서 값을 바꾸고 다음 명령을 실행 합니다.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 블록 blob 계정을 만들려면 Azure CLI v를 먼저 설치 해야 합니다. 2.0.46 이상 버전. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. Azure 구독에 로그인합니다.

   ```azurecli
   az login
   ```

1. 필요한 경우 새 리소스 그룹을 만듭니다. 대괄호 (대괄호 포함)로 값을 바꾸고 다음 명령을 실행 합니다.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. BlockBlobStorage 계정을 만듭니다. 대괄호 (대괄호 포함)로 값을 바꾸고 다음 명령을 실행 합니다.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>다음 단계

- 저장소 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)를 참조하세요.

- 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.
