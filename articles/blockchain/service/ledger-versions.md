---
title: Azure Blockchain 서비스 원장 버전, 패치, & 업그레이드
description: Azure Blockchain 서비스에서 지원 되는 원장 버전에 대 한 개요입니다. 시스템 패치 및 업그레이드에 대 한 정책을 포함 합니다.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807743"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>지원 되는 Azure Blockchain 서비스 원장 버전

Azure Blockchain 서비스는 알려진 참여자 그룹 내의 개인 트랜잭션 처리를 위해 설계 된 Ethereum 기반 [쿼럼](https://www.goquorum.com/developers) 원장을 사용 합니다 .이는 Azure blockchain 서비스에서 컨소시엄로 식별 됩니다.

현재 Azure Blockchain 서비스는 [쿼럼 버전 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) 및 [Tessera transaction manager](https://github.com/jpmorganchase/tessera)를 지원 합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

쿼럼의 버전 관리는 주 버전, 부 버전 및 패치 릴리스를 통해 수행 됩니다. 예를 들어 쿼럼 버전이 2.0.1 인 경우 릴리스 형식은 다음과 같이 분류 됩니다.

|주요함 | 부  | 패치  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain 서비스는 쿼럼의 패치 릴리스를 쿼럼에서 사용할 수 있도록 30 일 이내에 기존의 실행 중인 멤버로 자동으로 업데이트 합니다.

## <a name="availability-of-new-ledger-versions"></a>새 원장 버전의 가용성

Azure Blockchain 서비스는 쿼럼 제조업체에서 사용할 수 있는 60 일 이내에 최신 주 버전 및 부 버전의 쿼럼 원장을 제공 합니다. 새 구성원 및 컨소시엄을 프로 비전 할 때 consortia에서 선택할 수 있는 최대 4 개의 부 릴리스가 제공 됩니다. 에서 주 또는 부 릴리스로 업그레이드 하는 것은 현재 지원 되지 않습니다. 예를 들어 버전 2.x를 실행 하는 경우 버전 3.x로 업그레이드 하는 것은 현재 지원 되지 않습니다. 마찬가지로 버전 2.2을 실행 하는 경우에는 현재 버전 2.3로의 업그레이드가 지원 되지 않습니다.

## <a name="how-to-check-quorum-ledger-version"></a>쿼럼 원장 버전을 확인 하는 방법

Geth 또는 진단 로그 보기를 사용 하 여 노드에 연결 하 여 Azure Blockchain 서비스 구성원의 쿼럼 버전을 확인할 수 있습니다.

### <a name="using-geth"></a>Geth 사용

Geth를 사용 하 여 Azure Blockchain 서비스 노드에 연결 합니다. 예: `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

노드가 연결 되 면 geth는 다음 출력과 유사한 쿼럼 버전을 보고 합니다.

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Geth 사용에 대 한 자세한 내용은 [빠른 시작: Geth를 사용 하 여 Azure Blockchain 서비스 트랜잭션 노드에 연결](connect-geth.md)을 참조 하세요.

### <a name="using-diagnostic-logs"></a>진단 로그 사용

진단 로그를 사용 하도록 설정 하면 트랜잭션 노드에 대 한 쿼럼 버전이 보고 됩니다. 예를 들어 다음 노드 정보 로그 메시지에는 쿼럼 버전이 포함 되어 있습니다.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

진단 로그에 대 한 자세한 내용은 [Azure Monitor를 통해 Azure Blockchain 서비스 모니터링](monitor-azure-blockchain-service.md#diagnostic-settings)을 참조 하세요.

## <a name="how-to-check-genesis-file-content"></a>Genesis 파일 콘텐츠를 확인 하는 방법

Blockchain 노드의 genesis 파일 콘텐츠를 확인 하려면 다음 Ethereum JavaScript API를 사용할 수 있습니다.

``` bash
admin.nodeInfo.protocols
```
Geth 콘솔 또는 web3 라이브러리를 사용 하 여 API를 호출할 수 있습니다. Geth 사용에 대 한 자세한 내용은 [빠른 시작: Geth를 사용 하 여 Azure Blockchain 서비스 트랜잭션 노드에 연결](connect-geth.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain 서비스의 제한](limits.md)
