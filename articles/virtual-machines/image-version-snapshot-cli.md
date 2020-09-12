---
title: CLI-공유 이미지 갤러리의 스냅숏 또는 관리 디스크에서 이미지 만들기
description: Azure CLI를 사용 하 여 공유 이미지 갤러리의 스냅숏 또는 관리 디스크에서 이미지를 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e694630d8bcd7879d9405152c4141fb6e5bad4e2
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297096"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Azure CLI를 사용 하 여 공유 이미지 갤러리에서 관리 디스크 또는 스냅숏에서 이미지 만들기

공유 이미지 갤러리로 마이그레이션할 기존 스냅숏 또는 관리 디스크가 있는 경우 관리 디스크 또는 스냅숏에서 직접 공유 이미지 갤러리 이미지를 만들 수 있습니다. 새 이미지를 테스트 한 후에는 원본 관리 디스크 또는 스냅숏을 삭제할 수 있습니다. [Azure PowerShell](image-version-snapshot-powershell.md)를 사용 하 여 공유 이미지 갤러리에서 관리 디스크 또는 스냅숏에서 이미지를 만들 수도 있습니다.

이미지 갤러리의 이미지에는 다음 예제에서 만들 두 가지 구성 요소가 있습니다.
- 이미지 **정의** 는 이미지 및 사용에 대 한 요구 사항에 대 한 정보를 전달 합니다. 여기에는 이미지가 Windows 또는 Linux 인지, 특수 하거나 일반화 되었는지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함 됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 공유 이미지 갤러리를 사용할 때 VM을 만드는 데 사용 됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 만들 때 이미지 버전은 VM에 대 한 새 디스크를 만드는 데 사용 됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 스냅숏 또는 관리 디스크가 있어야 합니다. 

데이터 디스크를 포함 하려는 경우 데이터 디스크 크기는 1TB 이상일 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.

## <a name="find-the-snapshot-or-managed-disk"></a>스냅숏 또는 관리 디스크 찾기 

[Az snapshot list](/cli/azure/snapshot#az-snapshot-list)를 사용 하 여 리소스 그룹에서 사용할 수 있는 스냅숏 목록을 볼 수 있습니다. 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

스냅숏 대신 관리 디스크를 사용할 수도 있습니다. 관리 디스크를 가져오려면 [az Disk list](/cli/azure/disk#az-disk-list)를 사용 합니다. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

스냅숏 또는 관리 디스크의 ID를 만든 후 `$source` 나중에 사용할 수 있도록 이라는 변수에 할당 합니다.

동일한 프로세스를 사용 하 여 이미지에 포함 하려는 데이터 디스크를 가져올 수 있습니다. 변수에 할당 한 다음 나중에 이미지 버전을 만들 때 이러한 변수를 사용 합니다.


## <a name="find-the-gallery"></a>갤러리 찾기

이미지 정의를 만들려면 이미지 갤러리에 대 한 정보가 필요 합니다.

[Az sig list](/cli/azure/sig#az-sig-list)를 사용 하 여 사용 가능한 이미지 갤러리에 대 한 정보를 나열 합니다. 갤러리 이름 갤러리에서 나중에 사용할 리소스 그룹을 확인 합니다.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이미지에 대 한 정보를 관리 하는 데 사용 됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때에 올바른 정보가 모두 있는지 확인 합니다. 이 예에서는 스냅숏 또는 관리 디스크를 사용 중인 VM에서 사용 하는 것으로 가정 하 고 일반화 되지 않은 것으로 가정 합니다. Windows 용 Sysprep 또는 [waagent](https://github.com/Azure/WALinuxAgent) 또는 Linux를 실행 한 후에 관리 되는 디스크 또는 스냅숏이 일반화 된 OS를 만든 경우 `-deprovision` `-deprovision+user` `-OsState` 를로 변경 합니다 `generalized` . 

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./linux/shared-image-galleries.md#image-definitions)를 참조하세요.

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)를 사용하여 갤러리에서 이미지 정의를 만듭니다.

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition*이며 [특수](./linux/shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. Windows OS를 사용하여 이미지에 대한 정의를 만들려면 `--os-type Windows`를 사용합니다. 

이 예제에서 갤러리 이름은 *Mygallery*이 고, *myGalleryRG* 리소스 그룹에 있으며, 이미지 정의 이름이 *mimagedefinition*이 됩니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>이미지 버전 만들기

[Az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create)을 사용 하 여 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지의 버전은 *1.0.0* 이며, 미국 동부 2 *지역에서* 영역 중복 저장소를 사용 하 여 1 개의 복제본과 *미국 동부 2* 지역에 1 개의 복제본을 만듭니다. 복제를 위한 대상 영역을 선택할 때 관리 디스크 또는 스냅숏의 *원본* 지역을 복제 대상으로 포함 해야 한다는 점에 주의 해야 합니다.

매개 변수에서 스냅숏 또는 관리 디스크의 ID를 전달 `--os-snapshot` 합니다.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

이미지에 데이터 디스크를 포함 하려는 경우 `--data-snapshot-luns` LUN 번호에 설정 된 매개 변수와 `--data-snapshots` 데이터 디스크 또는 스냅숏의 ID로 설정 된 매개 변수를 모두 포함 해야 합니다.

> [!NOTE]
> 동일한 관리 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 이미지 버전을 만들 때를 추가 하 여 모든 이미지 버전 복제본을 [영역 중복 저장소](../storage/common/storage-redundancy.md) 에 저장할 수도 있습니다 `--storage-account-type standard_zrs` .
>

## <a name="next-steps"></a>다음 단계

[특수 한 이미지 버전](vm-specialized-image-version-cli.md)에서 VM을 만듭니다.

구매 계획 정보를 제공 하는 방법에 대 한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조 하세요.
