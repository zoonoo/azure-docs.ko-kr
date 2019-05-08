---
title: Azure Blockchain Service 개발 개요
description: Azure Blockchain 서비스 솔루션 개발에 대해 소개 합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027902"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure Blockchain Service 개발 개요

Azure Blockchain 서비스를 사용 하 여 컨소시엄 블록 체인 네트워크 자산 추적, 디지털 토큰, 고객 충성도 및 reward, 금융, 공급망 및 provenance와 같은 엔터프라이즈 시나리오를 사용 하도록 만들 수 있습니다. 이 문서는 Azure Blockchain Service 개발 개요와 엔터프라이즈에 대 한 블록 체인을 구현 하는 키 항목을 소개 합니다.

## <a name="client-connection-to-azure-blockchain-service"></a>Azure Blockchain 서비스에 클라이언트 연결

전체 노드, 연한 노드 및 원격 클라이언트를 포함 하 여 블록 체인 네트워크에 대 한 클라이언트의 종류가 있습니다. Azure Blockchain 서비스 노드를 포함 하는 블록 체인 네트워크를 빌드합니다. 블록 체인 개발에 대 한 게이트웨이로 Azure 블록 체인에 다른 클라이언트를 사용할 수 있습니다. Azure Blockchain 서비스 개발 끝점으로 기본 인증 또는 액세스 키를 제공합니다. 다음은 사용할 수는 인기 있는 클라이언트에 연결 합니다.

### <a name="metamask"></a>MetaMask

MetaMask는 브라우저 기반 wallet (원격 클라이언트), RPC 클라이언트 및 기본 계약 탐색기. 다른 브라우저 신분증 달리 MetaMask 삽입 web3 인스턴스를 브라우저 JavaScript 컨텍스트로 다양 한 Ethereum 블록 체인에 연결 하는 RPC 클라이언트 역할 (*mainnet*, *Ropsten testnet*, *Kovan testnet*, 로컬 RPC 노드 등.). 쉽게 연결 하 여 Azure Blockchain Service Remix를 사용 하 여 블록 체인 개발을 시작 하는 사용자 지정 RPC를 설정할 수 있습니다.

### <a name="geth"></a>Geth

Geth는 Go에서 구현 되는 전체 Ethereum 노드를 실행 하기 위한 명령줄 인터페이스입니다. 실행할 필요가 노드 전체 하지만 Azure Blockchain 서비스와 상호 작용 하는 JavaScript API를 노출 하는 JavaScript 런타임 환경을 제공 하는 대화형 콘솔을 시작할 수 있습니다.

## <a name="development-framework-configuration"></a>개발 프레임 워크 구성

정교한 엔터프라이즈 블록 체인 솔루션을 개발 하려면 다른 블록 체인 네트워크에 연결, 스마트 계약의 수명 주기 관리, 테스트 자동화 스크립트를 사용 하 여 스마트 계약을 배포 및 대화형 콘솔을 언제 든 지 개발 프레임 워크 필요 합니다.

Truffle 작성, 컴파일, 배포 및 Ethereum 블록 체인에 분산 된 응용 프로그램을 테스트 하는 인기 있는 blockchain 개발 프레임 워크 이며 스마트 계약 및 기존 웹 개발을 원활 하 게 통합 하려고 하는 프레임 워크로 Truffle 생각할 수도 있습니다.

가장 작은 프로젝트 라도 블록 체인 노드 두 개 이상의 상호 작용: 개발자의 컴퓨터 및 다른 개발자가 응용 프로그램을 배포 하는 위치는 네트워크를 나타내는 하나입니다. 기본 공용 Ethereum 네트워크 또는 Azure Blockchain 서비스 예를 들어 있습니다. Truffle 각 네트워크에 대 한 컴파일 및 배포 아티팩트를 관리 하기 위한 시스템을 제공 하 고 최종 응용 프로그램 배포를 간소화 하는 방식으로 수행 합니다. 자세한 내용은 [빠른 시작: Truffle 연결할 때 사용 하는 Azure Blockchain 서비스 네트워크를](connect-truffle.md)입니다.

## <a name="ethereum-quorum-private-transaction"></a>Ethereum 쿼럼 개인 트랜잭션

쿼럼에 트랜잭션 plus 계약 개인 정보 및 새로운 합의 메커니즘을 사용 하 여 분산된 원장의 Ethereum 기반 프로토콜입니다. Go Ethereum을 통해 주요 개선 사항은 다음과 같습니다.

* 개인 정보-쿼럼 개인 트랜잭션 및 공용 및 개인 상태 분리를 통해 개인 계약 지원 하 고 네트워크 참가자에 게 개인 데이터의 방향이 지정 된 전송에 대 한 피어 투 피어 암호화 된 메시지 교환을 사용 합니다.
* 대체 합의 메커니즘-권한 있는 네트워크에서 작업 증명 또는 단적인의 증명 합의 필요 하지 않습니다. 쿼럼 여러 합의 메커니즘으로 설계 된 래 등 IBFT 컨소시엄 체인에 대 한 제공 합니다.  Azure Blockchain 서비스 IBFT 합의 메커니즘을 사용 합니다.

* 권한 부여-피어 노드 및 피어 권한 부여를 스마트 계약으로 알려진된 파티 에서만 확인을 사용 하 여 네트워크에 참가할 수
* 더 높은 성능-쿼럼 공용 Geth 보다 더 높은 성능을 제공 합니다.

단계별 지침은 [자습서: Azure Blockchain 서비스를 사용 하는 트랜잭션이 전송](send-transaction.md) 개인 트랜잭션의 예입니다.

## <a name="block-explorers"></a>블록 탐색기

블록 탐색기는 개별 블록 콘텐츠, 트랜잭션 주소 데이터 및 기록을 표시 하는 온라인 blockchain 브라우저입니다. 그러나 기본 블록 정보를 사용할 수 있는 Azure Blockchain Service에서 Azure Monitor를 통해 개발 하는 동안 자세한 정보를 해야 하는 경우 블록 탐색기 유용할 수 있습니다.  인기 있는 오픈 소스 블록 탐색기를 사용할 수 있습니다. 다음은 Azure Blockchain Service를 사용 하는 블록 탐색기 목록입니다.

* [Azure Blockchain Service Explorer](https://web3labs.com/azure-offer) Web3 실습
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TP 측정

더 많은 엔터프라이즈 시나리오에서 블록 체인을 사용 하면 두 번째 (TP) 속도 당 트랜잭션이 시스템 비효율성 및 병목 현상을 방지 하는 일을 해야 합니다. 높은 트랜잭션 속도 분산 된 블록 체인 내에서 유지 관리 하기 어려울 수 있습니다. 정확 하 게 TP 측정 서버 스레드, 트랜잭션 큐 크기, 네트워크 대기 시간 및 보안과 같은 다른 요인에 따라 달라질 수 있습니다. 인기 있는 오픈 소스 도구는 개발 중 TPS 속도 측정 하는 경우 [ChainHammer](https://github.com/drandreaskrueger/chainhammer)합니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: Truffle 연결할 때 사용 하는 Azure Blockchain 서비스 네트워크를 사용 하는](connect-truffle.md)
