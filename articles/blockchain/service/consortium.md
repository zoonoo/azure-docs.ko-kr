---
title: Azure Blockchain Service 컨소시엄
description: Azure Blockchain Service에서 컨소시엄 블록체인 네트워크를 구현하는 방법에 대한 개요입니다.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84712533"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain Service 컨소시엄

Azure Blockchain Service를 사용하여 각 블록체인 네트워크를 네트워크의 특정 참가자로 제한할 수 있는 프라이빗 컨소시엄 블록체인 네트워크를 만들 수 있습니다. 프라이빗 컨소시엄 블록체인 네트워크의 참가자만 블록체인을 보고 상호 작용할 수 있습니다. Azure Blockchain Service의 컨소시엄 네트워크에는 두 가지 유형의 멤버 참가자 역할이 포함될 수 있습니다.

* **관리자** - 컨소시엄 관리 작업을 수행할 수 있고 블록체인 트랜잭션에 참여할 수 있는 권한 있는 참가자입니다.

* **사용자** -컨소시엄 관리 작업을 수행할 수는 없지만 블록체인 트랜잭션에는 참여할 수 있는 참가자입니다.

컨소시엄 네트워크는 참가자 역할이 혼합되어 있을 수 있으며 각 역할 유형은 임의의 수를 가질 수 있습니다. 적어도 하나 이상의 관리자가 있어야 합니다.

다음 다이어그램에서는 여러 참가자가 있는 컨소시엄 네트워크를 보여 줍니다.

![프라이빗 컨소시엄 네트워크 다이어그램](./media/consortium/network-diagram.png)

Azure Blockchain Service의 컨소시엄 관리를 사용하여 컨소시엄 네트워크에서 참가자를 관리할 수 있습니다. 컨소시엄의 관리는 네트워크의 합의 모델을 기반으로 합니다. 현재 미리 보기 릴리스에서는 Azure Blockchain Service에서 컨소시엄 관리를 위한 중앙 집중식 합의 모델을 제공합니다. 관리자 역할이 있는 모든 권한 있는 참가자는 네트워크에서 참가자를 추가하거나 제거하는 등과 같은 컨소시엄 관리 작업을 수행할 수 있습니다.

## <a name="roles"></a>역할

컨소시엄의 참가자는 개인 또는 조직일 수 있으며 사용자 역할 또는 관리자 역할을 할당할 수 있습니다. 다음 테이블은 두 역할 간의 높은 수준의 차이가 나열되어 있습니다.

| 작업 | 사용자 역할 | 관리자 역할
|--------|:----:|:------------:|
| 새 멤버 만들기 | 예 | 예 |
| 새 멤버 초대 | 예 | 예 |
| 멤버 참가자 역할 설정 또는 변경 | 예 | 예 |
| 멤버 표시 이름 변경 | 멤버 전용 | 멤버 전용 |
| 멤버 제거 | 멤버 전용 | 예 |
| 블록체인 트랜잭션에 참여 | 예 | 예 |

### <a name="user-role"></a>사용자 역할

사용자는 관리자 기능이 없는 컨소시엄 참가자입니다. 컨소시엄과 관련된 멤버 관리에 참여할 수 없습니다. 사용자는 멤버 표시 이름을 변경하고 컨소시엄에서 자신을 제거할 수 있습니다.

### <a name="administrator"></a>관리자

관리자는 컨소시엄 내에서 멤버를 관리할 수 있습니다. 관리자는 컨소시엄 내에서 멤버를 초대하거나 제거하거나 멤버 역할을 업데이트할 수 있습니다.
컨소시엄 내에는 항상 하나 이상의 관리자가 있어야 합니다. 최종 관리자는 컨소시엄에서 나가기 전에 다른 참가자를 관리자 역할로 지정해야 합니다.

## <a name="managing-members"></a>멤버 관리

관리자만 다른 참가자를 컨소시엄에 초대할 수 있습니다. 관리자는 Azure 구독 ID를 사용하여 참가자를 초대합니다.

초대된 참가자는 Azure Blockchain Service에서 새 멤버를 배포하여 블록체인 컨소시엄에 참가할 수 있습니다. 초대된 컨소시엄을 보고 조인하려면 네트워크 관리자가 초대하는 데 사용한 것과 동일한 Azure 구독 ID를 지정해야 합니다.

관리자는 컨소시엄에서 다른 관리자를 포함한 모든 참가자를 제거할 수 있습니다. 멤버는 컨소시엄에서만 자신을 제거할 수 있습니다.

## <a name="consortium-management-smart-contract"></a>컨소시엄 관리 스마트 계약

Azure Blockchain Service의 컨소시엄 관리는 컨소시엄 관리 스마트 계약을 통해 수행됩니다. 스마트 계약은 새 블록체인 멤버를 배포할 때 노드에 자동으로 배포됩니다.

루트 컨소시엄 관리 스마트 계약의 주소는 Azure Portal에서 볼 수 있습니다. **RootContract 주소** 는 블록체인 멤버의 개요 섹션에 있습니다.

![RootContract 주소](./media/consortium/rootcontract-address.png)

컨소시엄 관리 [PowerShell 모듈](manage-consortium-powershell.md)이나 Azure Portal을 사용하거나 Azure Blockchain Service 생성 Ethereum 계정을 사용한 스마트 계약을 통해 직접 컨소시엄 관리 스마트 계약과 상호 작용할 수 있습니다.

## <a name="ethereum-account"></a>Ethereum 계정

멤버가 생성되면 Ethereum 계정 키가 만들어집니다. Azure Blockchain Service는 키를 사용하여 컨소시엄 관리와 관련된 트랜잭션을 만듭니다. Ethereum 계정 키는 Azure Blockchain Service에서 자동으로 관리됩니다.

멤버 계정은 Azure Portal에서 볼 수 있습니다. 멤버 계정은 블록체인 멤버의 개요 섹션에 있습니다.

![멤버 계정](./media/consortium/member-account.png)

멤버 계정을 클릭하고 새 암호를 입력하여 Ethereum 계정을 초기화할 수 있습니다. Ethereum 계정 주소와 암호가 모두 초기화됩니다.  

## <a name="next-steps"></a>다음 단계

컨소시엄 관리 작업은 PowerShell을 통해 액세스할 수 있습니다. 자세한 내용은 [PowerShell을 사용하여 Azure Blockchain Service의 컨소시엄 멤버 관리](manage-consortium-powershell.md)를 참조하세요.
