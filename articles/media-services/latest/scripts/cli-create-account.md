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
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 2eeb47c2e0f96eca1ca9b852a2be6ca3102ba71e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353348"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI 예제: Azure Media Services 계정 만들기

이 항목의 Azure CLI 스크립트는 Azure Media Services 계정을 만드는 방법을 보여줍니다. Media Services 계정 및 이 계정에 연결된 스토리지 계정은 같은 데이터 센터 및 리소스 그룹에 포함되어 있어야 합니다.

## <a name="prerequisites"></a>필수 조건 

CLI를 로컬로 설치하여 사용하려면 이 문서에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

현재 일부 [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) 명령은 Azure Cloud Shell에서 작동하지 않습니다. CLI를 로컬로 사용하는 것이 좋습니다.

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
