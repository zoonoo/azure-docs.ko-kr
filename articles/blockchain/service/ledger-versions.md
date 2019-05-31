---
title: Azure Blockchain 서비스 원장 버전, 패치 및 업그레이드를 지원 합니다.
description: 패치 및 시스템 관리 시스템 및 사용자 관리 되는 업그레이드에 대 한 정책을 비롯 한 Azure Blockchain Service에서 지원 되는 원장 버전의 개요입니다.
services: azure-blockchain
keywords: 블록체인
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399095"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>지원 되는 Azure Blockchain Service 원장 버전

Azure Blockchain Service는 Ethereum 기반 [쿼럼](https://www.goquorum.com/developers) 원장 Azure Blockchain Service에서 컨소시엄으로 식별 하는 알려진된 참가자 그룹 내의 개인 트랜잭션 처리를 위해 설계 되었습니다.

현재, Azure Blockchain 서비스는 지원 [쿼럼 버전 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) 하 고 [Tessera 트랜잭션 관리자](https://github.com/jpmorganchase/tessera)합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

쿼럼에 대 한 버전 관리는 주, 부를 통해 수행 됩니다 하 고 패치 해제 합니다. 예를 들어, 쿼럼 버전 2.0.1 이면 릴리스 종류는 다음과 같이 구분할 수 있습니다:

|주 | Minor  | 패치  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain 서비스는 쿼럼에서 사용할 수 있는 30 일 이내 실행 중인 기존 멤버 쿼럼의 패치 릴리스를 자동으로 업데이트 합니다.

## <a name="availability-of-new-ledger-versions"></a>새 원장 버전 가용성

Azure Blockchain 서비스 쿼럼 제조업체에서 제공 되 고 60 일 이내 최근에 주 및 부 버전의 쿼럼 원장을 제공 합니다. Consortia 컨소시엄을 새 멤버를 프로 비전 할 때 선택할 수는 최대 4 부 버전이 제공 됩니다. 업그레이드에서 주 버전 또는 부 릴리스의 현재 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain 서비스의 제한 사항](limits.md)
