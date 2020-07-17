---
title: Geth를 사용하여 Azure Blockchain Service에 연결
description: Azure Blockchain Service 트랜잭션 노드의 Geth 인스턴스에 연결
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994786"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>빠른 시작: Geth를 사용하여 Azure Blockchain Service 트랜잭션 노드에 연결

이 빠른 시작에서는 Geth 클라이언트를 사용하여 Azure Blockchain Service 트랜잭션 노드의 Geth 인스턴스에 연결했습니다. 연결되면 Geth 콘솔을 사용하여 Ethereum JavaScript API를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) 설치
* [빠른 시작: Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기](create-member-cli.md)를 완료합니다.

## <a name="get-geth-connection-string"></a>Geth 연결 문자열 가져오기

Azure Portal에서 Azure Blockchain Service 트랜잭션 노드에 대한 Geth 연결 문자열을 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain Service 멤버로 이동합니다. **트랜잭션 노드**와 기본 트랜잭션 노드 링크를 선택합니다.

    ![기본 트랜잭션 노드 선택](./media/connect-geth/transaction-nodes.png)

1. **연결 문자열**을 선택합니다.
1. **HTTPS(액세스 키 1)** 에서 연결 문자열을 복사합니다. 다음 섹션에는 문자열이 필요합니다.

    ![연결 문자열](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Geth에 연결

1. 명령 프롬프트 또는 셸을 엽니다.
1. Geth attach 하위 명령을 사용하여 트랜잭션 노드에서 실행 중인 Geth 인스턴스에 연결합니다. 연결 문자열을 attach 하위 명령의 인수로 붙여넣습니다. 예를 들면 다음과 같습니다.

    ``` bash
    geth attach <connection string>
    ```

1. 트랜잭션 노드의 Ethereum 콘솔에 연결되면 Ethereum JavaScript API를 사용할 수 있습니다.

    예를 들어 다음 API를 사용하여 chainId를 확인합니다.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    이 예제에서 chainId는 661입니다.

    ![Azure Blockchain Service 옵션](./media/connect-geth/geth-attach.png)

1. 콘솔에서 연결을 끊으려면 `exit`를 입력합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Geth 클라이언트를 사용하여 Azure Blockchain Service 트랜잭션 노드의 Geth 인스턴스에 연결했습니다. 다음 자습서에서 Etherum용 Azure Blockchain Development Kit를 사용하여 트랜잭션을 통해 스마트 계약 함수를 만들고, 빌드하고, 배포하고, 실행해 보세요.

> [!div class="nextstepaction"]
> [Azure Blockchain Service에서 스마트 계약 생성, 빌드 및 배포](send-transaction.md)
