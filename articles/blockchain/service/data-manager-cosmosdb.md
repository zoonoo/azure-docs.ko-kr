---
title: Blockchain Data Manager를 사용하여 Azure Cosmos DB 업데이트 - Azure Blockchain Service
description: Azure Blockchain Service용 Blockchain Data Manager를 사용하여 Azure Cosmos DB로 블록체인 데이터 보내기
ms.date: 11/04/2019
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 497652f91d46592212a17a0a22832c02a696df62
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326254"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>자습서: Blockchain Data Manager를 사용하여 Azure Cosmos DB로 데이터 보내기

이 자습서에서는 Azure Blockchain Service에 Blockchain Data Manager를 사용하여 블록체인 트랜잭션 데이터를 Azure Cosmos DB에 기록합니다. Blockchain Data Manager는 블록체인 데이터를 캡처하고, 변환하고, Azure Event Grid 토픽으로 전송합니다. Azure Event Grid에서 Azure 논리 앱 커넥터를 사용하여 Azure Cosmos DB 데이터베이스에 문서를 만듭니다. 이 자습서를 완료하면 Azure Cosmos DB Data Explorer에서 블록체인 트랜잭션 데이터를 검색할 수 있습니다.

[![블록체인 트랜잭션 세부 정보](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Blockchain Data Manager 인스턴스 만들기
> * 블록체인 애플리케이션을 추가하여 트랜잭션 속성 및 이벤트 디코딩
> * 트랜잭션 데이터를 저장할 Azure Cosmos DB 계정 및 데이터베이스 만들기
> * Azure Event Grid 토픽을 Azure Cosmos DB에 연결하는 Azure 논리 앱 만들기
> * 블록체인 원장으로 트랜잭션 보내기
> * Azure Cosmos DB에서 디코딩된 트랜잭션 데이터 보기

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [빠른 시작: Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기](create-member-cli.md)를 완료합니다.
* [빠른 시작: Visual Studio Code를 사용하여 Azure Blockchain Service 컨소시엄 네트워크에 연결](connect-vscode.md)을 완료해야 합니다. 이 빠른 시작에서는 [Ethereum용 Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)를 설치하고 블록체인 개발 환경을 설정하는 방법을 안내합니다.
* [자습서: Visual Studio Code를 사용하여 스마트 계약 생성, 빌드 및 배포](send-transaction.md)를 완료합니다. 이 자습서에서는 샘플 스마트 계약을 만드는 과정을 안내합니다.
* [Event Grid 토픽](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) 만들기
* [Azure Event Grid의 이벤트 처리기](../../event-grid/event-handlers.md) 알아보기

## <a name="create-instance"></a>인스턴스 만들기

Blockchain Data Manager 인스턴스는 Azure Blockchain Service 트랜잭션 노드에 연결하여 모니터링합니다. 인스턴스는 트랜잭션 노드의 모든 원시 블록 및 원시 트랜잭션 데이터를 캡처합니다. 아웃바운드 연결은 블록체인 데이터를 Azure Event Grid로 보냅니다. 인스턴스를 만들 때 단일 아웃바운드 연결을 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 필수 조건 [빠른 시작: Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md)에서 만든 Azure Blockchain Service 멤버로 이동합니다. **Blockchain Data Manager**를 선택합니다.
1. **추가**를 선택합니다.

    ![Blockchain Data Manager 추가](./media/data-manager-cosmosdb/add-instance.png)

    다음 세부 정보를 입력합니다.

    설정 | 예 | 설명
    --------|---------|------------
    Name | mywatcher | 연결된 Blockchain Data Manager의 고유 이름을 입력합니다.
    트랜잭션 노드 | myblockchainmember | 필수 조건에서 만든 Azure Blockchain Service 멤버의 기본 트랜잭션 노드를 선택합니다.
    연결 이름 | cosmosdb | 블록체인 트랜잭션 데이터가 전송되는 아웃바운드 연결의 고유 이름을 입력합니다.
    Event Grid 엔드포인트 | myTopic | 필수 조건에서 만든 Event Grid 토픽을 선택합니다. 참고: Blockchain Data Manager 인스턴스와 Event Grid 토픽은 동일한 구독에 있어야 합니다.

1. **확인**을 선택합니다.

    1분 내에 Blockchain Data Manager 인스턴스가 만들어집니다. 인스턴스가 배포된 후 자동으로 시작됩니다. 실행 중인 Blockchain Data Manager 인스턴스는 트랜잭션 노드에서 블록체인 이벤트를 캡처하고 Event Grid에 데이터를 보냅니다.

## <a name="add-application"></a>애플리케이션 추가

Blockchain Data Manager가 이벤트 및 속성 상태를 디코딩할 수 있도록 **helloblockchain** 블록체인 애플리케이션을 추가합니다. Blockchain Data Manager에는 애플리케이션을 추가하는 스마트 계약 ABI 및 바이트 코드 파일이 필요합니다.

### <a name="get-contract-abi-and-bytecode"></a>계약 ABI 및 바이트 코드 가져오기

계약 ABI는 스마트 계약 인터페이스를 정의합니다. 스마트 계약과 상호 작용하는 방법을 설명합니다. [Azure Blockchain Development Kit for Ethereum 확장](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)을 사용하여 계약 ABI를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 **helloblockchain** Solidity 프로젝트의 **빌드/계약** 폴더를 확장합니다. 이 프로젝트는 필수 조건 [자습서: Visual Studio Code를 사용하여 스마트 계약 생성, 빌드 및 배포](send-transaction.md)에서 만든 프로젝트입니다.
1. 마우스 오른쪽 단추로 계약 메타데이터 JSON 파일을 클릭합니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. **계약 ABI 복사**를 선택합니다.

    ![계약 ABI 복사가 선택된 Visual Studio Code 창](./media/data-manager-cosmosdb/abi-devkit.png)

    계약 ABI가 클립보드에 복사됩니다.

1. **abi** 배열을 JSON 파일로 저장합니다. 예: *abi.json*. 이 파일은 이후 단계에서 사용됩니다.

Blockchain Data Manager에는 스마트 계약용으로 배포된 바이트 코드가 필요합니다. 배포된 바이트 코드는 스마트 계약 바이트 코드와 다릅니다. 배포된 바이트 코드는 컴파일된 계약 메타데이터 파일에서 가져올 수 있습니다.

1. Solidity 프로젝트의 **build/contracts** 폴더에 포함된 계약 메타데이터 파일을 엽니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. JSON 파일에서 **deployedBytecode** 요소를 찾습니다.
1. 따옴표 없이 16진수 값을 복사합니다.

    ![메타데이터의 바이트 코드가 있는 Visual Studio Code 창](./media/data-manager-portal/bytecode-metadata.png)

1. **바이트 코드** 값을 JSON 파일로 저장합니다. 예: *bytecode.json*. 이 파일은 이후 단계에서 사용됩니다.

다음 예제는 VS Code 편집기에서 열려 있는 *abi.json* 및 *bytecode.json* 파일을 보여줍니다. 파일은 다음과 비슷합니다.

![abi.json 및 bytecode.json 파일 예제](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>계약 ABI 및 바이트 코드 URL 가져오기

Blockchain Data Manager을 사용하려면 애플리케이션을 추가할 때 URL을 통해 계약 ABI 및 바이트 코드 파일에 액세스할 수 있어야 합니다. Azure Storage 계정을 사용하여 URL에 대한 프라이빗 액세스를 제공할 수 있습니다.

#### <a name="create-storage-account"></a>스토리지 계정 만들기

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>계약 파일 업로드

1. 스토리지 계정에 대한 새 컨테이너를 만듭니다. **컨테이너 > 컨테이너**를 선택합니다.

    ![스토리지 계정 컨테이너 만들기](./media/data-manager-cosmosdb/create-container.png)

    | 설정 | 설명 |
    |---------|-------------|
    | Name  | 컨테이너 이름을 지정합니다. 예: *smartcontract* |
    | 공용 액세스 수준 | *프라이빗(익명 액세스 없음)* 선택 |

1. **확인**을 선택하여 컨테이너를 만듭니다.
1. 컨테이너를 선택하고 **업로드**를 선택합니다.
1. [계약 ABI 및 바이트 코드](#get-contract-abi-and-bytecode) 가져오기 섹션에서 만든 JSON 파일 두 개를 모두 선택합니다.

    ![Blob 업로드](./media/data-manager-cosmosdb/upload-blobs.png)

    **업로드**를 선택합니다.

#### <a name="generate-url"></a>URL 생성

각 Blob에 대해 공유 액세스 서명을 생성합니다.

1. ABI JSON Blob을 선택합니다.
1. **SAS 생성**을 선택합니다.
1. 원하는 액세스 서명 만료를 설정한 다음, **Blob SAS 토큰 및 URL 생성**을 선택합니다.

    ![SAS 토큰 생성](./media/data-manager-cosmosdb/generate-sas.png)

1. 다음 섹션에서 사용할 수 있도록 **Blob SAS URL**을 복사하여 저장합니다.
1. 바이트 코드 JSON Blob에도 [URL 생성](#generate-url) 단계를 반복합니다.

### <a name="add-helloblockchain-application-to-instance"></a>인스턴스에 helloblockchain 애플리케이션 추가

1. 인스턴스 목록에서 Blockchain Data Manager 인스턴스를 선택합니다.
1. **블록체인 애플리케이션**을 선택합니다.
1. **추가**를 선택합니다.

    ![블록체인 애플리케이션 추가](./media/data-manager-cosmosdb/add-application.png)

    블록체인 애플리케이션의 이름과 스마트 계약 ABI 및 바이트 코드 URL을 입력합니다.

    설정 | 설명
    --------|------------
    Name | 추적할 블록체인 애플리케이션의 고유 이름을 입력합니다.
    계약 ABI | 계약 ABI 파일의 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조하세요.
    계약 바이트 코드 | 바이트 코드 파일의 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조하세요.

1. **확인**을 선택합니다.

    애플리케이션이 생성되면 블록체인 애플리케이션 목록에 표시됩니다.

    ![블록체인 애플리케이션 목록](./media/data-manager-cosmosdb/artifact-list.png)

Azure Storage 계정을 삭제해도 되고 또 다른 블록체인 애플리케이션을 구성하는 데 사용할 수도 있습니다. Azure Storage 계정을 삭제하려면 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 연결된 스토리지 계정과 기타 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

## <a name="create-azure-cosmos-db"></a>Azure Cosmos DB 만들기

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>데이터베이스 및 컨테이너 추가

Azure Portal에서 데이터 탐색기를 사용하여 데이터베이스와 컨테이너를 만들 수 있습니다.

1. Azure Cosmos DB 계정 페이지의 왼쪽 탐색 모음에서 **Data Explorer**를 선택한 다음, **새 컬렉션**을 선택합니다.
1. **컨테이너 추가** 창에서 새 컨테이너의 설정을 입력합니다.

    ![컨테이너 설정 추가](./media/data-manager-cosmosdb/add-container.png)

    | 설정 | 설명
    |---------|-------------|
    | 데이터베이스 ID | 새 데이터베이스의 이름으로 **blockchain-data**를 입력합니다. |
    | 처리량 | 처리량을 **400**RU/s(초당 요청 단위)로 유지합니다. 대기 시간을 줄이면 나중에 처리량을 늘릴 수 있습니다.|
    | 컨테이너 ID | 새 컨테이너의 이름으로 **Messages**를 입력합니다. |
    | 파티션 키 | 파티션 키로 **/MessageType**을 사용합니다. |

1. **확인**을 선택합니다. Data Explorer가 새 데이터베이스와 앞에서 만든 컨테이너를 표시합니다.

## <a name="create-logic-app"></a>논리 앱 만들기

Azure Logic Apps를 사용하면 시스템과 서비스를 통합해야 할 때 비즈니스 프로세스와 워크플로를 예약하고 자동화할 수 있습니다. 논리 앱을 사용하여 Event Grid를 Azure Cosmos DB에 연결할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **통합** > **논리 앱**을 선택합니다.
1. 논리 앱을 만드는 위치에 대한 세부 정보를 입력합니다. 완료되면 **만들기**를 선택합니다.

    논리 앱을 만드는 방법에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 자동화된 워크플로 만들기](../../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

1. Azure에서 앱을 배포한 후에 논리 앱 리소스를 선택합니다.
1. Logic Apps 디자이너의 **템플릿** 아래에서 **비어 있는 논리 앱**을 선택합니다.

### <a name="add-event-grid-trigger"></a>Event Grid 트리거 추가

모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 트리거가 발생될 때마다 Logic Apps 엔진은 워크플로를 시작하고 실행하는 논리 앱 인스턴스를 만듭니다. Azure Event Grid 트리거를 사용하여 Event Grid에서 Cosmos DB로 블록체인 트랜잭션 데이터를 보냅니다.

1. Logic Apps 디자이너에서 **Azure Event Grid** 커넥터를 검색하여 선택합니다.
1. **트리거** 탭에서 **리소스 이벤트가 발생하는 경우**를 선택합니다.
1. Event Grid 토픽에 대한 API 연결을 만듭니다.

    ![Event Grid 트리거 설정](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | 설정 | 설명
    |---------|-------------|
    | Subscription | Event Grid 토픽을 포함하는 구독을 선택합니다. |
    | 리소스 종류 | **Microsoft.EventGrid.Topics**를 선택합니다. |
    | 리소스 이름 | Blockchain Data Manager가 트랜잭션 데이터 메시지를 보내는 Event Grid 토픽의 이름을 선택합니다. |

### <a name="add-cosmos-db-action"></a>Cosmos DB 작업 추가

Cosmos DB에 각 트랜잭션에 대한 문서를 만드는 작업을 추가합니다. 트랜잭션 메시지 유형을 파티션 키로 사용하여 메시지를 분류합니다.

1. **새 단계**를 선택합니다.
1. **작업 선택**에서 **Azure Cosmos DB**를 검색합니다.
1. **Azure Cosmos DB > 작업 > 문서 만들기 또는 업데이트**를 선택합니다.
1. Cosmos DB 데이터베이스에 대한 API 연결을 만듭니다.

    ![Cosmos DB 연결 설정](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | 설정 | 설명
    |---------|-------------|
    | 연결 이름 | Event Grid 토픽을 포함하는 구독을 선택합니다. |
    | DocumentDB 계정 | [Azure Cosmos DB 계정 만들기](#create-azure-cosmos-db) 섹션에서 만든 DocumentDB 계정을 선택합니다. |

1. 이전에 [데이터베이스 및 컨테이너 추가](#add-a-database-and-container) 섹션에서 만든 Azure Cosmos DB의 **데이터베이스 ID** 및 **컬렉션 ID**를 입력합니다.

1. **문서** 설정을 선택합니다. *동적 콘텐츠 추가* 팝아웃에서 **식**을 선택하고 다음 식을 복사하여 붙여넣습니다.

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    이 식은 메시지의 데이터 부분을 가져와서 ID를 타임스탬프 값으로 설정합니다.

1. **새 매개 변수 추가**를 선택하고 **파티션 키 값**을 선택합니다.
1. **파티션 키 값**을 `"@{triggerBody()['data']['MessageType']}"`으로 설정합니다. 값을 큰따옴표로 묶어야 합니다.

    ![Cosmos DB 설정을 사용하는 Logic Apps 디자이너](./media/data-manager-cosmosdb/create-action.png)

    이 값은 파티션 키를 트랜잭션 메시지 유형으로 설정합니다.

1. **저장**을 선택합니다.

논리 앱은 Event Grid 토픽을 모니터링합니다. Blockchain Data Manager에서 새 트랜잭션 메시지가 전송되면 논리 앱은 Cosmos DB에 문서를 만듭니다.

## <a name="send-a-transaction"></a>트랜잭션 보내기

다음으로, 트랜잭션을 블록체인 원장으로 보내서 만든 항목을 테스트합니다. **sendrequest.js** 스크립트를 사용합니다. 이 스크립트는 필수 조건 [자습서: Visual Studio Code를 사용하여 스마트 계약 생성, 빌드 및 배포](send-transaction.md)에서 만든 스크립트입니다.

VS Code의 터미널 창에서 Truffle을 사용하여 컨소시엄 블록체인 네트워크에서 스크립트를 실행합니다. 터미널 창 메뉴 모음의 드롭다운에서 **터미널** 탭 및 **PowerShell**을 선택합니다.

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

\<블록체인 네트워크\>를 **truffle-config.js**에 정의된 블록체인 네트워크 이름으로 바꿉니다.

![트랜잭션 보내기](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>트랜잭션 데이터 보기

Blockchain Data Manager를 Azure Cosmos DB에 연결했으므로, 이제 Cosmos DB Data Explorer에서 블록체인 트랜잭션 메시지를 볼 수 있습니다.

1. Cosmos DB Data Explorer 보기로 이동합니다. 예를 들어 **cosmosdb-blockchain > Data Explorer > blockchain-data > 메시지 > 항목**으로 이동합니다.

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer는 Cosmos DB 데이터베이스에 만들어진 블록체인 데이터 메시지를 나열합니다.

1. 항목 ID를 선택하여 메시지를 찾아보면서 트랜잭션 해시가 일치하는 메시지를 찾습니다.

    [![블록체인 트랜잭션 세부 정보](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    원시 트랜잭션 메시지는 트랜잭션에 대한 세부 정보를 포함하고 있습니다. 그러나 속성 정보는 암호화됩니다.

    HelloBlockchain 스마트 계약을 Blockchain Data Manager 인스턴스에 추가했으므로, 디코딩된 속성 정보를 포함하는 **ContractProperties** 메시지 유형도 전송됩니다.

1. 트랜잭션에 대한 **ContractProperties** 메시지를 찾습니다. 이 메시지는 목록의 그 다음 메시지입니다.

    [![블록체인 트랜잭션 세부 정보](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    **DecodedProperties** 배열에는 트랜잭션의 속성이 포함됩니다.

축하합니다! Blockchain Data Manager 및 Azure Cosmos DB를 사용하여 트랜잭션 메시지 탐색기를 성공적으로 만들었습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 사용한 리소스와 리소스 그룹이 더 이상 필요 없으면 삭제해도 됩니다. 리소스 그룹을 삭제합니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

블록체인 원장 통합에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Logic Apps에서 Ethereum 블록체인 커넥터 사용](ethereum-logic-app.md)
