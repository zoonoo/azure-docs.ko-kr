---
title: Azure 블록 체인 토큰 계정 관리
description: Azure Blockchain Tokens 계정 관리를 사용하여 그룹을 만들고 블록 체인 계정을 연결하여 블록 체인 작업에 대한 액세스를 제어 할 수 있습니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326109"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure 블록 체인 토큰 계정 관리

[!INCLUDE [Preview note](./includes/preview.md)]

블록 체인 솔루션의 경우 사용자는 Azure 블록 체인 토큰 서비스로 생성 된 토큰에 대해 서로 다른 수준의 액세스권한을 요구할 수 있습니다. 대부분의 블록 체인 시나리오에서 원장에 있는 다른 블록 체인 계정을 계획하고 배포해야합니다. 또한 참가자 간에 액세스를 관리해야 합니다.Azure Blockchain Tokens 계정 관리를 사용하여 그룹을 만들고 블록 체인 계정을 연결하여 블록 체인 작업에 대한 액세스를 제어 할 수 있습니다.

## <a name="blockchain-networks"></a>블록체인 네트워크

Azure 블록 체인 토큰을 사용하면 블록 체인 네트워크 집합에서 토큰을 배포하고 관리 할 수 있습니다. 단일 블록 체인 원장 또는 여러 블록 체인 원장을 서비스에 연결할 수 있습니다.

## <a name="accounts"></a>계정

Azure Blockchain 토큰에 연결된 블록 체인 네트워크의 경우 서비스는 개인 공개 키 쌍을 만들고 관리하고 트랜잭션 서명 및 제출을 수행합니다. 또한 Azure 블록 체인 토큰은 계정과 원장의 공개 키 ID를 일치시키는 ID 매핑을 제공합니다.

## <a name="groups"></a>그룹

그룹을 사용하면 연결된 네트워크에서 많은 수의 블록 체인 계정을 관리할 수 있습니다. 디렉터리에서 Azure 블록 체인 토큰 API를 통해 계정을 사용할 수있는 응용 프로그램과 사용자를 추적하고 감사 할 수 있습니다. 예를 들어, 서로 다른 비즈니스 라인 또는 다른 역할과 블록 체인 토큰에 대한 액세스를 나타내는 계정 집합을 그룹화 할 수 있습니다.

그룹을 Azure Active Directory 사용자 또는 서비스 주체에 연결할 수도 있으며 이 보안 주체에는 그룹 및 관련 계정에 대한 사용 권한이 있습니다.  

## <a name="next-steps"></a>다음 단계

사용 가능한 [Azure Blockchain Tokens 템플릿](templates.md)에 대해 자세히 알아보세요.
