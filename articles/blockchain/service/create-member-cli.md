---
title: Azure Blockchain Service 멤버 만들기 - Azure CLI
description: Azure CLI를 사용하여 블록체인 컨소시엄을 위한 Azure Blockchain Service 멤버를 만듭니다.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: f97aab59d38e9b15838a78d0227bc2848615cd92
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504367"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Blockchain Service에 새 블록체인 멤버 및 컨소시엄을 배포합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

없음

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에 Azure CLI 버전 2.0.51 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-your-environment"></a>환경 준비

1. 로그인합니다.

    로컬에 설치된 CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 로그인합니다.

    ```azurecli
    az login
    ```

    터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

1. Azure CLI 확장을 설치합니다.

    Azure CLI에 대한 확장 참조를 사용하는 경우 먼저 확장을 설치해야 합니다.  Azure CLI 확장은 아직 핵심 CLI의 일부로 제공되지 않는 실험적 명령과 시험판 명령에 대한 액세스를 제공합니다.  확장 업데이트 및 제거를 포함하여 확장에 대해 자세한 내용을 보려면 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.

    다음 명령을 실행하여 [Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain)를 설치합니다.

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. 리소스 그룹을 만듭니다.

    모든 Azure 리소스와 마찬가지로 Azure Blockchain Service는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

    이 빠른 시작에서는 다음 [az group create](/cli/azure/group#az-group-create) 명령을 사용하여 _eastus_ 위치에 _myResourceGroup_이라는 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>블록체인 멤버 만들기

Azure Blockchain Service 멤버는 프라이빗 컨소시엄 블록체인 네트워크의 블록체인 노드입니다. 멤버를 프로비저닝할 때 컨소시엄 네트워크를 만들거나 조인할 수 있습니다. 컨소시엄 네트워크에 멤버가 하나 이상이 필요합니다. 참가자가 필요로 하는 블록체인 멤버 수는 시나리오에 따라 달라집니다. 컨소시엄 참가자는 하나 이상의 블록체인 멤버를 보유하거나 다른 참가자와 멤버를 공유할 수 있습니다. 컨소시엄에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요.

여러 매개 변수와 속성을 전달해야 합니다. 예제 매개 변수를 원하는 값으로 바꿉니다.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. 이전 섹션에서 만든 리소스 그룹을 사용합니다.
| **name** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. 예들 들어 `myblockchainmember.blockchain.azure.com`입니다.
| **location** | 블록체인 멤버가 만들어지는 Azure 지역입니다. `westus2`)을 입력합니다. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. 기능은 일부 지역에서 사용하지 못할 수도 있습니다. Azure Blockchain Data Manager는 다음 Azure 지역에서 사용할 수 있습니다. 즉 미국 동부 및 서유럽에서 실행되는 기본 웹 사이트의 두 인스턴스
| **password** | 멤버의 기본 트랜잭션 노드에 대한 암호입니다. 블록체인 멤버의 기본 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다.
| **protocol** | Blockchain 프로토콜입니다. 현재 *Quorum* 프로토콜이 지원됩니다.
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. 컨소시엄에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요.
| **컨소시엄-관리-계정-암호** | 컨소시엄 계정 암호는 멤버 계정 암호라고도 합니다. 멤버 계정 암호는 멤버용으로 생성된 Ethereum 계정의 프라이빗 키를 암호화하는 데 사용됩니다. 컨소시엄 관리용 멤버 계정과 멤버 계정 암호를 사용합니다.
| **sku** | 계층 유형입니다. *표준* 또는 *기본*입니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 프로덕션 등급 배포에 *표준* 계층을 사용합니다. Blockchain Data Manager를 사용하거나 대량의 프라이빗 트랜잭션을 전송하는 경우 *표준* 계층도 사용해야 합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

블록체인 멤버 및 지원 리소스를 만드는 데 약 10분이 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

앞에서 만든 블록체인 멤버를 다음 빠른 시작 또는 자습서에 사용할 수 있습니다. 더 이상 필요 없는 경우 빠른 시작을 위해 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Blockchain Service 멤버 및 새로운 컨소시엄을 배포했습니다. 다음 빠른 시작을 통해 Ethereum용 Azure Blockchain Development Kit를 사용하여 Azure Blockchain Service 멤버에 연결합니다.

> [!div class="nextstepaction"]
> [Visual Studio Code를 사용하여 Azure Blockchain Service에 연결](connect-vscode.md)
