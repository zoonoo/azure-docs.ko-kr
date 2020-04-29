---
title: Azure Blockchain 서비스 컨소시엄
description: Azure Blockchain 서비스에서 consortium Blockchain 네트워크를 구현 하는 방법에 대 한 개요입니다.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247619"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain 서비스 컨소시엄

Azure Blockchain 서비스를 사용 하 여 각 Blockchain 네트워크를 네트워크의 특정 참가자로 제한할 수 있는 개인 컨소시엄 Blockchain 네트워크를 만들 수 있습니다. 개인 컨소시엄 blockchain 네트워크의 참가자만 blockchain을 보고 상호 작용할 수 있습니다. Azure Blockchain 서비스의 컨소시엄 네트워크에는 두 가지 유형의 멤버 참여자 역할이 포함 될 수 있습니다.

* 컨소시엄 관리 작업을 수행할 수 있고 blockchain 트랜잭션에 참여할 수 있는 **관리자** 권한 참여자입니다.

* **사용자** -컨소시엄 관리 작업을 수행할 수 없지만 blockchain 트랜잭션에 참여할 수 있는 사용자 참가자입니다.

컨소시엄 네트워크는 참가자 역할을 혼합할 수 있으며 각 역할 유형의 임의 수를 가질 수 있습니다. 관리자가 한 명 이상 있어야 합니다.

다음 다이어그램에서는 여러 참가자가 있는 컨소시엄 네트워크를 보여 줍니다.

![개인 consortium 네트워크 다이어그램](./media/consortium/network-diagram.png)

Azure Blockchain 서비스의 컨소시엄 관리를 사용 하 여 consortium 네트워크에서 참가자를 관리할 수 있습니다. 컨소시엄의 관리는 네트워크의 합의 모델을 기반으로 합니다. 현재 미리 보기 릴리스에서는 Azure Blockchain 서비스가 consortium 관리를 위한 중앙 집중식 합의 모델을 제공 합니다. 관리자 역할이 있는 모든 권한 있는 참가자는 네트워크에서 참가자를 추가 하거나 제거 하는 등 컨소시엄 관리 작업을 수행할 수 있습니다.

## <a name="roles"></a>역할

컨소시엄의 참여자는 개인 또는 조직 일 수 있으며 사용자 역할 또는 관리자 역할을 할당할 수 있습니다. 다음 표에서는 두 역할 간의 높은 수준의 차이점을 보여 줍니다.

| 작업 | 사용자 역할 | 관리자 역할
|--------|:----:|:------------:|
| 새 멤버 만들기 | 예 | 예 |
| 새 구성원 초대 | 아니요 | 예 |
| 구성원 참가자 역할 설정 또는 변경 | 아니요 | 예 |
| 멤버 표시 이름 변경 | 멤버 전용 | 멤버 전용 |
| 구성원 제거 | 멤버 전용 | 예 |
| 블록 체인 트랜잭션에 참여 | 예 | 예 |

### <a name="user-role"></a>사용자 역할

사용자는 관리자 기능이 없는 컨소시엄 참가자입니다. 컨소시엄과 관련 된 멤버 관리에 참여할 수 없습니다. 사용자는 구성원 표시 이름을 변경 하 고 컨소시엄에서 자신을 제거할 수 있습니다.

### <a name="administrator"></a>관리자

관리자는 컨소시엄 내에서 구성원을 관리할 수 있습니다. 관리자는 컨소시엄 내에서 구성원을 초대 하거나, 멤버를 제거 하거나, 구성원 역할을 업데이트할 수 있습니다.
컨소시엄 내에는 항상 하나 이상의 관리자가 있어야 합니다. 최종 관리자는 컨소시엄을 나가기 전에 다른 참가자를 관리자 역할로 지정 해야 합니다.

## <a name="managing-members"></a>멤버 관리

관리자만 다른 참가자를 컨소시엄에 초대할 수 있습니다. 관리자는 Azure 구독 ID를 사용 하 여 참가자를 초대 합니다.

초대 된 참가자는 Azure Blockchain 서비스에서 새 구성원을 배포 하 여 블록 체인 컨소시엄에 가입할 수 있습니다. 초대 된 컨소시엄을 보고 조인 하려면 네트워크 관리자가 초대 하는 데 사용 된 것과 동일한 Azure 구독 ID를 지정 해야 합니다.

관리자는 다른 관리자를 포함 하 여 컨소시엄에서 모든 참가자를 제거할 수 있습니다. 구성원은 컨소시엄 에서만 제거할 수 있습니다.

## <a name="consortium-management-smart-contract"></a>컨소시엄 관리 스마트 계약

Azure Blockchain 서비스의 컨소시엄 관리는 consortium 관리 스마트 계약을 통해 수행 됩니다. 스마트 계약은 새 blockchain 멤버를 배포할 때 노드에 자동으로 배포 됩니다.

루트 consortium 관리 스마트 계약의 주소는 Azure Portal에서 볼 수 있습니다. **Rootcontract 주소** 는 blockchain 멤버의 개요 섹션에 있습니다.

![RootContract 주소](./media/consortium/rootcontract-address.png)

컨소시엄 관리 [PowerShell 모듈](manage-consortium-powershell.md)을 사용 하 여, Azure Portal을 사용 하거나, Azure Blockchain 서비스 생성 Ethereum 계정을 사용 하 여 스마트 계약을 통해 직접 consortium 관리 스마트 계약과 상호 작용할 수 있습니다.

## <a name="ethereum-account"></a>Ethereum 계정

구성원이 생성 되 면 Ethereum 계정 키가 생성 됩니다. Azure Blockchain 서비스는 키를 사용 하 여 컨소시엄 관리와 관련 된 트랜잭션을 만듭니다. Ethereum 계정 키는 Azure Blockchain 서비스에서 자동으로 관리 됩니다.

멤버 계정은 Azure Portal에서 볼 수 있습니다. 구성원 계정은 blockchain 멤버의 개요 섹션에 있습니다.

![구성원 계정](./media/consortium/member-account.png)

구성원 계정을 클릭 하 고 새 암호를 입력 하 여 Ethereum 계정을 다시 설정할 수 있습니다. Ethereum 계정 주소와 암호가 모두 다시 설정 됩니다.  

## <a name="next-steps"></a>다음 단계

컨소시엄 관리 작업은 PowerShell을 통해 액세스할 수 있습니다. 자세한 내용은 [PowerShell을 사용 하 여 Azure 블록 체인 서비스에서 consortium 구성원 관리](manage-consortium-powershell.md)를 참조 하세요.
