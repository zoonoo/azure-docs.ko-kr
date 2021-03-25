---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f0d0322f6f5f14b94a67285fe8688d72c941b3a4
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104481"
---
<!-- ### Create a storage account -->

Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services에서 스토리지 계정을 사용하는 방법에 대한 자세한 내용은 [스토리지 계정](../storage-account-concept.md)을 참조하세요.

하나의 **기본** 저장소 계정이 있어야 하며, Media Services 계정에 연결 된 **보조** 저장소 계정을 개수에 제한 없이 사용할 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. Blob 전용 계정은 **기본** 으로 허용되지 않습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 옵션](../../../storage/common/storage-account-overview.md)을 참조하세요. 

다음 예제에서는 범용 v2, 표준 LRS 계정을 만듭니다. 스토리지 계정을 실험하려면 `--sku Standard_LRS`를 사용합니다. 그러나 프로덕션용 SKU를 선택하는 경우 비즈니스 연속성을 위해 지리적 복제를 제공하는 `--sku Standard_RAGRS`를 고려해야 합니다. 자세한 내용은 [스토리지 계정](/cli/azure/storage/account)을 참조하세요.

다음 명령은 Media Services 계정과 연결될 스토리지 계정을 만듭니다. 아래 스크립트에서 `storageaccountforams` 길이가 24 자 미만인 고유한 고유한 이름으로 대체 합니다. `amsResourceGroup` 이전 단계에서 리소스 그룹에 대해 지정한 값과 일치 해야 합니다.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
