---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747939"
---
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

[az storage account create](/cli/azure/storage/account) 명령을 사용하여 범용 스토리지 계정을 만듭니다. 범용 스토리지 계정은 4개의 모든 서비스(Blob, 파일, 테이블 및 큐)에 사용할 수 있습니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>스토리지 계정 자격 증명 지정

Azure CLI는 이 자습서의 명령 대부분에 스토리지 계정 자격 증명을 필요로 합니다. 이를 수행하는 여러 가지 옵션이 있지만 가장 쉬운 방법 중 하나는 `AZURE_STORAGE_ACCOUNT` 및 `AZURE_STORAGE_KEY` 환경 변수를 설정하는 것입니다.

> [!NOTE]
> 이 문서에서는 Bash를 사용하여 환경 변수를 설정하는 방법을 보여 줍니다. 다른 환경에서는 구문 수정이 필요할 수 있습니다.

먼저, [az storage account keys list](/cli/azure/storage/account/keys) 명령을 사용하여 스토리지 계정 키를 표시합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

이제 `AZURE_STORAGE_ACCOUNT` 및 `AZURE_STORAGE_KEY` 환경 변수를 설정합니다. `export` 명령을 사용하여 Bash 셸에서 이 작업을 수행할 수 있습니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Azure Portal를 사용하여 계정 액세스 키를 검색하는 방법에 대한 자세한 내용은 [Azure Portal에서 스토리지 계정 설정 관리](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys)의 **액세스 키**를 참조하세요.