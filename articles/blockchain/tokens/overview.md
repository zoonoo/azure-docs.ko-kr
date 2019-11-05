---
title: Azure Blockchain 토큰 이란?
description: Azure Blockchain 토큰은 토큰 발급 및 관리를 위한 PaaS (platform as a service)입니다.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: d04c4202f89e58185465669c3da0f7d5e4d7b9bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518646"
---
# <a name="what-is-azure-blockchain-tokens"></a>Azure Blockchain 토큰 이란?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure 블록 체인 토큰은 Azure의 Blockchain 원장에서 표준화 된 토큰 발급 및 관리를 위한 PaaS (platform as a service)입니다.

Azure Blockchain 토큰을 사용 하 여 미리 작성 된 토큰 템플릿을 사용 하 여 블록 체인 솔루션에 대 한 표준화 된 토큰을 만들 수 있습니다. 서비스를 사용 하 여 고유한 토큰 템플릿을 작성할 수도 있습니다. 만든 후에는 Azure Blockchain 토큰을 사용 하 여 블록 체인에 연결 하 고 토큰을 발급 합니다. 발급 된 후에는 여러 blockchain 네트워크에서 토큰을 관리할 수 있습니다.

## <a name="templates"></a>템플릿

Azure Blockchain 토큰을 사용 하 여 미리 작성 된 토큰 템플릿을 선택 하거나 고유한 토큰 템플릿을 만듭니다. Azure Blockchain 토큰은 지원 되는 동작을 기반으로 사용자 고유의 토큰 템플릿을 만들 수 있는 토큰 템플릿 composability을 지원 합니다. 토큰 템플릿은 가장 일반적으로 사용 되는 토큰에 매핑되므로 대부분의 blockchain 솔루션에 사용할 수 있습니다. 템플릿으로 시작 하 고, 개인 설정 하 고, 솔루션에 대 한 토큰을 배포할 수 있습니다.

Azure Blockchain 토큰 템플릿에 대 한 자세한 내용은 [Azure Blockchain 토큰 템플릿](templates.md)을 참조 하세요.

## <a name="management"></a>관리

Azure Blockchain 토큰은 기존 Blockchain 네트워크에 연결 하는 Azure Portal 관리 및 Api를 제공 합니다. 현재는 [Azure Blockchain 서비스](../service/overview.md) 또는 다른 Ethereum family blockchain에 연결할 수 있습니다.

하나 이상의 blockchain 네트워크에 연결 된 후에는 Azure Blockchain 토큰 Api를 사용 하 여 블록 체인 솔루션에서 사용할 토큰을 발급 하 고 관리할 수 있습니다. Api를 사용 하 여 비즈니스 응용 프로그램 및 논리에서 토큰 관리를 통합할 수 있습니다. 예를 들어 블록 체인에서 직접 토큰을 관리 하는 대신 REST API를 사용 하 여 토큰을 관리할 수 있습니다.

## <a name="blockchains-and-accounts"></a>블록 체인 및 계정

Azure Blockchain 토큰은 연결 된 Blockchain 네트워크에 새 그룹 및 새 Blockchain 계정을 만드는 Azure Portal 관리 및 Api를 제공 합니다. 연결 된 네트워크에서 직접 새 계정을 만들 수 있으며, Azure Blockchain 토큰은 사용자 대신 계정 개인 키를 관리 합니다. 그룹을 사용 하 여 여러 네트워크에서 다양 한 blockchain 계정을 그룹화 하 고 그룹을 통해 액세스 제어를 관리할 수 있습니다.

Azure Blockchain 토큰 계정 관리에 대 한 자세한 내용은 [Azure Blockchain 토큰 계정 관리](account-management.md)를 참조 하세요.

## <a name="token-taxonomy-framework"></a>토큰 분류 프레임 워크

Azure Blockchain 토큰은 .TTF (토큰 분류 프레임 워크) 라는 표준 기반 foundation을 기반으로 빌드됩니다. .TTF는 tti ( [토큰 분류 이니셔티브](https://entethalliance.org/participate/token-taxonomy-initiative/) ) 토큰 작업 그룹에서 만든 결과물 집합입니다. TTI 작업 그룹은 Ethereum, 쿼럼, Corda 및 Hyperledger 패브릭을 비롯 한 모든 주요 원장 적용할 수 있는 토큰 및 동작에 대 한 비즈니스 분류를 정의 합니다. 작업 그룹의 목표는 비즈니스 관점에서 토큰을 사용 하 여 간소화 및 누구나 토큰 기반 개발을 수행 하는 프레임 워크를 만드는 것입니다. 업계에서 이러한 토큰 및 해당 동작을 비즈니스 수준에서 정의 하도록 하면 토큰을 조작 하는 비즈니스 논리에서 토큰의 자세한 구현이 추상화 됩니다.

## <a name="next-steps"></a>다음 단계

사용 가능한 [Azure Blockchain 토큰 템플릿](templates.md)에 대해 자세히 알아보세요.
