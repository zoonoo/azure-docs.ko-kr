---
title: Azure Blockchain 제한
description: Azure Blockchain 서비스의 서비스 및 기능 제한 개요
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 20c26db9453220270d17801b74d904384c74cb36
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577213"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain 서비스의 제한

Azure Blockchain 서비스에는 구성원에 게 포함할 수 있는 노드 수, consortium 제한 및 저장소 양과 같은 서비스 및 기능 제한이 있습니다.

## <a name="pricing-tier"></a>가격 책정 계층

트랜잭션 및 유효성 검사기 노드에 대 한 최대 제한은 기본 또는 표준 가격 책정 계층에서 Azure Blockchain 서비스를 프로 비전 하는지 여부에 따라 달라 집니다.

| 가격 책정 계층 | 최대 트랜잭션 노드 | 최대 유효성 검사기 노드 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

멤버를 만든 후에 기본 및 표준 간에 가격 책정 계층을 변경 하는 것은 지원 되지 않습니다.

## <a name="storage-capacity"></a>Storage 용량

원장 데이터 및 로그에 노드당 사용할 수 있는 최대 저장소 크기는 1.8입니다.

원장 및 로그 저장소 크기를 줄이는 것은 지원 되지 않습니다.

## <a name="consortium-limits"></a>컨소시엄 제한

* **컨소시엄 및 멤버 이름은** Azure Blockchain 서비스의 다른 consortium 및 구성원 이름에서 고유 해야 합니다.

* **구성원 및 consortium 이름은 변경할 수 없습니다.**

* **컨소시엄의 모든 멤버는 동일한 가격 책정 계층에 있어야 합니다.**

* **컨소시엄에 참여 하는 모든 멤버는 동일한 지역에 있어야 합니다.**

    컨소시엄에서 만든 첫 번째 멤버는 지역을 결정 합니다. 컨소시엄에 초대 된 구성원이 첫 번째 멤버와 동일한 지역에 있어야 합니다. 모든 구성원을 동일한 지역으로 제한 하면 네트워크 합의에 부정적인 영향을 주지 않습니다.

* **컨소시엄에 관리자가 한 명 이상 있어야 합니다.**

    컨소시엄에 관리자가 한 명 뿐 이면 컨소시엄에서 자체 관리자를 제거 하거나, 다른 관리자가 컨소시엄에서 추가 되거나 승격 될 때까지 해당 구성원을 삭제할 수 없습니다.

* **컨소시엄에서 제거 된 멤버는 다시 추가할 수 없습니다.**

    대신, 컨소시엄에 조인 하 고 새 멤버를 만들기 위해 다시 초대 해야 합니다. 기존 멤버 리소스는 삭제 되지 않으므로 기록 트랜잭션을 유지 합니다.

* **컨소시엄의 모든 멤버는 동일한 원장 버전을 사용 해야 합니다.**

    Azure Blockchain 서비스에서 사용할 수 있는 패치, 업데이트 및 원장 버전에 대 한 자세한 내용은 [패치, 업데이트 및 버전](ledger-versions.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [패치, 업데이트 및 버전](ledger-versions.md)
