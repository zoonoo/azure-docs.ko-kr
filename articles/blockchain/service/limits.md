---
title: Azure Blockchain 제한
description: 서비스 및 기능 제한 사항에 Azure Blockchain Service 개요
services: azure-blockchain
keywords: 블록체인
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028172"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain 서비스의 제한 사항

Azure Blockchain 서비스에 서비스 및 노드 수 있습니다, 컨소시엄 제한 사항 및 저장소 크기의 수와 같은 기능 제한 합니다.

## <a name="pricing-tier"></a>가격 책정 계층

트랜잭션 및 유효성 검사기 노드의의 최대 제한 기본 또는 표준 가격 책정 계층에서 Azure Blockchain 서비스를 프로 비전 하는지 여부에 따라 달라 집니다.

| 가격 책정 계층 | 최대 트랜잭션 노드 | 최대 유효성 검사기 노드 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

멤버 만들기 지원 되지 않습니다 후 기본 및 표준 간의 가격 책정 계층을 변경 합니다.

## <a name="storage-capacity"></a>저장소 용량

원장 데이터 및 로그에 대 한 노드당 사용할 수 있는 저장소의 최대 크기는 1tb입니다.

원장 및 로그 저장소 크기를 줄이는 것은 지원 되지 않습니다.

## <a name="consortium-limits"></a>컨소시엄 제한

* **컨소시엄 및 멤버 이름은 고유 해야** Azure Blockchain 서비스에서 다른 컨소시엄 및 멤버 이름에서.

* **멤버 이름과 컨소시엄을 변경할 수 없습니다.**

* **컨소시엄의 모든 멤버는 동일한 가격 책정 계층에 있어야 합니다.**

* **컨소시엄에 참여 하는 모든 멤버는 동일한 지역에 있어야 합니다.**

    협회에서 만든 첫 번째 멤버 영역을 나타냅니다. 초대 된 구성원 컨소시엄을 첫 번째 멤버와 동일한 지역에 있어야 합니다. 동일한 지역에 모든 멤버를 제한 네트워크 합의 부정적인 영향을 받지 않도록 하는 데 도움이 됩니다.

* **컨소시엄 관리자를 한 명 이상 있어야 합니다.**

    컨소시엄에 하나의 관리자만 있으면 컨소시엄에서 자체를 제거 하거나 다른 관리자가 추가 되거나 컨소시엄에서 승격 될 때까지 해당 멤버를 삭제할 수는 없습니다.

* **컨소시엄에서 제거 된 구성원을 다시 추가할 수 없습니다.**

    대신, 컨소시엄을 조인 하 여 새 멤버를 만들려면 다시 초대할 수 해야 있습니다. 기존 멤버 리소스 기록 트랜잭션의 유지 하기 위해 삭제 되지 않습니다.

* **모든 멤버 컨소시엄에 동일한 원장 버전을 사용 해야**

    패치, 업데이트 및 Azure Blockchain Service에서 사용할 수 있는 원장 버전에 대 한 자세한 내용은 참조 하세요. [패치, 업데이트 및 버전](ledger-versions.md)합니다.

## <a name="next-steps"></a>다음 단계

* [패치, 업데이트 및 버전](ledger-versions.md)
