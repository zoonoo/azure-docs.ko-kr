---
title: Azure Blockchain 서비스 개발 개요
description: Azure Blockchain 서비스에서 솔루션을 개발 하는 방법을 소개 합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 2990917243507d93cb6a86da11cf771f3a42eebd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298419"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure Blockchain 서비스 개발 개요

Azure Blockchain 서비스를 사용 하면 컨소시엄 Blockchain 네트워크를 만들어 자산 추적, 디지털 토큰, 충성도 및 보상, 공급망 재무, provenance 등의 엔터프라이즈 시나리오를 사용할 수 있습니다. 이 문서는 엔터프라이즈에 대 한 Blockchain을 구현 하는 Azure Blockchain 서비스 개발 및 주요 주제를 소개 합니다.

## <a name="client-connection-to-azure-blockchain-service"></a>Azure Blockchain 서비스에 대 한 클라이언트 연결

블록 체인 네트워크에는 전체 노드, 밝은 노드 및 원격 클라이언트를 비롯 한 다양 한 유형의 클라이언트가 있습니다. Azure Blockchain 서비스는 노드를 포함 하는 블록 체인 네트워크를 구축 합니다. 블록 체인 개발을 위해 Azure Blockchain 서비스에 대 한 게이트웨이로 다른 클라이언트를 사용할 수 있습니다. Azure Blockchain 서비스는 기본 인증 또는 액세스 키를 개발 끝점으로 제공 합니다. 다음은 연결을 사용할 수 있는 인기 있는 클라이언트입니다.

### <a name="metamask"></a>MetaMask

MetaMask는 브라우저 기반 전자 지갑 (원격 클라이언트), RPC 클라이언트 및 기본 계약 탐색기입니다. 다른 브라우저 wallets와 달리 MetaMask는 web3 인스턴스를 브라우저 JavaScript 컨텍스트에 삽입 하 고, 다양 한 Ethereum 블록 체인 (*mainnet*, *Ropsten testnet*, *Kovan testnet*, 로컬 rpc 노드, 등). 사용자 지정 RPC를 쉽게 설정 하 여 Azure Blockchain 서비스에 연결 하 고 Remix를 사용 하 여 블록 체인 개발을 시작할 수 있습니다.

### <a name="geth"></a>Geth

Geth는 Go에서 구현 된 전체 Ethereum 노드를 실행 하기 위한 명령줄 인터페이스입니다. 전체 노드를 실행할 필요는 없지만, Azure Blockchain 서비스와 상호 작용 하는 JavaScript API를 제공 하는 JavaScript 런타임 환경을 제공 하는 대화형 콘솔을 시작할 수 있습니다.

## <a name="development-framework-configuration"></a>개발 프레임 워크 구성

정교한 엔터프라이즈 블록 체인 솔루션을 개발 하려면 다양 한 blockchain 네트워크에 연결 하 고, 스마트 계약 수명 주기를 관리 하 고, 테스트를 자동화 하 고, 스크립트를 사용 하 여 스마트 계약을 배포 하 고, 대화형 콘솔을 사용 해야 합니다.

Truffle는 Ethereum blockchain에서 분산 된 응용 프로그램을 작성, 컴파일, 배포 및 테스트 하기 위한 인기 있는 blockchain 개발 프레임 워크입니다. 또한 스마트 계약 개발과 기존 웹 개발을 원활 하 게 통합 하려고 하는 프레임 워크로 Truffle을 생각할 수 있습니다.

가장 작은 프로젝트도 두 개 이상의 blockchain 노드와 상호 작용 합니다. 하나는 개발자의 컴퓨터에 있고, 다른 하나는 개발자가 응용 프로그램을 배포 하는 네트워크를 나타냅니다. 예를 들어, main public Ethereum network 또는 Azure Blockchain Service입니다. Truffle는 각 네트워크에 대 한 컴파일 및 배포 아티팩트를 관리 하는 시스템을 제공 하며, 최종 응용 프로그램 배포를 간소화 하는 방식으로 수행 합니다. 자세한 내용은 [빠른 시작: Truffle를 사용 하 여 Azure Blockchain 서비스 네트워크 @ no__t-0에 연결 합니다.

## <a name="ethereum-quorum-private-transaction"></a>Ethereum 쿼럼 개인 트랜잭션

쿼럼은 트랜잭션 및 계약 개인 정보 및 새로운 합의 메커니즘과 함께 Ethereum 기반 분산 원장 프로토콜입니다. Ethereum에 대 한 주요 향상 된 기능은 다음과 같습니다.

* 개인 정보-쿼럼은 공용 및 개인 상태 분리를 통해 개인 트랜잭션과 개인 계약을 지원 하 고 개인 데이터를 네트워크 참가자에 게 전달 하기 위해 피어 투 피어 암호화 된 메시지 교환을 활용 합니다.
* 대체 관련 메커니즘-permissioned 네트워크에서 작업 증명 또는 stake의 대/소문자를 구분 하는 데 필요 하지 않습니다. 쿼럼은 래 프 트 및 IBFT와 같은 컨소시엄 체인을 위해 설계 된 여러 가지 합의 메커니즘을 제공 합니다.  Azure Blockchain 서비스는 IBFT 합의 메커니즘을 사용 합니다.

* 피어 권한 부여-스마트 계약을 사용 하 여 노드 및 피어 권한 부여-알려진 당사자만 네트워크에 연결할 수 있도록 합니다.
* 높은 성능-쿼럼이 공개 지역 보다 높은 성능을 제공 합니다.

단계별 지침은 [자습서: 개인 트랜잭션 예제를 보려면 Azure Blockchain Service @ no__t-0을 사용 하 여 트랜잭션을 보냅니다.

## <a name="block-explorers"></a>블록 탐색기

블록 탐색기는 개별 블록 콘텐츠, 트랜잭션 주소 데이터 및 기록을 표시 하는 온라인 blockchain 브라우저입니다. 기본 블록 정보는 Azure Blockchain 서비스의 Azure Monitor를 통해 사용할 수 있지만, 개발 하는 동안 자세한 정보를 필요로 하는 경우 블록 탐색기가 유용할 수 있습니다.  사용할 수 있는 인기 있는 오픈 소스 블록 탐색기가 있습니다. 다음은 Azure Blockchain 서비스를 사용 하는 블록 탐색기의 목록입니다.

* Web3 Labs의 [Epirus Azure Blockchain 서비스 탐색기](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS 측정

블록 체인은 더 많은 엔터프라이즈 시나리오에서 사용 되므로, 병목 현상 및 시스템의 비효율성을 방지 하려면 TPS (초당 트랜잭션 수) 속도가 중요 합니다. 높은 트랜잭션 요금은 분산 된 블록 체인 내에서 유지 관리 하기 어려울 수 있습니다. 정확한 TPS 측정은 서버 스레드, 트랜잭션 큐 크기, 네트워크 대기 시간, 보안 등의 여러 가지 요인에 의해 영향을 받을 수 있습니다. 개발 중에 TPS 속도를 측정 해야 할 경우 널리 사용 되는 오픈 소스 도구가 [ChainHammer](https://github.com/drandreaskrueger/chainhammer)됩니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: Truffle를 사용 하 여 Azure Blockchain 서비스 네트워크에 연결 @ no__t-0
