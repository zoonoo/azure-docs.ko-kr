---
title: Azure CLI를 사용하여 Azure Blockchain Service 만들기
description: Azure Blockchain Service에서 Azure CLI를 사용하여 블록체인 멤버를 만듭니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: e1b7558ea83c8948a8984215e15040e4d929cb1b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141375"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기

Azure Blockchain Service는 스마트 계약 내에서 비즈니스 논리를 실행하는 데 사용할 수 있는 블록체인 플랫폼입니다. 이 빠른 시작에서는 Azure CLI를 통해 블록체인 멤버를 만들어서 시작하는 방법을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에 Azure CLI 버전 2.0.51 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>블록체인 멤버 만들기

Azure Blockchain Service를 사용하여 새 컨소시엄에서 Quorum 원장 프로토콜을 실행하는 블록체인 멤버를 만듭니다.

여러 매개 변수와 속성을 전달해야 합니다. 다음 매개 변수를 해당하는 값으로 바꾸세요.

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. 이전 섹션에서 만든 리소스 그룹을 사용합니다.
| **name** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. 예: `myblockchainmember.blockchain.azure.com`
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. 예: `eastus` 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다.
| **암호** | 멤버 계정 암호 멤버 계정 암호는 기본 인증을 사용하여 블록체인 멤버의 공용 엔드포인트를 인증할 때 사용됩니다.
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다.
| **consortiumManagementAccountPassword** | 컨소시엄 관리 암호입니다. 컨소시엄에 참가할 때 사용됩니다.
| **skuName** | 계층 유형입니다. 표준에는 S0를 사용하고, 기본에는 B0를 사용합니다.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

블록체인 멤버 및 지원 리소스를 만드는 데 약 10분이 걸립니다.

다음 예제는 성공적인 만들기 작업의 출력을 보여줍니다.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>리소스 정리

앞에서 만든 블록체인 멤버를 다음 빠른 시작 또는 자습서에 사용할 수 있습니다. 더 이상 필요 없으면 Azure Blockchain Service에서 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

블록체인 멤버를 만들었으므로, 이제 [Geth](connect-geth.md), [MetaMask](connect-metamask.md) 또는 [Truffle](connect-truffle.md)에 대한 트랜잭션 노드에 연결 빠른 시작 중 하나를 사용합니다.

> [!div class="nextstepaction"]
> [Truffle을 사용하여 Azure Blockchain Service 네트워크에 연결](connect-truffle.md)
