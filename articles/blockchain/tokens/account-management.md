---
title: Azure Blockchain 토큰 계정 관리
description: Azure Blockchain 토큰 계정 관리를 사용 하 여 블록 체인 작업에 대 한 액세스를 제어 하는 그룹을 만들고 Blockchain 계정을 연결할 수 있습니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326109"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain 토큰 계정 관리

[!INCLUDE [Preview note](./includes/preview.md)]

블록 체인 솔루션의 경우 사용자에 게 Azure Blockchain 토큰 서비스를 사용 하 여 만든 토큰에 대 한 다양 한 수준의 액세스 권한이 필요할 수 있습니다. 대부분의 blockchain 시나리오에서는 원장에 존재 하는 서로 다른 blockchain 계정을 계획 하 고 배포 해야 합니다. 또한 참가자 간 액세스를 관리 해야 합니다.Azure Blockchain 토큰 계정 관리를 사용 하 여 블록 체인 작업에 대 한 액세스를 제어 하는 그룹을 만들고 Blockchain 계정을 연결할 수 있습니다.

## <a name="blockchain-networks"></a>Blockchain 네트워크

Azure Blockchain 토큰은 Blockchain 네트워크 집합에서 토큰을 배포 하 고 관리할 수 있도록 합니다. 단일 blockchain 원장 또는 여러 blockchain 원장를 서비스에 연결할 수 있습니다.

## <a name="accounts"></a>계정

Azure Blockchain 토큰에 연결 된 blockchain 네트워크의 경우 서비스는 계정 개인-공개 키 쌍을 만들고 관리 하며 트랜잭션 서명 및 전송을 수행 합니다. 또한 Azure Blockchain 토큰은 회계 원장의 공개 키 id와 계정을 일치 시키는 id 매핑을 제공 합니다.

## <a name="groups"></a>그룹

그룹을 사용 하면 연결 된 네트워크에서 많은 수의 블록 체인 계정을 관리할 수 있습니다. Azure Blockchain 토큰 Api를 통해 계정을 사용할 수 있는 디렉터리의 응용 프로그램과 사용자를 추적 하 고 감사할 수 있습니다. 예를 들어 여러 줄의 비즈니스 또는 다른 역할을 나타내는 계정 집합을 그룹화 하 고 블록 체인 토큰에 액세스할 수 있습니다.

또한 그룹을 Azure Active Directory 사용자 또는 서비스 주체에 연결할 수 있으며,이 보안 주체는 그룹 및 연결 된 계정에 대 한 사용 권한을 가집니다.  

## <a name="next-steps"></a>다음 단계

사용 가능한 [Azure Blockchain Tokens 템플릿](templates.md)에 대해 자세히 알아보세요.
