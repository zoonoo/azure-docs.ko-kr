---
title: Azure CLI를 사용 하 여 블록 체인 Data Manager 구성-Azure Blockchain 서비스
description: Azure CLI를 사용 하 여 Azure Blockchain 서비스에 대 한 Blockchain Data Manager 만들기 및 관리
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: e490803fabeed7d6234bd6984acbfb9f5270e0c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254413"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Azure CLI를 사용하여 Blockchain Data Manager 구성

블록 체인 데이터를 캡처하도록 Blockchain Data Manager 구성 하 여 Azure Event Grid 토픽으로 보냅니다.

Blockchain Data Manager 인스턴스를 구성 하려면 다음을 수행 합니다.

* 블록 체인 관리자 인스턴스 만들기
* Azure Blockchain 서비스 트랜잭션 노드에 대 한 입력 만들기
* Azure Event Grid 항목에 대 한 출력 만들기
* 블록체인 애플리케이션 추가
* 인스턴스 시작

## <a name="prerequisites"></a>전제 조건

* 최신 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 를 설치 하 고를 사용 `az login`하 여 로그인 합니다.
* 빠른 시작 완료 [: Visual Studio Code을 사용 하 여 Azure Blockchain Service consortium 네트워크에 연결](connect-vscode.md)합니다. Blockchain Data Manager를 사용 하는 경우 Azure Blockchain 서비스 *표준* 계층을 사용 하는 것이 좋습니다.
* [Event Grid 토픽](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) 만들기
* [Azure Event Grid의 이벤트 처리기](../../event-grid/event-handlers.md) 알아보기

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에 Azure CLI 버전 2.0.51 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>인스턴스 만들기

Blockchain Data Manager 인스턴스는 Azure Blockchain 서비스 트랜잭션 노드를 모니터링 합니다. 인스턴스는 트랜잭션 노드의 모든 원시 블록 및 원시 트랜잭션 데이터를 캡처합니다. Blockchain Data Manager web3 eth [Getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) 및 [getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) 쿼리에서 반환 되는 정보의 상위 집합인 **RawBlockAndTransactionMsg** 메시지를 게시 합니다.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| 매개 변수 | Description |
|-----------|-------------|
| resource-group | 블록 체인 Data Manager 인스턴스를 만들 리소스 그룹 이름입니다. |
| name | 블록 체인 Data Manager 인스턴스의 이름입니다. |
| 리소스 유형 | Blockchain Data Manager 인스턴스에 대 한 리소스 유형은 **Microsoft blockchain/감시자**입니다. |
| is-full-object | 속성에 감시자 리소스에 대 한 옵션이 포함 되어 있음을 나타냅니다. |
| properties | 감시자 리소스의 속성을 포함 하는 JSON 형식 문자열입니다. 는 문자열 또는 파일로 전달할 수 있습니다.  |

### <a name="create-instance-examples"></a>인스턴스 만들기 예제

**미국 동부** 지역에 블록 체인 관리자 인스턴스를 만드는 JSON 구성 예제입니다.

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| 요소 | Description |
|---------|-------------|
| 위치 | 감시자 리소스를 만들 지역 |
| properties | 감시자 리소스를 만들 때 설정할 속성 |

구성에 JSON 문자열을 사용 하 여 *mywatcher* 라는 blockchain Data Manager 인스턴스를 만듭니다.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

JSON 구성 파일을 사용 하 여 *mywatcher* 라는 블록 체인 Data Manager 인스턴스를 만듭니다.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>입력 만들기

입력은 Blockchain Data Manager를 Azure Blockchain 서비스 트랜잭션 노드에 연결 합니다. 트랜잭션 노드에 대 한 액세스 권한이 있는 사용자만 연결을 만들 수 있습니다.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| 매개 변수 | Description |
|-----------|-------------|
| resource-group | 입력 리소스를 만들 리소스 그룹 이름입니다. |
| name | 입력의 이름입니다. |
| namespace | **Microsoft Blockchain** 공급자 네임 스페이스를 사용 합니다. |
| 리소스 유형 | 블록 체인에 대 한 리소스 유형은 입력 Data Manager 입력 **입니다.** |
| 부모(parent) | 입력이 연결 된 감시자의 경로입니다. 예를 들면 **감시자/mywatcher**가 있습니다. |
| is-full-object | 속성에 입력 리소스에 대 한 옵션이 포함 되어 있음을 나타냅니다. |
| properties | 입력 리소스의 속성을 포함 하는 JSON 형식 문자열입니다. 는 문자열 또는 파일로 전달할 수 있습니다. |

### <a name="input-examples"></a>입력 예제

블록 체인 구성원 \<\>에 연결 된 *미국 동부* 지역에서 입력 리소스를 만들기 위한 구성 JSON 예제입니다.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| 요소 | Description |
|---------|-------------|
| 위치 | 입력 리소스를 만들 지역입니다. |
| inputType | Azure Blockchain 서비스 구성원의 원장 유형입니다. 현재 **Ethereum** 가 지원 됩니다. |
| resourceId | 입력이 연결 된 트랜잭션 노드입니다. 구독 \<\>ID \<, 리소스\>그룹 및 \<blockchain 멤버\> 를 트랜잭션 노드 리소스에 대 한 값으로 바꿉니다. 입력은 Azure Blockchain 서비스 멤버의 기본 트랜잭션 노드에 연결 됩니다. |

구성에 JSON 문자열을 사용 하 여 *myinput* 에 대해 *myinput* 이라는 입력을 만듭니다.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

JSON 구성 파일을 사용 하 여 *myinput* 에 대해 *myinput* 이라는 입력을 만듭니다.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>출력 만들기

아웃바운드 연결은 블록체인 데이터를 Azure Event Grid로 보냅니다. 블록체인 데이터는 단일 대상 또는 여러 대상으로 보낼 수 있습니다. Blockchain Data Manager는 지정된 Blockchain Data Manager 인스턴스에 대한 여러 개의 Event Grid 항목 아웃바운드 연결을 지원합니다.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| 매개 변수 | Description |
|-----------|-------------|
| resource-group | 출력 리소스를 만들 리소스 그룹 이름입니다. |
| name | 출력의 이름입니다. |
| namespace | **Microsoft Blockchain** 공급자 네임 스페이스를 사용 합니다. |
| 리소스 유형 | Blockchain Data Manager 출력에 대 한 리소스 유형은 **출력**입니다. |
| 부모(parent) | 출력이 연결 된 감시자의 경로입니다. 예를 들면 **감시자/mywatcher**가 있습니다. |
| is-full-object | 속성에 출력 리소스에 대 한 옵션이 포함 되어 있음을 나타냅니다. |
| properties | 출력 리소스에 대 한 속성을 포함 하는 JSON 형식 문자열입니다. 는 문자열 또는 파일로 전달할 수 있습니다. |

### <a name="output-examples"></a>출력 예제

Event grid 토픽 \<\>이라는 event grid 토픽에 연결 된 *미국 동부* 지역에 출력 리소스를 만드는 구성 JSON 예제입니다.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| 요소 | Description |
|---------|-------------|
| 위치 | 출력 리소스를 만들 지역입니다. |
| outputType | 출력의 유형입니다. 현재 **Eventgrid** 가 지원 됩니다. |
| resourceId | 출력이 연결 되는 리소스입니다. 구독 \<\>ID \<, 리소스\>그룹 및 \<blockchain 멤버\> 를 event grid 리소스의 값으로 바꿉니다. |

JSON 구성 문자열을 사용 하 여 event grid 토픽에 연결 하는 *mywatcher* 용 *myoutput* 이라는 출력을 만듭니다.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

JSON 구성 파일을 사용 하 여 event grid 토픽에 연결 하는 *mywatcher* 용 *myoutput* 이라는 출력을 만듭니다.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>블록 체인 응용 프로그램 추가

Blockchain 응용 프로그램을 추가 하는 경우 Blockchain Data Manager 응용 프로그램에 대 한 이벤트 및 속성 상태를 디코딩합니다. 그렇지 않으면 원시 블록 및 원시 트랜잭션 데이터만 전송 됩니다. Blockchain Data Manager 계약을 배포할 때 계약 주소도 검색 합니다. 블록 체인 Data Manager 인스턴스에 여러 블록 체인 응용 프로그램을 추가할 수 있습니다.


> [!IMPORTANT]
> 현재는 농담 [배열 형식](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) 또는 [매핑 형식을](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) 선언 하는 블록 체인 응용 프로그램은 완전히 지원 되지 않습니다. 배열 또는 매핑 형식으로 선언 된 속성은 *ContractPropertiesMsg* 또는 *DecodedContractEventsMsg* 메시지에서 디코딩되 지 않습니다.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| 매개 변수 | Description |
|-----------|-------------|
| resource-group | 응용 프로그램 리소스를 만들 리소스 그룹 이름입니다. |
| name | 응용 프로그램의 이름입니다. |
| namespace | **Microsoft Blockchain** 공급자 네임 스페이스를 사용 합니다. |
| 리소스 유형 | Blockchain Data Manager 응용 프로그램에 대 한 리소스 형식이 **아티팩트**입니다. |
| 부모(parent) | 응용 프로그램이 연결 된 감시자의 경로입니다. 예를 들면 **감시자/mywatcher**가 있습니다. |
| is-full-object | 속성에 응용 프로그램 리소스에 대 한 옵션이 포함 되어 있음을 나타냅니다. |
| properties | 응용 프로그램 리소스에 대 한 속성을 포함 하는 JSON 형식 문자열입니다. 는 문자열 또는 파일로 전달할 수 있습니다. |

### <a name="blockchain-application-examples"></a>Blockchain 응용 프로그램 예제

계약 ABI 및 바이트 코드에서 정의한 스마트 계약을 모니터링 하는 응용 프로그램 리소스를 *미국 동부* 지역에 만드는 구성 JSON 예제입니다.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| 요소 | Description |
|---------|-------------|
| 위치 | 응용 프로그램 리소스를 만들 지역입니다. |
| artifactType | 애플리케이션의 유형입니다. 현재 **EthereumSmartContract** 가 지원 됩니다. |
| abiFileUrl | 스마트 계약 ABI JSON 파일에 대 한 URL입니다. 계약 ABI를 가져오고 URL을 만드는 방법에 대 한 자세한 내용은 [계약 abi 및 바이트 코드 가져오기](data-manager-portal.md#get-contract-abi-and-bytecode) 및 [계약 abi 및 바이트 코드 URL 만들기](data-manager-portal.md#create-contract-abi-and-bytecode-url)를 참조 하세요. |
| bytecodeFileUrl | 스마트 계약에 배포 된 바이트 코드 JSON 파일의 URL입니다. 스마트 계약에서 바이트 코드를 배포 하 고 URL을 만드는 방법에 대 한 자세한 내용은 [계약 abi 및 바이트 코드 가져오기](data-manager-portal.md#get-contract-abi-and-bytecode) 및 [계약 abi 및 바이트 코드 url 만들기](data-manager-portal.md#create-contract-abi-and-bytecode-url)를 참조 하세요. 참고: Blockchain Data Manager에는 **배포 된 바이트**집합이 필요 합니다. |
| queryTargetTypes | 게시 된 메시지 유형입니다. **ContractProperties** 게시 *ContractPropertiesMsg* 메시지 유형을 지정 합니다. **ContractEvents** 게시 *DecodedContractEventsMsg* 메시지 유형을 지정 합니다. 참고: *RawBlockAndTransactionMsg* 및 *RawTransactionContractCreationMsg* 메시지 유형은 항상 게시 됩니다. |

JSON 문자열로 정의 된 스마트 계약을 모니터링 하는, *mywatcher* 에 대해 *myApplication* 이라는 응용 프로그램을 만듭니다.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

JSON 구성 파일을 사용 하 여 정의 된 스마트 계약을 감시 하는, *mywatcher* 용 *myApplication* 이라는 응용 프로그램을 만듭니다.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>인스턴스 시작

블록 체인 관리자 인스턴스는 실행 될 때 정의 된 입력에서 Blockchain 이벤트를 모니터링 하 고 정의 된 출력에 데이터를 보냅니다.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 매개 변수 | Description |
|-----------|-------------|
| action | **Start** 를 사용 하 여 감시자를 실행 합니다. |
| ids | 감시자 리소스 ID입니다. 구독 \<\>ID \<, 리소스\>그룹 및 \<감시자 이름을\> 감시자 리소스의 값으로 바꿉니다.|

### <a name="start-instance-example"></a>시작 인스턴스 예제

*Mywatcher*라는 블록 체인 Data Manager 인스턴스를 시작 합니다.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>인스턴스 중지

블록 체인 Data Manager 인스턴스를 중지 합니다.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 매개 변수 | Description |
|-----------|-------------|
| action | **중지** 를 사용 하 여 감시자를 중지 합니다. |
| ids | 감시자의 이름입니다. 구독 \<\>ID \<, 리소스\>그룹 및 \<감시자 이름을\> 감시자 리소스의 값으로 바꿉니다. |

### <a name="stop-watcher-example"></a>감시자 중지 예

*Mywatcher*라는 인스턴스를 중지 합니다.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>인스턴스 삭제

블록 체인 Data Manager 인스턴스를 삭제 합니다.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| 매개 변수 | Description |
|-----------|-------------|
| resource-group | 삭제할 감시자의 리소스 그룹 이름입니다. |
| name | 삭제할 감시자의 이름입니다. |
| 리소스 유형 | Blockchain Data Manager 감시자의 리소스 유형은 **Microsoft blockchain/감시자**입니다. |

### <a name="delete-instance-example"></a>인스턴스 삭제 예제

*Mywatcher* 리소스 그룹에서 *mywatcher* 라는 인스턴스를 삭제 합니다.

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>다음 단계

블록 체인 Data Manager 및 Azure Cosmos DB를 사용 하 여 블록 체인 트랜잭션 메시지 탐색기를 만드는 다음 자습서를 시도 합니다.

> [!div class="nextstepaction"]
> [Blockchain Data Manager를 사용하여 Azure Cosmos DB로 데이터 보내기](data-manager-cosmosdb.md)
