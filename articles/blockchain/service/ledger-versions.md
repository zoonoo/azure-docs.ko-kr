---
title: Azure Blockchain 서비스 원장 버전, 패치 및 업그레이드
description: 시스템 패치, 시스템 관리 및 사용자 관리 업그레이드에 대 한 정책을 포함 하 여 Azure Blockchain 서비스에서 지원 되는 원장 버전에 대 한 개요입니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 16d0f0876e7d75dfd0266468ddc3005b86fc632f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579809"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>지원 되는 Azure Blockchain 서비스 원장 버전

Azure Blockchain 서비스는 알려진 참여자 그룹 내의 개인 트랜잭션 처리를 위해 설계 된 Ethereum 기반 [쿼럼](https://www.goquorum.com/developers) 원장을 사용 합니다 .이는 Azure blockchain 서비스에서 컨소시엄로 식별 됩니다.

현재 Azure Blockchain 서비스는 [쿼럼 버전 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) 및 [Tessera transaction manager](https://github.com/jpmorganchase/tessera)를 지원 합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

쿼럼의 버전 관리는 주 버전, 부 버전 및 패치 릴리스를 통해 수행 됩니다. 예를 들어 쿼럼 버전이 2.0.1 인 경우 릴리스 형식은 다음과 같이 분류 됩니다.

|주 | Minor  | 패치  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain 서비스는 쿼럼의 패치 릴리스를 쿼럼에서 사용할 수 있도록 30 일 이내에 기존의 실행 중인 멤버로 자동으로 업데이트 합니다.

## <a name="availability-of-new-ledger-versions"></a>새 원장 버전의 가용성

Azure Blockchain 서비스는 쿼럼 제조업체에서 사용할 수 있는 60 일 이내에 최신 주 버전 및 부 버전의 쿼럼 원장을 제공 합니다. 새 구성원 및 컨소시엄을 프로 비전 할 때 consortia에서 선택할 수 있는 최대 4 개의 부 릴리스가 제공 됩니다. 에서 주 또는 부 릴리스로 업그레이드 하는 것은 현재 지원 되지 않습니다. 예를 들어 버전 2.x를 실행 하는 경우 버전 3.x로 업그레이드 하는 것은 현재 지원 되지 않습니다. 마찬가지로 버전 2.2을 실행 하는 경우에는 현재 버전 2.3로의 업그레이드가 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain 서비스의 제한](limits.md)
