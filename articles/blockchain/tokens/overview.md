---
title: Azure Blockchain Tokens란?
description: Azure Blockchain Tokens는 토큰을 발급하고 관리하는 PaaS(Platform as a Service)입니다.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75387176"
---
# <a name="what-is-azure-blockchain-tokens"></a>Azure Blockchain 토큰이란?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens는 Azure에서 블록체인 원장 간에 표준화된 토큰을 발급하고 관리하는 PaaS(Platform as a Service)입니다.

Azure Blockchain Tokens를 사용하면 미리 작성된 토큰 템플릿을 사용하여 블록체인 솔루션에 대한 표준화된 토큰을 만들 수 있습니다. 또한 서비스를 사용하여 사용자 고유의 토큰 템플릿을 작성할 수도 있습니다. 토큰이 만들어지면 Azure Blockchain Tokens를 사용하여 블록체인에서 토큰을 연결하고 발급합니다. 토큰이 발급되면 여러 블록체인 네트워크에서 해당 토큰을 관리할 수 있습니다.

## <a name="templates"></a>템플릿

Azure Blockchain Tokens를 사용하여 미리 작성된 토큰 템플릿을 선택하거나 고유한 토큰 템플릿을 만듭니다. Azure Blockchain Tokens는 지원되는 동작에 따라 고유한 토큰 템플릿을 만들 수 있는 토큰 템플릿 결합성(composability)을 지원합니다. 토큰 템플릿은 가장 일반적으로 사용되는 토큰에 매핑되므로 대부분의 블록체인 솔루션에 사용할 수 있습니다. 템플릿으로 시작하고, 맞춤 설정하고, 솔루션에 대한 토큰을 배포할 수 있습니다.

Azure Blockchain Tokens 템플릿에 대한 자세한 내용은 [Azure Blockchain Tokens 템플릿](templates.md)을 참조하세요.

## <a name="management"></a>관리

Azure Blockchain Tokens는 기존 블록체인 네트워크에 연결하는 Azure Portal 관리 및 API를 제공합니다. 현재 [Azure Blockchain Service](../service/overview.md) 또는 다른 Ethereum 계열 블록체인에 연결할 수 있습니다.

하나 이상의 블록체인 네트워크에 연결되면 Azure Blockchain Tokens API를 사용하여 블록체인 솔루션에 사용할 토큰을 발급하고 관리할 수 있습니다. API를 사용하여 토큰 관리를 비즈니스 애플리케이션 및 논리에 통합할 수 있습니다. 예를 들어 블록체인에서 토큰을 직접 관리하는 대신 REST API를 사용하여 토큰을 관리할 수 있습니다.

## <a name="blockchains-and-accounts"></a>블록체인 및 계정

Azure Blockchain Tokens는 연결된 블록체인 네트워크에서 새 그룹과 새 블록체인 계정을 만드는 Azure Portal 관리 및 API를 제공합니다. 연결된 네트워크에서 새 계정을 직접 만들 수 있으며, Azure Blockchain Tokens는 사용자를 대신하여 계정 프라이빗 키를 관리합니다. 그룹을 사용하면 여러 네트워크에서 다양한 블록체인 계정을 그룹화하고, 그룹을 통해 액세스 제어를 관리할 수 있습니다.

Azure Blockchain Tokens 계정 관리에 대한 자세한 내용은 [Azure Blockchain Tokens 계정 관리](account-management.md)를 참조하세요.

## <a name="token-taxonomy-framework"></a>토큰 분류 프레임워크

Azure Blockchain Tokens는 TTF(토큰 분류 프레임워크)라는 표준 기반 기본 사항에 따라 작성되었습니다. TTF는 [TTI](https://entethalliance.org/participate/token-taxonomy-initiative/)(토큰 분류 이니셔티브) 토큰 작업 그룹에서 만든 결과물 세트입니다. TTI 작업 그룹은 Ethereum, Quorum, Corda 및 Hyperledger Fabric을 포함한 모든 주요 원장에 적용할 수 있는 토큰과 해당 동작에 대한 비즈니스 분류를 정의합니다. 작업 그룹의 목표는 토큰의 사용을 비즈니스 관점에서 표준화하여 토큰 기반 개발을 간소화하고 민주화하는 프레임워크를 만드는 것입니다. 업계에서 이러한 토큰과 해당 동작을 비즈니스 수준에서 정의할 수 있도록 하여 토큰을 조작하는 비즈니스 논리에서 토큰의 자세한 구현을 추상화합니다.

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Blockchain 뉴스의 경우 [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하여 Azure Blockchain 엔지니어링 팀의 블록체인 서비스 제공 및 정보를 최신 상태로 유지하세요.

Azure Blockchain 토큰 미리 보기에 액세스하려면 [Azure Blockchain 토큰 팀](https://aka.ms/PreviewForm)에 문의하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어에 투표하세요.

## <a name="next-steps"></a>다음 단계

사용 가능한 [Azure Blockchain Tokens 템플릿](templates.md)에 대해 자세히 알아보세요.
