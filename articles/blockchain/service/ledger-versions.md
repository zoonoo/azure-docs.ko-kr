---
title: Azure Blockchain Service 원장 버전, 패치 및 업그레이드
description: Azure Blockchain Service에서 지원되는 원장 버전에 대한 개요입니다. 시스템 패치 및 업그레이드에 대한 정책을 포함합니다.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807743"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>지원되는 Azure Blockchain Service 원장 버전

Azure Blockchain Service는 Azure Blockchain Service의 컨소시엄으로 식별되는 알려진 참여자들의 그룹 내 비공개 트랜잭션 처리를 위해 설계된 Ethereum 기반 [Quorum](https://www.goquorum.com/developers) 원장을 사용합니다.

현재 Azure Blockchain Service는 [Quorum 버전 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) 및 [Tessera 트랜잭션 관리자](https://github.com/jpmorganchase/tessera)를 지원합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

Quorum의 버전 관리는 주 릴리스, 부 릴리스 및 패치 릴리스를 통해 수행됩니다. 예를 들어 Quorum 버전이 2.0.1인 경우 릴리스 형식은 다음과 같이 분류됩니다.

|주요함 | 부  | 패치  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service는 Quorum의 패치 릴리스를 Quorum에서 출시한 날로부터 30일 이내에 기존의 운영 멤버들에게 자동으로 업데이트합니다.

## <a name="availability-of-new-ledger-versions"></a>새 원장 버전의 가용성

Azure Blockchain Service는 Quorum 원장의 최신 주 버전 및 부 버전을 Quorum 제조업체에서 출시한 날로부터 60일 이내에 제공합니다. 새 멤버 및 컨소시엄을 프로비저닝할 때 컨소시엄들이 선택할 수 있는 최대 4개의 부 릴리스가 제공됩니다. 주 릴리스 또는 부 릴리스로의 업그레이드는 현재 지원되지 않습니다. 예를 들어 버전 2.x를 실행하는 경우 버전 3.x로 업그레이드하는 것은 현재 지원되지 않습니다. 마찬가지로 버전 2.2를 실행하는 경우 버전 2.3으로의 업그레이드는 현재 지원되지 않습니다.

## <a name="how-to-check-quorum-ledger-version"></a>Quorum 원장 버전을 확인하는 방법

geth를 사용하거나 진단 로그를 확인하여 노드에 연결함으로써 Azure Blockchain Service 멤버의 Quorum 버전을 확인할 수 있습니다.

### <a name="using-geth"></a>geth 사용

geth를 사용하여 Azure Blockchain Service 노드에 연결합니다. 예들 들어 `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`입니다.

노드가 연결되면 geth는 다음 출력과 유사한 Quorum 버전을 보고합니다.

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

geth 사용에 대한 자세한 내용은 [빠른 시작: Geth를 사용하여 Azure Blockchain Service 트랜잭션 노드에 연결](connect-geth.md)을 참조하세요.

### <a name="using-diagnostic-logs"></a>진단 로그 사용

진단 로그를 사용하도록 설정하면 트랜잭션 노드에 대하여 Quorum 버전이 보고됩니다. 예를 들어 아래의 노드 정보 로그 메시지에는 Quorum 버전이 포함되어 있습니다.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

진단 로그에 대한 자세한 내용은 [Azure Monitor를 통해 Azure Blockchain Service 모니터링](monitor-azure-blockchain-service.md#diagnostic-settings)을 참조하세요.

## <a name="how-to-check-genesis-file-content"></a>genesis 파일 콘텐츠를 확인하는 방법

블록체인 노드의 genesis 파일 콘텐츠를 확인하려면 다음 Ethereum JavaScript API를 사용할 수 있습니다.

``` bash
admin.nodeInfo.protocols
```
geth 콘솔 또는 web3 라이브러리를 사용하여 API를 호출할 수 있습니다. geth 사용에 대한 자세한 내용은 [빠른 시작: Geth를 사용하여 Azure Blockchain Service 트랜잭션 노드에 연결](connect-geth.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain Service의 제한 사항](limits.md)
