---
title: Azure Blockchain 서비스 제한
description: Azure Blockchain 서비스의 서비스 및 기능 제한 개요
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80676527"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain 서비스의 제한

Azure Blockchain 서비스에는 구성원에 게 포함할 수 있는 노드 수, consortium 제한 및 저장소 양과 같은 서비스 및 기능 제한이 있습니다.

## <a name="pricing-tier"></a>가격 책정 계층

트랜잭션 및 유효성 검사기 노드에 대 한 최대 제한은 기본 또는 표준 가격 책정 계층에서 Azure Blockchain 서비스를 프로 비전 하는지 여부에 따라 달라 집니다.

| 가격 책정 계층 | 최대 트랜잭션 노드 | 최대 유효성 검사기 노드 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| 표준 | 10 | 2 |

컨소시엄 네트워크에는 두 개 이상의 Azure Blockchain 서비스 표준 계층 노드가 있어야 합니다. 표준 계층 노드에는 두 가지 유효성 검사기 노드가 포함 됩니다. [이스탄불 비잔틴 내결함성 합의](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)를 충족 하려면 4 개의 유효성 검사기 노드가 필요 합니다.

기본 계층은 개발, 테스트 및 개념 증명에 사용 됩니다. 프로덕션 등급 배포에 표준 계층을 사용 합니다. Blockchain Data Manager를 사용하거나 대량의 프라이빗 트랜잭션을 전송하는 경우 *표준* 계층도 사용해야 합니다.

멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

## <a name="storage-capacity"></a>스토리지 용량

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

## <a name="performance"></a>성능

각 트랜잭션 제출에 *eth* 를 사용 하지 마십시오. *Eth* 함수는 메모리를 많이 사용 합니다. 함수를 여러 번 호출 하면 초당 트랜잭션이 크게 줄어듭니다.

가능 하면 트랜잭션 제출에 대해 보수적인 가스 값을 사용 하 고 *eth*의 사용을 최소화 합니다.

## <a name="next-steps"></a>다음 단계

시스템 패치 및 업그레이드, [패치, 업데이트 및 버전](ledger-versions.md)에 대 한 정책에 대해 자세히 알아보세요.
