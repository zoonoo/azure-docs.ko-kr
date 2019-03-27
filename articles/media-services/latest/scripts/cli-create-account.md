---
title: Azure Media Services 계정 만들기 - Azure CLI| Microsoft Docs
description: Azure CLI 스크립트를 사용하여 Azure Media Services 계정을 만들 수 있습니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 7e668852346de1b48bd34658dea001435ba8a625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842226"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI 예제: Azure Media Services 계정 만들기

이 항목의 Azure CLI 스크립트는 Azure Media Services 계정을 만드는 방법을 보여줍니다. 

Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. Media Services 계정과 동일한 위치의 스토리지 계정을 사용하는 것이 좋습니다.

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 저장소 계정을 만듭니다. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Media Services 계정 만들기 |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | 암호로 서비스 주체를 만들고 Azure Media Services 계정에 대한 액세스를 구성합니다. 
| [az group delete](/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 예](../cli-samples.md)를 참조하세요.
