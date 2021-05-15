---
title: Azure Blockchain Service 제한
description: Azure Blockchain Service의 서비스 및 기능 제한 개요
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80676527"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain Service의 제한 사항

Azure Blockchain Service에는 멤버가 가질 수 있는 노드 수, 컨소시엄 제한 및 스토리지 양과 같은 서비스 및 기능 제한이 있습니다.

## <a name="pricing-tier"></a>가격 책정 계층

트랜잭션 및 유효성 검사기 노드에 대한 최대 한도는 기본 또는 표준 가격 책정 계층에서 Azure Blockchain Service를 프로비저닝하는지 여부에 따라 달라집니다.

| 가격 책정 계층 | 최대 트랜잭션 노드 | 최대 유효성 검사기 노드 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

컨소시엄 네트워크에는 두 개 이상의 Azure Blockchain Service 표준 계층 노드가 있어야 합니다. 표준 계층 노드에는 두 가지 유효성 검사기 노드가 포함됩니다. [이스탄불 비잔틴 내결함성 합의](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)를 충족하려면 4개의 유효성 검사기 노드가 필요합니다.

기본 계층은 개발, 테스트, 개념 증명에 사용됩니다. 프로덕션 등급 배포를 위해 표준 계층을 사용합니다. Blockchain Data Manager를 사용하거나 대량의 프라이빗 트랜잭션을 전송하는 경우 *표준* 계층도 사용해야 합니다.

멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

## <a name="storage-capacity"></a>스토리지 용량

원장 데이터 및 로그에 노드당 사용할 수 있는 최대 스토리지 크기는 1.8테라바이트입니다.

원장 및 로그 스토리지 크기 줄이기는 지원되지 않습니다.
## <a name="consortium-limits"></a>컨소시엄 제한

* Azure Blockchain Service의 다른 컨소시엄 및 멤버 이름과 달리 **컨소시엄 및 멤버 이름은 고유해야 합니다**.

* **멤버 및 컨소시엄 이름은 변경할 수 없습니다.**

* **컨소시엄의 모든 멤버는 동일한 가격 책정 계층에 있어야 합니다.**

* **컨소시엄에 참여하는 모든 멤버는 동일한 지역에 있어야 함**

    컨소시엄에서 만든 첫 번째 멤버는 지역을 결정합니다. 컨소시엄에 초대된 멤버는 첫 번째 멤버와 동일한 지역에 있어야 합니다. 모든 멤버를 동일한 지역으로 제한하면 네트워크 합의에 부정적인 영향을 미치지 않도록 보장하는 데 도움이 됩니다.

* **컨소시엄에는 적어도 하나 이상의 관리자가 있어야 합니다.**

    컨소시엄에 관리자가 한 명만 있는 경우 컨소시엄에 다른 관리자가 추가되거나 승격될 때까지 컨소시엄에서 자신을 삭제하거나 멤버를 삭제할 수 없습니다.

* **컨소시엄에서 제거된 멤버는 다시 추가할 수 없습니다.**

    대신 컨소시엄에 조인하고 새 멤버를 만들기 위해 다시 초대되어야 합니다. 기록 트랜잭션을 보존하기 위해 기존 멤버 리소스는 삭제되지 않습니다.

* **컨소시엄의 모든 멤버는 동일한 원장 버전을 사용해야 합니다.**

    Azure Blockchain Service에서 사용 가능한 패치, 업데이트, 원장 버전에 대한 자세한 내용은 [패치, 업데이트 및 버전](ledger-versions.md)을 참조하세요.

## <a name="performance"></a>성능

각 트랜잭션 제출에 *eth.estimate* 가스 함수를 사용하지 마세요. *eth.estimate* 함수는 메모리를 많이 사용합니다. 함수를 여러 번 호출하면 초당 트랜잭션이 크게 줄어듭니다.

가능하면 트랜잭션 제출에 대해 보수적인 가스 값을 사용하고 *eth.estimate* 사용을 최소화합니다.

## <a name="next-steps"></a>다음 단계

시스템 패치 및 업그레이드 - [패치, 업데이트 및 버전](ledger-versions.md) 관련 정책에 대해 자세히 알아보세요.
