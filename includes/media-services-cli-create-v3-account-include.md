---
title: 포함 파일
description: 포함 파일
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: da27f818bf368108568287f1ed1bbdae4c3902d4
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72560474"
---
## <a name="create-a-media-services-account"></a>Media Services 계정 만들기

먼저 Media Services 계정을 만들어야 합니다. 이 섹션에서는 Azure CLI를 사용하여 계정을 생성할 때 수행해야 하는 작업을 설명합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

다음 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure Media Services 계정 및 관련 스토리지 계정과 같은 리소스가 배포되어 관리되는 논리적 컨테이너입니다.

@No__t_0를 값으로 대체할 수 있습니다.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services에서 스토리지 계정을 사용하는 방법에 대한 자세한 내용은 [스토리지 계정](../articles/media-services/latest/storage-account-concept.md)을 참조하세요.

**기본** 스토리지 계정이 하나 있어야 하며 미디어 서비스 계정과 연결된 **보조** 스토리지 계정이 여러 개 있을 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. Blob 전용 계정은 **기본**으로 허용되지 않습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 옵션](../articles/storage/common/storage-account-options.md)을 참조하세요. 

다음 예제에서는 범용 v2, 표준 LRS 계정을 만듭니다. 스토리지 계정을 실험하려면 `--sku Standard_LRS`를 사용합니다. 그러나 프로덕션용 SKU를 선택하는 경우 비즈니스 연속성을 위해 지리적 복제를 제공하는 `--sku Standard_RAGRS`를 고려해야 합니다. 자세한 내용은 [스토리지 계정](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)을 참조하세요.
 
다음 명령은 Media Services 계정과 연결될 스토리지 계정을 만듭니다. 아래 스크립트에서 `storageaccountforams`를 원하는 값으로 대체할 수 있습니다. `amsResourceGroup`은 이전 단계에서 리소스 그룹에 대해 지정한 값과 일치 해야 합니다. 저장소 계정 이름의 길이는 24 자 미만 이어야 합니다.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services 계정 만들기

다음 Azure CLI 명령은 새 Media Services 계정을 만듭니다. `amsaccount``storageaccountforams`(스토리지 계정에 지정한 값과 일치해야 함) 및 `amsResourceGroup`(리소스 그룹에 지정한 값과 일치해야 함) 값을 바꿀 수 있습니다.

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```
