---
title: Azure CLI 스크립트 샘플 - SignalR Service 만들기
description: 샘플 스크립트에 따라 임의의 이름으로 새 리소스 그룹에 새 Azure SignalR Service를 만드는 방법을 알아봅니다.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 17b8df919c5059da3f3abd7a4f06aca1eadd0817
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563973"
---
# <a name="create-a-signalr-service"></a>SignalR Service 만들기 

이 샘플 스크립트는 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 새 SignalR Service 리소스와 새 리소스 그룹을 만듭니다. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

echo "$primaryConnectionString"
```

새 리소스 그룹에 대해 생성된 실제 이름을 적어 둡니다. 모든 그룹 리소스를 삭제하려는 경우 해당 리소스 그룹 이름을 사용합니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Azure SignalR Service 리소스를 만듭니다. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | SignalR을 통해 실시간 콘텐츠 업데이트를 푸시할 때 애플리케이션에서 사용할 키를 나열합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure SignalR Service CLI 스크립트 샘플은 [Azure SignalR Service 설명서](../signalr-reference-cli.md)에서 확인할 수 있습니다.
