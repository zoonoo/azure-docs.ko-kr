---
title: Azure CLI 스크립트 샘플 - SignalR Service 만들기
description: Azure CLI 스크립트 샘플 - SignalR Service 만들기
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 364a8b6574b06aa2403ea028fecd0676ba0342a7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256216"
---
# <a name="create-a-signalr-service"></a>SignalR Service 만들기 

이 샘플 스크립트는 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 Azure CLI용 *signalr* 확장을 사용합니다. 이 샘플 스크립트를 사용하기 전에 다음 명령을 실행하여 Azure CLI용 *signalr* 확장을 설치합니다.

```azurecli-interactive
az extension add -n signalr
```

이 스크립트는 새 SignalR Service 리소스와 새 리소스 그룹을 만듭니다. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

새 리소스 그룹에 대해 생성된 실제 이름을 적어 둡니다. 모든 그룹 리소스를 삭제하려는 경우 해당 리소스 그룹 이름을 사용합니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az signalr create](/cli/azure/group#az-group-create) | Azure SignalR Service 리소스를 만듭니다. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | SignalR을 통해 실시간 콘텐츠 업데이트를 푸시할 때 응용 프로그램에서 사용할 키를 나열합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure SignalR Service CLI 스크립트 샘플은 [Azure SignalR Service 설명서](../signalr-cli-samples.md)에서 확인할 수 있습니다.
