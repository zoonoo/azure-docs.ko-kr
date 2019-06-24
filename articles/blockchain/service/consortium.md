---
title: Azure Blockchain 서비스 컨소시엄
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027917"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain 서비스 컨소시엄

Azure Blockchain 서비스를 사용 하 여 만들 수 있습니다 개인 컨소시엄 블록 체인 네트워크는 각 블록 체인 네트워크 네트워크에서 특정 참가자를 제한할 수 있습니다. 개인 컨소시엄 블록 체인 네트워크 참가자만 본 블록 체인을 조작할 수 있습니다. Azure Blockchain Service에서 컨소시엄 네트워크는 두 가지 유형의 멤버 참가자 역할을 포함할 수 있습니다.

* **관리자** -Privileged 참가자 컨소시엄 관리 작업을 수행할 수 있는 블록 체인 트랜잭션에 참여할 수 있습니다.

* **사용자** -참가자 모든 컨소시엄 관리 작업을 수행할 수 없습니다. 하지만 blockchain 트랜잭션에 참여할 수 있습니다.

컨소시엄 네트워크 참가자 역할을 혼합할 수 있으며 임의 개수의 역할 유형별로 수 있습니다. 하나 이상의 관리자 여야 합니다.

다음 다이어그램은 여러 참가자를 사용 하 여 컨소시엄 네트워크를 보여줍니다.

![개인 컨소시엄 네트워크 다이어그램](./media/consortium/network-diagram.png)

Azure Blockchain Service에서 컨소시엄 관리를 사용 하면 컨소시엄 네트워크 참가자를 관리할 수 있습니다. 관리 컨소시엄 네트워크의 합의 모델을 기반으로 합니다. 현재 미리 보기 릴리스에서 Azure Blockchain 서비스 컨소시엄 관리에 대 한 중앙 집중식된 합의 모델을 제공합니다. 관리자 역할이 있는 privileged 참가자가 네트워크에서 참가자를 제거 하거나 추가 작업과 같이 컨소시엄 관리 작업을 걸릴 수 있습니다.

## <a name="roles"></a>역할

컨소시엄 참가자 개인 이나 조직 수 있으며 사용자 역할 또는 관리자 역할을 할당할 수 있습니다. 다음 표에서 높은 수준의 두 역할 간의 차이점을 나열합니다.

| 액션(Action) | 사용자 역할 | 관리자 역할
|--------|:----:|:------------:|
| 새 구성원 만들기 | 예 | 예 |
| 새 멤버 초대 | 아닙니다. | 예 |
| 설정 또는 참가자 역할 구성원 변경 | 아닙니다. | 예 |
| 멤버 표시 이름 변경 | 직접 멤버에 대해서만 | 직접 멤버에 대해서만 |
| 구성원 제거 | 직접 멤버에 대해서만 | 예 |
| 블록 체인 트랜잭션을 참여합니다 | 예 | 예 |

### <a name="user-role"></a>사용자 역할

사용자는 없는 관리자 기능을 사용 하 여 컨소시엄 참가자입니다. 관리와 관련 된 컨소시엄 멤버 참여할 수 없습니다. 사용자는 해당 멤버의 표시 이름을 변경할 수 및 컨소시엄에서 자신을 제거할 수 있습니다.

### <a name="administrator"></a>관리자

관리자는 컨소시엄 내에서 멤버를 관리할 수 있습니다. 관리자로 멤버 초대, 구성원을 제거 하거나 컨소시엄 내에서 멤버 역할을 업데이트할 수 있습니다.
컨소시엄 내에서 하나 이상의 관리자 항상 있어야 합니다. 마지막 관리자 컨소시엄을 벗어나기 전에 관리자 역할로 다른 참가자를 지정 해야 합니다.

## <a name="managing-members"></a>멤버 관리

관리자만 컨소시엄 다른 참가자를 초대할 수 있습니다. 관리자가 해당 Azure 구독 ID를 사용 하 여 참가자 초대

초대를 받은 후 참가자 Azure Blockchain Service에 새 멤버를 배포 하 여 blockchain 컨소시엄을 조인할 수 있습니다. 확인 하 고는 초대 된 컨소시엄 조인 네트워크 관리자가 초대에 사용 된 동일한 Azure 구독 ID를 지정 해야 합니다.

관리자는 다른 관리자를 포함 하 여 컨소시엄을에서 모든 참가자를 제거할 수 있습니다. 멤버는 컨소시엄에서 자신을 제거할만 있습니다.

## <a name="consortium-management-smart-contract"></a>컨소시엄 관리에 대 한 스마트 계약

Azure Blockchain Service에서 컨소시엄 관리 컨소시엄 관리에 대 한 스마트 계약을 통해 수행 됩니다. 스마트 계약 새 blockchain 멤버를 배포 하는 경우 노드를 자동으로 배포 됩니다.

Azure portal에서 루트 컨소시엄 관리 스마트 계약의 주소를 볼 수 있습니다. 합니다 **RootContract 주소** blockchain 멤버의 개요 섹션에 있습니다.

![RootContract 주소](./media/consortium/rootcontract-address.png)

컨소시엄 관리 스마트 계약 컨소시엄 관리를 사용 하 여 상호 작용할 수 있습니다 [PowerShell 모듈](manage-consortium-powershell.md), Azure 포털 또는 사용 하 여 스마트 계약을 통해 직접 Azure Blockchain Service Ethereum을 생성 계정입니다.

## <a name="ethereum-account"></a>Ethereum 계정

멤버를 만들면 Ethereum 계정 키가 생성 됩니다. Azure Blockchain 서비스 키를 사용 하 여 컨소시엄 관리와 관련 된 트랜잭션을 만듭니다. Ethereum 계정 키를 Azure Blockchain 서비스에서 자동으로 관리 됩니다.

회원 계정 Azure portal에서 볼 수 있습니다. 구성원 계정이 blockchain 멤버의 개요 섹션입니다.

![구성원 계정](./media/consortium/member-account.png)

Ethereum 계정의 멤버 계정에 클릭 하 고 새 암호를 입력 하 여 재설정할 수 있습니다. Ethereum 계정 주소와 암호를 재설정 됩니다.  

## <a name="next-steps"></a>다음 단계

[PowerShell을 사용 하 여 Azure Blockchain 서비스에서 멤버를 관리 하는 방법](manage-consortium-powershell.md)
