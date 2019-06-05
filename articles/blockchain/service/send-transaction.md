---
title: Azure Blockchain Service를 사용하여 트랜잭션 보내기
description: Azure Blockchain Service를 사용하여 스마트 계약을 배포하고 프라이빗 트랜잭션을 보내는 방법에 대한 자습서입니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0b5e39e9cf2fc3ffe91db6587bc1ed1bab079e93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65777334"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>자습서: Azure Blockchain Service를 사용하여 트랜잭션 보내기

이 자습서에서는 계약 및 트랜잭션 개인 정보를 테스트할 트랜잭션 노드를 만듭니다.  Truffle을 사용하여 로컬 개발 환경을 만들고 스마트 계약을 배포하여 프라이빗 트랜잭션을 보냅니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 트랜잭션 노드 추가
> * Truffle을 사용하여 스마트 계약 배포
> * 트랜잭션 보내기
> * 트랜잭션 개인 정보에 대한 유효성 검사

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md)를 완료합니다.
* [빠른 시작: Truffle을 사용하여 컨소시엄 네트워크에 연결](connect-truffle.md)을 완료합니다.
* Truffle을 사용하려면 [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 및 [Truffle](https://github.com/trufflesuite/truffle)을 포함한 여러 도구를 설치해야 합니다.

    Windows 10에 빠르게 설치하려면 Unix Bash 셸 터미널용 [Ubuntu on Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6)를 설치한 다음, [Truffle](https://github.com/trufflesuite/truffle)을 설치합니다. Ubuntu on Windows 배포에는 Node.js 및 Git이 포함되어 있습니다.

* [Visual Studio Code](https://code.visualstudio.com/Download)를 설치합니다.
* [Visual Studio Code Solidity 확장](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)을 설치합니다.

## <a name="create-transaction-nodes"></a>트랜잭션 노드 만들기

기본적으로 하나의 트랜잭션 노드가 있습니다. 2개를 더 추가할 것입니다. 노드 중 하나는 프라이빗 트랜잭션에 참여합니다. 다른 하나는 프라이빗 트랜잭션에 포함되지 않습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain 멤버로 이동하여 **트랜잭션 노드 > 추가**를 차례로 선택합니다.
1. `alpha`라는 새 트랜잭션 노드에 대한 설정을 완료합니다.

    ![트랜잭션 노드 만들기](./media/send-transaction/create-node.png)

    | 설정 | 값 | 설명 |
    |---------|-------|-------------|
    | Name | `alpha` | 트랜잭션 노드 이름입니다. 트랜잭션 노드 엔드포인트에 대한 DNS 주소를 만드는 데 사용됩니다. 예: `alpha-mymanagedledger.blockchain.azure.com` |
    | 암호 | 강력한 암호 | 기본 인증을 사용 하여 트랜잭션 노드 엔드포인트에 액세스하는 데 사용됩니다.

1. **만들기**를 선택합니다.

    새 트랜잭션 노드를 프로비저닝하는 데 약 10분이 걸립니다.

1. 2~4단계를 반복하여 `beta`라는 트랜잭션 노드를 추가합니다.

노드가 프로비저닝되는 동안 자습서를 계속할 수 있습니다. 프로비저닝이 완료되면 3개의 트랜잭션 노드가 있습니다.

## <a name="open-truffle-project"></a>Truffle 프로젝트 열기

1. Bash 셸 터미널을 엽니다.
1. [빠른 시작: Truffle을 사용하여 컨소시엄 네트워크에 연결](connect-truffle.md)의 필수 조건에서 경로를 Truffle 프로젝트 디렉터리로 변경합니다. 예를 들면 다음과 같습니다.

    ```bash
    cd truffledemo
    ```

1. Truffle의 대화형 개발 콘솔을 시작합니다.

    ``` bash
    truffle develop
    ```

    Truffle이 로컬 개발 블록체인을 만들고 대화형 콘솔을 제공합니다.

## <a name="connect-to-transaction-node"></a>트랜잭션 노드에 연결

Web3을 사용하여 기본 트랜잭션 노드에 연결하고 계정을 만듭니다. Web3 연결 문자열은 Azure Portal에서 가져올 수 있습니다.

1. Azure Portal에서 기본 트랜잭션 노드로 이동하여 **트랜잭션 노드 > 샘플 코드 > Web3**을 차례로 선택합니다.
1. **HTTPS(액세스 키 1)** 에서 JavaScript를 복사합니다. ![Web3 샘플 코드](./media/send-transaction/web3-code.png)

1. 기본 트랜잭션 노드에 대한 Web3 JavaScript 코드를 Truffle 대화형 개발 콘솔에 붙여넣습니다. 이 코드는 Azure Blockchain Service 트랜잭션 노드에 연결되는 Web3 개체를 만듭니다.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Web3 개체에서 메서드를 호출하여 트랜잭션 노드와 상호 작용할 수 있습니다.

1. 새 계정을 기본 트랜잭션 노드에 만듭니다. 암호 매개 변수를 사용자 고유의 강력한 암호로 바꿉니다.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    반환된 계정 주소와 다음 섹션에서 사용한 암호를 적어 두세요.

1. Truffle 개발 환경을 종료합니다.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Truffle 프로젝트 구성

Truffle 프로젝트를 구성하려면 Azure Portal의 일부 트랜잭션 노드 정보가 필요합니다.

### <a name="transaction-node-public-key"></a>트랜잭션 노드 공개 키

각 트랜잭션 노드에는 공개 키가 있습니다. 프라이빗 키를 사용하면 개인 트랜잭션을 노드에 보낼 수 있습니다. 트랜잭션을 기본 트랜잭션 노드에서 *alpha* 트랜잭션 노드에 보내려면 *alpha* 트랜잭션 노드의 공개 키가 필요합니다.

공개 키는 트랜잭션 노드 목록에서 가져올 수 있습니다. alpha 노드의 공개 키를 복사하고 나중에 자습서의 뒷부분에서 사용할 값을 저장합니다.

![트랜잭션 노드 목록](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>트랜잭션 노드 엔드포인트 주소

1. Azure Portal에서 각 트랜잭션 노드로 이동하여 **트랜잭션 노드 > 연결 문자열**을 차례로 선택합니다.
1. 각 트랜잭션 노드에 대한 **HTTPS(액세스 키 1)** 에서 엔드포인트 URL을 복사하여 저장합니다. 자습서의 뒷부분에 있는 스마트 계약 구성 파일에 대한 엔드포인트 주소가 필요합니다.

    ![트랜잭션 엔드포인트 주소](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>구성 파일 편집

1. Visual Studio Code를 시작하고, **파일 > 폴더 열기** 메뉴를 사용하여 Truffle 프로젝트 디렉터리 폴더를 엽니다.
1. `truffle-config.js` Truffle 구성 파일을 엽니다.
1. 파일의 내용을 다음 구성 정보로 바꿉니다. 엔드포인트 주소 및 계정 정보가 포함된 변수를 추가합니다. 꺾쇠 괄호 섹션을 이전 섹션에서 수집한 값으로 바꿉니다.

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

구성 코드를 구성의 **module.exports** 섹션에 추가합니다.

```javascript
module.exports = {
  networks: {
    defaultnode: {
      provider:(() =>  {
      const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);

      const web3 = new Web3(AzureBlockchainProvider);
      web3.eth.personal.unlockAccount(myAccount, myPassword);

      return AzureBlockchainProvider;
      })(),

      network_id: "*",
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>스마트 계약 만들기

**contracts** 폴더에서 `SimpleStorage.sol`이라는 새 파일을 만듭니다. 다음 코드를 추가합니다.

```solidity
pragma solidity >=0.4.21 <0.6.0;

contract SimpleStorage {
    string public storedData;

    constructor(string memory initVal) public {
        storedData = initVal;
    }

    function set(string memory x) public {
        storedData = x;
    }

    function get() view public returns (string memory retVal) {
        return storedData;
    }
}
```

**miginations** 폴더에서 `2_deploy_simplestorage.js`라는 새 파일을 만듭니다. 다음 코드를 추가합니다.

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

꺾쇠 괄호의 값을 바꿉니다.

| 값 | 설명
|-------|-------------
| \<alpha 노드 공개 키\> | alpha 노드의 공개 키
| \<계정 주소\> | 기본 트랜잭션 노드에 만든 계정 주소

이 예제에서는 **storeData** 값의 초기 값이 42로 설정되었습니다.

**privateFor**은 계약을 사용할 수 있는 노드를 정의합니다. 이 예제에서는 기본 트랜잭션 노드의 계정에서 프라이빗 트랜잭션을 **alpha** 노드에 캐스팅할 수 있습니다. 모든 프라이빗 트랜잭션 참가자에 대한 공개 키를 추가해야 합니다. **privateFor:** 및 **from:** 이 포함되지 않으면 스마트 계약 트랜잭션이 공개되어 모든 컨소시엄 구성원이 볼 수 있습니다.

**파일 > 모두 저장**을 차례로 선택하여 모든 파일을 저장합니다.

## <a name="deploy-smart-contract"></a>스마트 계약 배포

Truffle을 사용하여 `SimpleStorage.sol`을 기본 트랜잭션 노드 네트워크에 배포합니다.

```bash
truffle migrate --network defaultnode
```

Truffle에서 먼저 **SimpleStorage** 스마트 계약을 컴파일한 다음, 배포합니다.

예제 출력:

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>계약 개인 정보에 대한 유효성 검사

계약 개인 정보 보호로 인해 계약 값은 **privateFor**에서 선언한 노드에서만 쿼리할 수 있습니다. 다음 예제에서는 계정이 해당 노드에 있으므로 기본 트랜잭션 노드를 쿼리할 수 있습니다. Truffle 콘솔을 사용하여 기본 트랜잭션 노드에 연결합니다.

```bash
truffle console --network defaultnode
```

계약 인스턴스의 값을 반환하는 명령을 실행합니다.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

기본 트랜잭션 노드를 쿼리하는 데 성공하면 42라는 값이 반환됩니다.

예제 출력:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

콘솔을 종료합니다.

```bash
.exit
```

**privateFor**에서 **alpha** 노드의 공개 키를 선언했으므로 **alpha** 노드를 쿼리할 수 있습니다. Truffle 콘솔을 사용하여 **alpha** 노드에 연결합니다.

```bash
truffle console --network alpha
```

계약 인스턴스의 값을 반환하는 명령을 실행합니다.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

**alpha** 노드를 쿼리하는 데 성공하면 42라는 값이 반환됩니다.

예제 출력:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

콘솔을 종료합니다.

```bash
.exit
```

**privateFor**에서 **beta** 노드의 공개 키를 선언하지 않았으므로 계약 개인 정보 보호로 인해 **beta** 노드를 쿼리할 수 없습니다. Truffle 콘솔을 사용하여 **beta** 노드에 연결합니다.

```bash
truffle console --network beta
```

계약 인스턴스의 값을 반환하는 명령을 실행합니다.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

계약이 프라이빗이므로 **beta** 노드를 쿼리하는 데 실패합니다.

예제 출력:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

콘솔을 종료합니다.

```bash
.exit
```

## <a name="send-a-transaction"></a>트랜잭션 보내기

`sampletx.js`라는 파일을 만듭니다. 프로젝트의 루트에 저장합니다.

이 스크립트는 계약 **storedData** 변수 값을 65로 설정합니다. 코드를 새 파일에 추가합니다.

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
  }).then(function(result) {
    console.log("Transaction:", result.tx);
    console.log("Finished!");
    done();
  }).catch(function(e) {
    console.log(e);
    done();
  });
};
```

꺾쇠 괄호 안의 값을 바꾼 다음, 파일을 저장합니다.

| 값 | 설명
|-------|-------------
| \<alpha 노드 공개 키\> | alpha 노드의 공개 키
| \<계정 주소\> | 기본 트랜잭션 노드에 만든 계정 주소

**privateFor**은 트랜잭션을 사용할 수 있는 노드를 정의합니다. 이 예제에서는 기본 트랜잭션 노드의 계정에서 프라이빗 트랜잭션을 **alpha** 노드에 캐스팅할 수 있습니다. 모든 프라이빗 트랜잭션 참가자에 대한 공개 키를 추가해야 합니다.

Truffle을 사용하여 기본 트랜잭션 노드에 대한 스크립트를 실행합니다.

```bash
truffle exec sampletx.js --network defaultnode
```

계약 인스턴스의 값을 반환하는 명령을 실행합니다.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

트랜잭션이 성공하면 값 65라는 값이 반환됩니다.

예제 출력:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

콘솔을 종료합니다.

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>트랜잭션 개인 정보에 대한 유효성 검사

트랜잭션 개인 정보 보호로 인해 트랜잭션은 **privateFor**에서 선언한 노드에서만 수행할 수 있습니다. 다음 예제에서는 **privateFor**에서 **alpha** 노드의 공개 키를 선언했으므로 트랜잭션을 수행할 수 있습니다. Truffle을 사용하여 **alpha** 노드에서 트랜잭션을 실행합니다.

```bash
truffle exec sampletx.js --network alpha
```

계약 인스턴스의 값을 반환하는 명령을 실행합니다.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

트랜잭션이 성공하면 값 65라는 값이 반환됩니다.

예제 출력:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

콘솔을 종료합니다.

```bash
.exit
```

이 자습서에서는 계약 및 트랜잭션 개인 정보를 표시할 두 개의 트랜잭션 노드를 추가했습니다. 기본 노드를 사용하여 프라이빗 스마트 계약을 배포했습니다. 계약 값을 쿼리하고 블록체인에서 트랜잭션을 수행하여 개인 정보를 테스트했습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure Blockchain Service에서 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Service를 사용하여 블록체인 애플리케이션 개발](develop.md)
