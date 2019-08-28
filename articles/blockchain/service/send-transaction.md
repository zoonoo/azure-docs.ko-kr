---
title: Azure Blockchain Service에서 스마트 계약 사용
description: Azure Blockchain Service를 사용하여 스마트 계약을 배포하고, 트랜잭션을 통해 함수를 실행하는 방법에 대한 자습서입니다.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705123"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>자습서: Azure Blockchain Service에서 스마트 계약 사용

이 자습서에서는 Etherum용 Azure Blockchain Development Kit를 사용하여 스마트 계약을 만들고 배포한 다음, 컨소시엄 블록체인 네트워크에서 트랜잭션을 통해 스마트 계약 함수를 실행합니다.

Etherum용 Azure Blockchain Development Kit를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Blockchain Service 컨소시엄 블록체인 멤버에 연결
> * 스마트 계약 만들기
> * 스마트 계약 배포
> * 트랜잭션을 통해 스마트 계약 함수 실행
> * 계약 상태 쿼리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [빠른 시작: Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기](create-member-cli.md)를 완료합니다.
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Etherum용 Azure Blockchain Development Kit 확장](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.JS](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). python.exe를 경로에 추가합니다. Azure Blockchain Development Kit에는 경로의 Python이 필요합니다.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Azure Blockchain Development Kit 환경 확인

Azure Blockchain Development Kit는 개발 환경 필수 구성 요소가 충족되었는지 확인합니다. 개발 환경을 확인하려면 다음을 수행합니다.

VS Code 명령 팔레트에서 **Azure Blockchain: 시작 페이지 표시**를 선택합니다.

Azure Blockchain Development Kit는 완료하는 데 1분 정도 걸리는 유효성 검사 스크립트를 실행합니다. **터미널 > 새 터미널**을 차례로 선택하여 출력을 볼 수 있습니다. 터미널 메뉴 모음의 드롭다운에서 **출력** 탭 및 **Azure Blockchain**을 선택합니다. 성공한 유효성 검사는 다음 이미지와 같습니다.

![유효한 개발 환경](./media/send-transaction/valid-environment.png)

 필수 도구가 없는 경우 **Azure Blockchain Development Kit - 미리 보기**라는 새 탭에는 설치할 필수 앱과 해당 도구를 다운로드하는 데 필요한 링크가 나열됩니다.

![개발 키트 필수 앱](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>컨소시엄 멤버에 연결

Azure Blockchain Development Kit VS Code 확장을 사용하여 컨소시엄 멤버에 연결할 수 있습니다. 컨소시엄에 연결되면 스마트 계약을 Azure Blockchain Service 컨소시엄 멤버에 컴파일, 빌드 및 배포할 수 있습니다.

Azure Blockchain Service 컨소시엄 멤버에 액세스할 수 없는 경우 [빠른 시작: Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기](create-member-cli.md)의 필수 조건을 완료합니다.

1. VS Code(Visual Studio Code) 탐색기 창에서 **Azure Blockchain** 확장을 펼칩니다.
1. **컨소시엄에 연결**을 선택합니다.

   ![컨소시엄에 연결](./media/send-transaction/connect-consortium.png)

    Azure 인증을 요청하는 메시지가 표시되면 프롬프트에 따라 브라우저를 사용하여 인증합니다.
1. 명령 팔레트 드롭다운에서 **Azure Blockchain Service 컨소시엄에 연결**을 선택합니다.
1. Azure Blockchain Service 컨소시엄 멤버와 연결된 구독 및 리소스 그룹을 선택합니다.
1. 목록에서 컨소시엄을 선택합니다.

컨소시엄 및 블록체인 멤버가 Visual Studio 탐색기 사이드바에 나열됩니다.

![탐색기에 표시된 컨소시엄](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>스마트 계약 만들기

Etherum용 Azure Blockchain Development Kit는 프로젝트 템플릿과 Truffle 도구를 사용하여 계약을 스캐폴드, 빌드 및 배포하는 데 도움을 줍니다.

1. VS Code 명령 팔레트에서 **Azure Blockchain: 새 Solidity 프로젝트**를 선택합니다.
1. **기본 프로젝트 만들기**를 선택합니다.
1. `HelloBlockchain`이라는 새 폴더를 만들고, **새 프로젝트 경로를 선택**합니다.

Azure Blockchain Development Kit에서 새 Solidity 프로젝트를 만들고 초기화합니다. 기본 프로젝트에는 **HelloBlockchain** 스마트 계약 샘플과 Azure Blockchain Service의 컨소시엄 멤버에 빌드 및 배포하는 데 필요한 모든 파일이 포함되어 있습니다. 프로젝트를 만드는 데 몇 분 정도 걸릴 수 있습니다. Azure Blockchain의 출력을 선택하여 VS Code의 터미널 패널의 진행 상황을 모니터링할 수 있습니다.

프로젝트 구조는 다음 예제와 같습니다.

   ![Solidity 프로젝트](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>스마트 계약 빌드

스마트 계약은 프로젝트의 **contracts** 디렉터리에 있습니다. 블록체인에 배포하기 전에 스마트 계약을 컴파일합니다. **계약 빌드** 명령을 사용하여 프로젝트의 모든 스마트 계약을 컴파일합니다.

1. VS Code 탐색기 사이드바에서 프로젝트의 **contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **계약 빌드**를 선택합니다.

    ![계약 빌드](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit에서 Truffle을 사용하여 스마트 계약을 컴파일합니다.

![컴파일 출력](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>스마트 계약 배포

Truffle은 마이그레이션 스크립트를 사용하여 계약을 Ethereum 네트워크에 배포합니다. 마이그레이션은 프로젝트의 **miginations** 디렉터리에 있는 JavaScript 파일입니다.

1. 스마트 계약을 배포하려면 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **계약 배포**를 선택합니다.
1. **truffle-config.js에서** 아래에서 Azure Blockchain 컨소시엄 네트워크를 선택합니다. 컨소시엄 블록체인 네트워크는 프로젝트를 만들 때 프로젝트의 Truffle 구성 파일에 추가되었습니다.
1. **니모닉 생성**을 선택합니다. 파일 이름을 선택하고, 해당 니모닉 파일을 프로젝트 폴더에 저장합니다. 예: `myblockchainmember.env` 니모닉 파일은 블록체인 멤버에 대한 Ethereum 프라이빗 키를 생성하는 데 사용됩니다.

Azure Blockchain Development Kit에서 Truffle을 사용하여 계약을 블록체인으로 배포하는 마이그레이션 스크립트를 실행합니다.

![성공적으로 배포된 계약](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>계약 함수 호출

**HelloBlockchain** 계약의 **SendRequest** 함수는 **RequestMessage** 상태 변수를 변경합니다. 블록체인 네트워크의 상태를 변경하는 작업은 트랜잭션을 통해 수행됩니다. 트랜잭션을 통해 **SendRequest** 함수를 실행하는 스크립트를 만들 수 있습니다.

1. 새 파일을 Truffle 프로젝트의 루트에 만들고, 이름을 `sendrequest.js`로 지정합니다. 다음 Web3 JavaScript 코드를 파일에 추가합니다.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Azure Blockchain Development Kit에서 프로젝트를 만들면 Truffle 구성 파일이 컨소시엄 블록체인 네트워크 엔드포인트의 세부 정보를 사용하여 생성됩니다. 프로젝트에서 **truffle-config.js**를 엽니다. 구성 파일에는 각각 development라는 이름 및 컨소시엄과 동일한 이름의 두 개의 네트워크가 나열되어 있습니다.
1. VS Code의 터미널 창에서 Truffle을 사용하여 컨소시엄 블록체인 네트워크에서 스크립트를 실행합니다. 터미널 창 메뉴 모음의 드롭다운에서 **터미널** 탭 및 **PowerShell**을 선택합니다.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    \<블록체인 네트워크\>를 **truffle-config.js**에 정의된 블록체인 네트워크 이름으로 바꿉니다.

Truffle이 블록체인 네트워크에서 스크립트를 실행합니다.

![스크립트 출력](./media/send-transaction/execute-transaction.png)

트랜잭션을 통해 계약 함수를 실행하면 블록이 만들어질 때까지 트랜잭션이 처리되지 않습니다. 트랜잭션을 통해 수행되는 함수는 반환 값 대신 트랜잭션 ID를 반환합니다.

## <a name="query-contract-state"></a>계약 상태 쿼리

스마트 계약 함수는 상태 변수의 현재 값을 반환할 수 있습니다. 상태 변수의 값을 반환하는 함수를 추가해 보겠습니다.

1. **HelloBlockchain.sol**에서 **getMessage** 함수를 **HelloBlockchain** 스마트 계약에 추가합니다.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    이 함수는 계약의 현재 상태에 따라 상태 변수에 저장된 메시지를 반환합니다.

1. 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **계약 빌드**를 선택하여 스마트 계약의 변경 내용을 컴파일합니다.
1. 배포하려면 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **계약 배포**를 선택합니다.
1. 다음으로 **getMessage** 함수를 호출하는 데 사용하는 스크립트를 만듭니다. 새 파일을 Truffle 프로젝트의 루트에 만들고, 이름을 `getmessage.js`로 지정합니다. 다음 Web3 JavaScript 코드를 파일에 추가합니다.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. VS Code의 터미널 창에서 Truffle을 사용하여 블록체인 네트워크에서 스크립트를 실행합니다. 터미널 창 메뉴 모음의 드롭다운에서 **터미널** 탭 및 **PowerShell**을 선택합니다.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    \<블록체인 네트워크\>를 **truffle-config.js**에 정의된 블록체인 네트워크 이름으로 바꿉니다.

스크립트에서 getMessage 함수를 호출하여 스마트 계약을 쿼리합니다. **RequestMessage** 상태 변수의 현재 값이 반환됩니다.

![스크립트 출력](./media/send-transaction/execute-get.png)

값이 **Hello, blockchain!** 이 아닙니다. 대신, 반환되는 값은 자리 표시자입니다. 계약을 변경하여 배포하는 경우 계약에는 새 계약 주소가 지정되고 스마트 계약 생성자의 값이 상태 변수에 할당됩니다. Truffle 샘플인 **2_deploy_contracts.js** 마이그레이션 스크립트는 스마트 계약을 배포하고 자리 표시자 값을 인수로 전달합니다. 생성자는 **RequestMessage** 상태 변수를 자리 표시자 값으로 설정하고 이를 반환합니다.

1. **RequestMessage** 상태 변수를 설정하고 값을 쿼리하려면 **sendrequest.js** 및 **getmessage.js** 스크립트를 다시 실행합니다.

    ![스크립트 출력](./media/send-transaction/execute-set-get.png)

    **sendrequest.js**는 **RequestMessage** 상태 변수를 **Hello, blockchain!** 으로 설정하고, **getmessage.js**는 **RequestMessage** 상태 변수의 값에 대한 계약을 쿼리하고 **Hello, blockchain!** 을 반환합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 *블록체인 멤버 만들기* 필수 조건 빠른 시작에서 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Blockchain Development Kit를 사용하여 Solidity 프로젝트 샘플을 만들었습니다. 스마트 계약을 빌드하고 배포한 다음, Azure Blockchain Service에서 호스팅되는 블록체인 컨소시엄 네트워크에서 트랜잭션을 통해 함수를 호출했습니다.

> [!div class="nextstepaction"]
> [Azure Blockchain Service를 사용하여 블록체인 애플리케이션 개발](develop.md)
