---
title: Configure Blockchain Data Manager using Azure portal - Azure Blockchain Service
description: Create and manage Blockchain Data Manager for Azure Blockchain Service using the Azure portal.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 9c682f449fbab823134d626870c7dcfe8a8f2847
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455820"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Azure Portal을 사용하여 Blockchain Data Manager 구성

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data and send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Data Manager instance for an Azure Blockchain Service transaction node
* Add your blockchain applications

## <a name="prerequisites"></a>전제 조건

* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)
* [Event Grid 토픽](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) 만들기
* [Azure Event Grid의 이벤트 처리기](../../event-grid/event-handlers.md) 알아보기

## <a name="create-instance"></a>인스턴스 만들기

Blockchain Data Manager 인스턴스는 Azure Blockchain Service 트랜잭션 노드에 연결하여 모니터링합니다. Only users with access to the transaction node can create a connection. 인스턴스는 트랜잭션 노드의 모든 원시 블록 및 원시 트랜잭션 데이터를 캡처합니다.

아웃바운드 연결은 블록체인 데이터를 Azure Event Grid로 보냅니다. 인스턴스를 만들 때 단일 아웃바운드 연결을 구성합니다. Blockchain Data Manager는 지정된 Blockchain Data Manager 인스턴스에 대한 여러 개의 Event Grid 항목 아웃바운드 연결을 지원합니다. 블록체인 데이터는 단일 대상 또는 여러 대상으로 보낼 수 있습니다. To add another destination, just add additional outbound connections to the instance.

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
1. Go to the Azure Blockchain Service member you want to connect to Blockchain Data Manager. **Blockchain Data Manager**를 선택합니다.
1. **추가**를 선택합니다.

    ![Blockchain Data Manager 추가](./media/data-manager-portal/add-instance.png)

    다음 세부 정보를 입력합니다.

    설정 | 설명
    --------|------------
    name | 연결된 Blockchain Data Manager의 고유 이름을 입력합니다. The Blockchain Data Manager name can contain lower case letters and numbers and has a maximum length of 20 characters.
    트랜잭션 노드 | Choose a transaction node. Only transaction nodes you have read access are listed.
    연결 이름 | 블록체인 트랜잭션 데이터가 전송되는 아웃바운드 연결의 고유 이름을 입력합니다.
    Event Grid 엔드포인트 | Choose an event grid topic in the same subscription as the Blockchain Data Manager instance.

1. **확인**을 선택합니다.

    1분 내에 Blockchain Data Manager 인스턴스가 만들어집니다. 인스턴스가 배포된 후 자동으로 시작됩니다. A running Blockchain Data Manager instance captures blockchain events from the transaction node and sends data to the outbound connections.

    The new instance appears in the list of Blockchain Data Manager instances for the Azure Blockchain Service member.

    ![List of Blockchain Data Member instances](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.

> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

Blockchain Data Manager requires a smart contract ABI and deployed bytecode file to add the application.

### <a name="get-contract-abi-and-bytecode"></a>Get Contract ABI and bytecode

계약 ABI는 스마트 계약 인터페이스를 정의합니다. 스마트 계약과 상호 작용하는 방법을 설명합니다. [Azure Blockchain Development Kit for Ethereum 확장](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)을 사용하여 계약 ABI를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 Solidity 프로젝트의 **build/contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 계약 메타데이터 JSON 파일을 클릭합니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. **계약 ABI 복사**를 선택합니다.

    ![계약 ABI 복사가 선택된 Visual Studio Code 창](./media/data-manager-portal/abi-devkit.png)

    계약 ABI가 클립보드에 복사됩니다.

1. **abi** 배열을 JSON 파일로 저장합니다. 예: *abi.json*. 이 파일은 이후 단계에서 사용됩니다.

Blockchain Data Manager에는 스마트 계약용으로 배포된 바이트 코드가 필요합니다. 배포된 바이트 코드는 스마트 계약 바이트 코드와 다릅니다. 배포된 바이트 코드는 컴파일된 계약 메타데이터 파일에서 가져올 수 있습니다.

1. Solidity 프로젝트의 **build/contracts** 폴더에 포함된 계약 메타데이터 파일을 엽니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. JSON 파일에서 **deployedBytecode** 요소를 찾습니다.
1. 따옴표 없이 16진수 값을 복사합니다.

    ![메타데이터의 바이트 코드가 있는 Visual Studio Code 창](./media/data-manager-portal/bytecode-metadata.png)

1. **바이트 코드** 값을 JSON 파일로 저장합니다. 예: *bytecode.json*. 이 파일은 이후 단계에서 사용됩니다.

다음 예제는 VS Code 편집기에서 열려 있는 *abi.json* 및 *bytecode.json* 파일을 보여줍니다. 파일은 다음과 비슷합니다.

![abi.json 및 bytecode.json 파일 예제](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>계약 ABI 및 바이트 코드 URL 가져오기

Blockchain Data Manager을 사용하려면 애플리케이션을 추가할 때 URL을 통해 계약 ABI 및 바이트 코드 파일에 액세스할 수 있어야 합니다. Azure Storage 계정을 사용하여 URL에 대한 프라이빗 액세스를 제공할 수 있습니다.

#### <a name="create-storage-account"></a>스토리지 계정 만들기

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>계약 파일 업로드

1. 스토리지 계정에 대한 새 컨테이너를 만듭니다. **컨테이너 > 컨테이너**를 선택합니다.

    ![스토리지 계정 컨테이너 만들기](./media/data-manager-portal/create-container.png)

    | 필드 | 설명 |
    |-------|-------------|
    | name  | 컨테이너 이름을 지정합니다. 예: *smartcontract* |
    | 공용 액세스 수준 | *프라이빗(익명 액세스 없음)* 선택 |

1. **확인**을 선택하여 컨테이너를 만듭니다.
1. 컨테이너를 선택하고 **업로드**를 선택합니다.
1. [계약 ABI 및 바이트 코드](#get-contract-abi-and-bytecode) 가져오기 섹션에서 만든 JSON 파일 두 개를 모두 선택합니다.

    ![Blob 업로드](./media/data-manager-portal/upload-blobs.png)

    **업로드**를 선택합니다.

#### <a name="generate-url"></a>URL 생성

각 Blob에 대해 공유 액세스 서명을 생성합니다.

1. ABI JSON Blob을 선택합니다.
1. **SAS 생성**을 선택합니다.
1. 원하는 액세스 서명 만료를 설정한 다음, **Blob SAS 토큰 및 URL 생성**을 선택합니다.

    ![SAS 토큰 생성](./media/data-manager-portal/generate-sas.png)

1. 다음 섹션에서 사용할 수 있도록 **Blob SAS URL**을 복사하여 저장합니다.
1. 바이트 코드 JSON Blob에도 [URL 생성](#generate-url) 단계를 반복합니다.

### <a name="add-application-to-instance"></a>Add application to instance

1. 인스턴스 목록에서 Blockchain Data Manager 인스턴스를 선택합니다.
1. **블록체인 애플리케이션**을 선택합니다.
1. **추가**를 선택합니다.

    ![블록체인 애플리케이션 추가](./media/data-manager-portal/add-application.png)

    블록체인 애플리케이션의 이름과 스마트 계약 ABI 및 바이트 코드 URL을 입력합니다.

    설정 | 설명
    --------|------------
    name | 추적할 블록체인 애플리케이션의 고유 이름을 입력합니다.
    계약 ABI | 계약 ABI 파일의 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조하세요.
    계약 바이트 코드 | 바이트 코드 파일의 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조하세요.

1. **확인**을 선택합니다.

    애플리케이션이 생성되면 블록체인 애플리케이션 목록에 표시됩니다.

    ![블록체인 애플리케이션 목록](./media/data-manager-portal/artifact-list.png)

Azure Storage 계정을 삭제해도 되고 또 다른 블록체인 애플리케이션을 구성하는 데 사용할 수도 있습니다. Azure Storage 계정을 삭제하려면 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 연결된 스토리지 계정과 기타 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

## <a name="stop-instance"></a>Stop instance

Stop the Blockchain Manager instance when you want to stop capturing blockchain events and sending data to the outbound connections. When the instance is stopped, no charges are incurred for Blockchain Data Manager. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/blockchain-service)을 참조하세요.

1. Go to **Overview** and select **Stop**.

    ![Stop instance](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>다음 단계

Try the next tutorial creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)