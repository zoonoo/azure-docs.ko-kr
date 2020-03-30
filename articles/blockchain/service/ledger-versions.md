---
title: Azure 블록 체인 서비스 원장 버전, 패치, & 업그레이드
description: 시스템 패치 및 시스템 관리 및 사용자 관리 업그레이드에 대한 정책을 포함하여 Azure 블록 체인 서비스에서 지원되는 원장 버전의 개요입니다.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325182"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>지원되는 Azure 블록 체인 서비스 원장 버전

Azure 블록 체인 서비스는 Azure 블록 체인 서비스에서 컨소시엄으로 식별 된 알려진 참가자 그룹 내에서 개인 트랜잭션처리를 위해 설계된 이더리움 기반 [쿼럼](https://www.goquorum.com/developers) 원장을 사용합니다.

현재 Azure 블록 체인 서비스는 [쿼럼 버전 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) 및 [Tessera 트랜잭션 관리자를](https://github.com/jpmorganchase/tessera)지원합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

정원회의 버전 전환은 주요, 사소한 및 패치 릴리스를 통해 수행됩니다. 예를 들어 쿼럼 버전이 2.0.1인 경우 릴리스 유형은 다음과 같이 분류됩니다.

|주요함 | Minor  | 패치  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 블록 체인 서비스는 쿼럼에서 사용할 수 있게 된 후 30일 이내에 기존 실행 중인 멤버에게 쿼럼의 패치 릴리스를 자동으로 업데이트합니다.

## <a name="availability-of-new-ledger-versions"></a>새 원장 버전의 가용성

Azure 블록 체인 서비스는 쿼럼 제조업체에서 사용할 수 있게 된 후 60일 이내에 쿼럼 원장의 최신 주 및 부 버전을 제공합니다. 컨소시엄은 새 멤버와 컨소시엄을 프로비저닝할 때 선택할 수 있는 최대 4개의 마이너 릴리스가 제공됩니다. 현재 주 또는 부 릴리스로 업그레이드할 수 없습니다. 예를 들어 버전 2.x를 실행 중인 경우 버전 3.x로의 업그레이드는 현재 지원되지 않습니다. 마찬가지로 버전 2.2를 실행 중인 경우 버전 2.3으로의 업그레이드는 현재 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure 블록 체인 서비스의 한계](limits.md)
