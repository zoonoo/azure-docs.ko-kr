---
title: Azure 블록체인 서비스 개발 개요
description: Azure 블록체인 서비스에 대한 솔루션 개발에 대한 소개입니다.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348373"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 블록체인 서비스 개발 개요

Azure Blockchain Service를 사용하면 컨소시엄 블록 체인 네트워크를 만들어 자산 추적, 디지털 토큰, 충성도 및 보상, 공급망 금융 및 출처와 같은 엔터프라이즈 시나리오를 활성화 할 수 있습니다. 다음 섹션에서는 엔터프라이즈 블록 체인 솔루션을 구현하기 위한 Azure 블록 체인 서비스 개발을 소개합니다.

## <a name="connecting-to-azure-blockchain-service"></a>Azure 블록체인 서비스에 연결

전체 노드, 라이트 노드 및 원격 클라이언트를 포함하여 블록 체인 네트워크에 대한 다양한 유형의 클라이언트가 있습니다. Azure 블록 체인 서비스는 노드를 포함하는 블록 체인 네트워크를 구축합니다. 블록체인 개발을 위해 Azure 블록체인 서비스에 대한 게이트웨이로 다른 클라이언트를 사용할 수 있습니다. Azure 블록 체인 서비스는 기본 인증 또는 액세스 키를 개발 끝점으로 제공합니다. 다음은 연결을 사용할 수 있는 인기 있는 클라이언트입니다.

### <a name="visual-studio-code"></a>Visual Studio Code

Azure 블록 체인 개발 키트 비주얼 스튜디오 코드 확장을 사용하여 컨소시엄 멤버에 연결할 수 있습니다. 컨소시엄에 연결되면 스마트 계약을 Azure Blockchain Service 컨소시엄 멤버에 컴파일, 빌드 및 배포할 수 있습니다.

정교한 엔터프라이즈 블록 체인 솔루션을 개발하려면 다른 블록 체인 네트워크에 연결하고 스마트 계약 수명 주기를 관리하려면 개발 프레임 워크가 필요합니다. 대부분의 프로젝트는 적어도 두 개의 블록 체인 노드와 상호 작용합니다. 개발자는 개발 중에 로컬 블록 체인을 사용합니다. 응용 프로그램이 테스트 또는 릴리스할 준비가 되면 개발자는 블록 체인 네트워크에 배포합니다. 예를 들어, 주요 공용 이더리움 네트워크 또는 Azure 블록 체인 서비스. 비주얼 스튜디오 코드에서 이더리움 확장을 위한 Azure 블록 체인 개발 키트는 트러플을 사용합니다. 트러플은 이더리움 블록체인에서 분산형 애플리케이션을 작성, 컴파일, 배포 및 테스트하는 인기 있는 블록체인 개발 프레임워크입니다. 트뤼플은 스마트 계약 개발과 기존 웹 개발을 원활하게 통합하려는 프레임워크라고 생각할 수도 있습니다.

자세한 내용은 [빠른 시작: Visual Studio 코드를 사용하여 Azure 블록 체인 서비스 컨소시엄 네트워크에 연결하십시오.](connect-vscode.md)

### <a name="metamask"></a>메타 마스크

MetaMask는 브라우저 기반 지갑(원격 클라이언트), RPC 클라이언트 및 기본 계약 탐색기입니다. 다른 브라우저 지갑과 달리, 메타 마스크는 브라우저 자바 스크립트 컨텍스트에 웹3 인스턴스를 주입, 이더리움 블록 체인의 다양한에 연결하는 RPC 클라이언트 역할을 *(메인넷,* *롭스텐 테스트 넷,* *코반 테스트 넷,* 로컬 RPC 노드, 기타). 사용자 지정 RPC를 쉽게 설정하여 Azure 블록 체인 서비스에 연결하고 Remix를 사용하여 블록 체인 개발을 시작할 수 있습니다.

자세한 내용은 [빠른 시작: MetaMask를 사용하여 스마트 계약을 연결하고 배포하는 경우를](connect-metamask.md) 참조하십시오.

### <a name="geth"></a>게 (것)게

Geth는 Go에서 구현된 전체 이더리움 노드를 실행하기 위한 명령줄 인터페이스입니다. 전체 노드를 실행할 필요는 없지만 Azure 블록 체인 서비스와 상호 작용하기 위해 JavaScript API를 노출하는 JavaScript 런타임 환경을 제공하는 대화형 콘솔을 시작할 수 있습니다.

자세한 내용은 [빠른 시작: Geth를 사용하여 Azure 블록 체인 서비스 트랜잭션 노드에 연결합니다.](connect-geth.md)

## <a name="ethereum-quorum-private-transactions"></a>이더리움 쿼럼 개인 거래

쿼럼은 거래 플러스 계약 개인 정보 보호 및 새로운 합의 메커니즘이있는 이더리움 기반의 분산 원장 프로토콜입니다. Go-Ethereum에 대한 주요 개선 사항은 다음과 같습니다.

* **개인 정보 보호** - 쿼럼은 공공 및 민간 국가 분리를 통해 개인 거래 및 개인 계약을 지원하고 네트워크 참가자에게 개인 데이터를 직접 전송하기 위해 피어 투 피어 암호화 된 메시지 교환을 활용합니다.
* **대체 합의 메커니즘** - 허가된 네트워크에는 작업 증명 또는 지분 증명 합의가 필요하지 않습니다. 쿼럼은 RAFT 및 IBFT와 같은 컨소시엄 체인을 위해 설계된 여러 합의 메커니즘을 제공합니다.Azure 블록 체인 서비스는 IBFT 합의 메커니즘을 사용합니다.
* **피어 권한 부여** - 스마트 계약을 사용하는 노드 및 피어 권한 은 알려진 당사자만 네트워크에 참여할 수 있도록 합니다.
* **더 높은 성능** - 쿼럼은 Public Geth보다 더 높은 성능을 제공합니다.

## <a name="block-explorers"></a>탐색기 차단

블록 탐색기는 개별 블록 콘텐츠, 트랜잭션 주소 데이터 및 기록을 표시하는 온라인 블록 체인 브라우저입니다. 기본 블록 정보는 Azure 블록 체인 서비스의 Azure 모니터를 통해 사용할 수 있습니다. 그러나 개발 중에 자세한 정보가 필요한 경우 블록 탐색기가 유용할 수 있습니다.  다음 블록 탐색기는 Azure 블록 체인 서비스와 함께 작동합니다.

* 웹3 랩의 [에피루스 Azure 블록체인 서비스 탐색기](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [블록 스카우트](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

또한 블록 체인 데이터 관리자 및 Azure Cosmos DB를 [Tutorial: Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)사용하여 자신의 블록 탐색기를 빌드할 수 있습니다.

## <a name="tps-measurement"></a>TPS 측정

블록체인이 더 많은 엔터프라이즈 시나리오에서 사용되기 때문에 병목 현상과 시스템 비효율성을 피하기 위해 TPS(초당 트랜잭션 속도)가 중요합니다. 높은 거래 속도는 분산 된 블록 체인 내에서 유지하기 어려울 수 있습니다. 정확한 TPS 측정은 서버 스레드, 트랜잭션 큐 크기, 네트워크 대기 시간 및 보안과 같은 다양한 요인의 영향을 받을 수 있습니다. 개발 중에 TPS 속도를 측정해야하는 경우 인기있는 오픈 소스 도구는 [ChainHammer입니다.](https://github.com/drandreaskrueger/chainhammer)

## <a name="next-steps"></a>다음 단계

이더리움용 Azure 블록체인 개발 키트를 사용하여 Azure 블록체인 서비스의 컨소시엄에 연결하는 빠른 시작을 시도해 보십시오.

> [!div class="nextstepaction"]
> [Visual Studio Code를 사용하여 Azure Blockchain Service에 연결](connect-vscode.md)