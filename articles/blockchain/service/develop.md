---
title: Azure Blockchain Service 개발 개요
description: Azure Blockchain Service에서 솔루션을 개발하는 방법을 소개합니다.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80348373"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure Blockchain Service 개발 개요

Azure Blockchain Service를 사용하면 컨소시엄 블록체인 네트워크를 만들어 자산 추적, 디지털 토큰, 충성도 및 보상, 공급망 재무, 출처 등의 엔터프라이즈 시나리오를 사용할 수 있습니다. 다음 섹션에서는 엔터프라이즈 블록체인 솔루션을 구현하기 위한 Azure Blockchain Service 개발을 소개합니다.

## <a name="connecting-to-azure-blockchain-service"></a>Azure Blockchain Service에 연결

블록체인 네트워크에는 전체 노드, 밝은 노드, 원격 클라이언트를 포함한 다양한 유형의 클라이언트가 있습니다. Azure Blockchain Service는 노드를 포함한 블록체인 네트워크를 빌드합니다. 블록체인 개발을 위해 Azure Blockchain Service에 대한 게이트웨이로 다른 클라이언트를 사용할 수 있습니다. Azure Blockchain Service는 기본 인증 또는 액세스 키를 개발 엔드포인트로 제공합니다. 다음은 연결을 사용할 수 있는 인기 클라이언트입니다.

### <a name="visual-studio-code"></a>Visual Studio Code

Azure Blockchain 개발 키트 Visual Studio Code 확장을 사용하여 컨소시엄 멤버에 연결할 수 있습니다. 컨소시엄에 연결되면 스마트 계약을 Azure Blockchain Service 컨소시엄 멤버에 컴파일, 빌드 및 배포할 수 있습니다.

정교한 엔터프라이즈 블록체인 솔루션을 개발하려면 다양한 블록체인 네트워크에 연결하고 스마트 계약 수명 주기를 관리하기 위한 개발 프레임워크가 필요합니다. 대부분의 프로젝트는 두 개 이상의 블록체인 노드와 상호 작용합니다. 개발자는 개발 중에 로컬 블록체인을 사용합니다. 애플리케이션을 테스트 또는 릴리스할 준비가 되면 개발자가 블록체인 네트워크에 배포합니다. 예를 들어 기본 퍼블릭 Ethereum 네트워크 또는 Azure Blockchain Service가 있습니다. Visual Studio Code의 Ethereum 확장에 대한 Azure Blockchain 개발 키트는 Truffle을 사용합니다. Truffle은 Ethereum 블록체인에서 분산된 애플리케이션을 작성, 컴파일, 배포, 테스트하기 위한 인기 블록체인 개발 프레임워크입니다. 또한 Truffle을 스마트 계약 개발과 기존 웹 개발을 원활하게 통합하려고 시도하는 프레임워크로 생각할 수 있습니다.

자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Azure Blockchain Service 컨소시엄 네트워크에 연결](connect-vscode.md)을 참조하세요.

### <a name="metamask"></a>MetaMask

MetaMask는 브라우저 기반 지갑(원격 클라이언트), RPC 클라이언트, 기본 계약 탐색기입니다. 다른 브라우저 지갑과 달리 MetaMask는 web3 인스턴스를 브라우저 JavaScript 컨텍스트에 삽입하고 다양한 Ethereum 블록체인(*mainnet*, *Ropsten testnet*, *Kovan testnet*, local RPC node 등)에 연결하는 RPC 클라이언트 역할을 수행합니다. 간편하게 사용자 지정 RPC를 설정하여 Azure Blockchain Service에 연결하고 Remix를 사용하여 블록체인 개발을 시작할 수 있습니다.

자세한 내용은 [빠른 시작: MetaMask를 사용하여 스마트 계약 연결 및 배포](connect-metamask.md)를 참조하세요.

### <a name="geth"></a>Geth

Geth는 Go에서 구현된 전체 Ethereum 노드를 실행하기 위한 명령줄 인터페이스입니다. 전체 노드를 실행할 필요는 없지만 Azure Blockchain Service와 상호 작용하는 JavaScript API를 제공하는 JavaScript 런타임 환경을 제공하는 대화형 콘솔을 시작할 수 있습니다.

자세한 내용은 [빠른 시작: Geth를 사용하여 Azure Blockchain Service 트랜잭션 노드에 연결](connect-geth.md)을 참조하세요.

## <a name="ethereum-quorum-private-transactions"></a>Ethereum Quorum 프라이빗 트랜잭션

Quorum은 트랜잭션 및 계약 개인 정보 및 새로운 합의 메커니즘을 사용하는 Ethereum 기반 분산 원장 프로토콜입니다. Go-Ethereum에 대해 향상된 주요 기능은 다음과 같습니다.

* **개인 정보** - Quorum은 퍼블릭 및 프라이빗 상태 분리를 통해 프라이빗 트랜잭션과 프라이빗 계약을 지원하고 프라이빗 데이터를 네트워크 참가자에게 전달하기 위해 피어 투 피어 암호화된 메시지 교환을 활용합니다.
* **대체 합의 메커니즘** - 작업 증명 또는 관련 증명 합의는 권한 있는 네트워크에는 필요하지 않습니다. Quorum은 RAFT 및 IBFT와 같은 컨소시엄 체인을 위해 설계된 여러 합의 메커니즘을 제공합니다.  Azure Blockchain Service는 IBFT 합의 메커니즘을 사용합니다.
* **피어 권한 부여** - 스마트 계약을 사용하는 노드 및 피어 권한 부여는 알려진 당사자만이 네트워크에 조인할 수 있도록 합니다.
* **고성능** - Quorum은 퍼블릭 Geth보다 더 높은 성능을 제공합니다.

## <a name="block-explorers"></a>블록 탐색기

블록 탐색기는 개별 블록 콘텐츠, 트랜잭션 주소 데이터, 기록을 표시하는 온라인 블록체인 브라우저입니다. 기본 블록 정보는 Azure Blockchain Service의 Azure Monitor를 통해 사용할 수 있습니다. 그러나 개발하는 동안 자세한 정보가 필요한 경우 블록 탐색기가 유용할 수 있습니다.  다음 블록 탐색기는 Azure Blockchain Service를 사용하여 작업합니다.

* Web3 Labs의 [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Blockchain Data Manager 및 Azure Cosmos DB를 사용하여 사용자 고유의 블록 탐색기를 빌드할 수도 있습니다. [자습서: Blockchain Data Manager를 사용하여 데이터를 Azure Cosmos DB에 전송](data-manager-cosmosdb.md)을 참조하세요.

## <a name="tps-measurement"></a>TPS 측정

블록체인은 더 많은 엔터프라이즈 시나리오에서 사용되므로 병목 상태 및 시스템의 비효율성을 방지하려면 TPS(초당 트랜잭션 수) 속도가 중요합니다. 높은 트랜잭션 속도는 분산된 블록체인 내에서 유지 관리하기 어려울 수 있습니다. 정확한 TPS 측정은 서버 스레드, 트랜잭션 큐 크기, 네트워크 대기 시간, 보안 등의 여러 가지 요인에 의해 영향을 받을 수 있습니다. 개발 중에 TPS 속도를 측정해야 하는 경우 인기 오픈 소스 도구는 [ChainHammer](https://github.com/drandreaskrueger/chainhammer)입니다.

## <a name="next-steps"></a>다음 단계

Ethereum용 Azure Blockchain 개발 키트를 사용하여 Azure Blockchain Service의 컨소시엄에 연결하고 빠르게 시작해 보세요.

> [!div class="nextstepaction"]
> [Visual Studio Code를 사용하여 Azure Blockchain Service에 연결](connect-vscode.md)