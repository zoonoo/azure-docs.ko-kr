---
title: Azure Blockchain Service 개요
description: Azure Blockchain Service 개요
services: azure-blockchain
keywords: 블록체인
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9fcf75a07d1caf7b411c315d11ce984cbe35df75
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577192"
---
# <a name="what-is-azure-blockchain-service"></a>Azure Blockchain Service란?

Azure Blockchain Service는 사용자가 Azure에서 대규모로 Blockchain 네트워크를 확장하고 작동할 수 있도록 하는 완전 관리형 원장 서비스입니다. Azure Blockchain Service는 blockchain 네트워크 거버넌스 뿐만 아니라 인프라 관리를 통합적으로 제어하여 다음 기능을 제공합니다.

* 간단한 네트워크 배포 및 작업
* 기본 제공 컨소시엄 관리
* 친숙한 개발 도구를 사용하여 스마트 계약 개발

Azure Blockchain Service는 여러 원장 프로토콜을 지원하도록 고안되었습니다. 현재는 [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) 합의 메커니즘을 사용하여 Ethereum [Quorum](https://www.jpmorgan.com/Quorum) 원장을 지원합니다.

이러한 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 가상 머신과 인프라를 관리하는 데 시간과 리소스를 할당하는 대신, 앱 개발 및 비즈니스 논리에 집중할 수 있습니다. 또한 새로운 기술을 습득하지 않고도 원하는 오픈 소스 도구와 플랫폼을 사용하여 솔루션을 전달할 수 있는 애플리케이션을 계속 개발할 수 있습니다.

## <a name="network-deployment-and-operations"></a>네트워크 배포 및 작업

Azure Blockchain Service 배포는 Azure Blockchain 확장을 사용하여 Visual Studio Code에서뿐만 아니라 Azure Portal, Azure CLI를 통해서도 수행할 수 있습니다.  트랜잭션 및 유효성 검사기 노드의 프로비저닝, 서비스 관리형 스토리지 뿐만 아니라 보안 격리를 위한 Azure Virtual Network를 비롯한 배포가 간소화됩니다.  또한 새 블록체인 멤버를 배포하는 경우 사용자는 컨소시엄을 만들거나 참가하기도 합니다.  컨소시엄에서는 다른 Azure 구독에 속하는 여러 당사자가 공유 블록체인에서 서로 안전하게 통신할 수 있게 됩니다.  이 간소화된 배포는 블록체인 네트워크 배포를 며칠에서 몇 분으로 단축합니다.

### <a name="performance-and-service-tiers"></a>성능 및 서비스 계층

Azure Blockchain Service는 두 가지 서비스 계층인 *기본* 및 *표준*을 제공합니다. 각 계층은 경량 개발 및 테스트 워크로드에서 대규모로 확장된 프로덕션 블록체인 배포까지 지원할 수 있는 다양한 성능 및 기능을 제공합니다. 두 계층 모두 하나 이상의 트랜잭션 노드와 하나의 유효성 검사기 노드(기본) 또는 두 개의 유효성 검사기 노드(표준)를 포함합니다.

![가격 책정 계층](./media/overview/pricing-tiers.png)

두 개의 유효성 검사기 노드를 제공하는 것 외에도 *표준* 계층은 각 트랜잭션 및 유효성 검사기 노드에 대해 2개의 *vCore*를 제공하지만, 기본 계층은 1개의 vCore 구성을 제공합니다.  트랜잭션 및 유효성 검사기 노드에 대해 2개의 vCore를 제공하므로, 1개 vCore는 Quorum 원장에 전용될 수 있고, 나머지 1개 vCore는 다른 인프라 관련 서비스에 사용할 수 있으므로 프로덕션 블록체인 워크로드의 성능을 최적화할 수 있습니다. 가격 책정 정보에 대한 자세한 내용은 [Azure Blockchain Service 가격 책정](https://azure.microsoft.com/pricing/details/blockchain-service)을 참조하세요.

### <a name="security-and-maintenance"></a>보안 및 유지 관리

첫 번째 블록체인 멤버를 프로비저닝한 후에는 멤버에 트랜잭션 노드를 더 추가할 수 있습니다.  기본적으로 트랜잭션 노드는 방화벽 규칙을 통해 보호되므로 액세스할 수 있게 구성해야 해야 합니다.  또한 모든 트랜잭션 노드는 TLS를 통해 동작 중인 데이터를 암호화합니다.  방화벽 규칙, 기본 인증, 액세스 키 및 Azure Active Directory 통합을 비롯하여 트랜잭션 노드 액세스 보안을 유지하기 위한 여러 옵션이 있습니다. 자세한 내용은 [트랜잭션 노드 구성](configure-transaction-nodes.md) 및 [Azure Active Directory 액세스 구성](configure-aad.md)을 참조하세요.

관리 서비스인 Azure Blockchain Service는 고가용성을 위해 구성된 최신 호스트 운영 체제 및 원장 소프트웨어 스택 업데이트(표준 계층에만 해당)로 블록체인 멤버의 노드를 패치하여 기존 IaaS 블록체인 노드에 필요한 많은 DevOps를 줄여줍니다.  패치 및 업데이트에 대한 자세한 내용은 [지원되는 Azure Blockchain Service 원장 버전](ledger-versions.md)을 참조하세요.

### <a name="monitoring-and-logging"></a>모니터링 및 로깅

또한 Azure Blockchain Service는 Azure Monitor 서비스를 통해 풍부한 메트릭을 제공하여 노드 CPU, 메모리 및 스토리지 사용에 대한 인사이트와 트랜잭션 및 마이닝 블록, 트랜잭션 큐 깊이, 활성 연결과 같은 블록체인 네트워크 활동에 대한 유용한 인사이트를 얻을 수 있도록 합니다.  블록체인 애플리케이션에 중요한 인사이트를 볼 수 있도록 메트릭을 사용자 지정할 수 있습니다.  또한 경고에 임계값을 정의하여 사용자가 메일 또는 문자 메시지를 보내거나, 논리 앱, Azure 함수를 실행하거나, 사용자 지정된 웹후크로 전송하는 것과 같은 작업을 트리거할 수 있도록 합니다.

![메트릭](./media/overview/metrics.png)

Azure Log Analytics를 통해 사용자는 Quorum 원장과 관련된 로그 또는 트랜잭션 노드에 대해 시도된 연결과 같은 기타 중요한 정보를 볼 수 있습니다.

## <a name="built-in-consortium-management"></a>기본 제공 컨소시엄 관리

첫 번째 블록체인 멤버를 배포할 때 새 컨소시엄에 참가하거나 컨소시엄을 만듭니다.  컨소시엄은 다중 당사자 프로세스에 따라 거래를 수행하는 블록체인 멤버 간의 거버넌스 및 연결을 관리하는 데 사용되는 논리 그룹입니다.  Azure Blockchain Service는 컨소시엄의 작업 멤버가 수행할 수 있는 작업을 결정하는 미리 정의된 스마트 계약을 통해 기본 제공 거버넌스 컨트롤을 제공합니다.  이러한 거버넌스 컨트롤은 컨소시엄 관리자가 필요에 따라 사용자 지정할 수 있습니다. 새 컨소시엄을 만들 때 블록체인 멤버는 컨소시엄의 기본 관리자이므로, 다른 당사자를 컨소시엄에 참가하도록 초대할 수 있습니다.  이전에 초대된 경우에만 컨소시엄에 참가할 수 있습니다.  컨소시엄에 참가할 때 컨소시엄의 관리자가 수행하는 거버넌스 컨트롤이 블록체인 멤버에 적용됩니다.

![컨소시엄 관리](./media/overview/consortium.png)

컨소시엄에서 멤버 추가 및 제거와 같은 컨소시엄 관리 작업은 PowerShell 및 REST API를 통해 액세스할 수 있습니다. 확정된 스마트 계약을 수정 및 제출하는 대신, 공통 인터페이스를 사용하여 컨소시엄를 프로그래밍 방식으로 관리할 수 있습니다. 자세한 내용은 [컨소시엄 관리](consortium.md)를 참조하세요.

## <a name="develop-using-familiar-development-tools"></a>친숙한 개발 도구를 사용하여 개발

오픈 소스 Quorum Ethereum 원장을 기준으로, 기존 Ethereum 애플리케이션에 대해 수행하는 것과 동일한 방식으로 Azure Blockchain Service용 애플리케이션을 개발할 수 있습니다. 선두적인 업계 파트너와 협력하면서 Azure Blockchain Development Kit Visual Studio Code 확장을 사용하여 개발자들은 Truffle Suite와 같은 친숙한 도구를 통해 스마트 계약을 빌드할 수 있습니다. 개발자는 Azure Blockchain Development Kit 확장을 사용하여 하나의 IDE에서 스마트 계약을 빌드 및 배포할 수 있도록 컨소시엄을 만들거나 기존 컨소시엄에 연결할 수 있습니다. 사용자는 Azure Blockchain Visual Studio Code 확장을 사용하여 하나의 IDE에서 스마트 계약을 빌드 및 배포할 수 있도록 컨소시엄을 만들거나 기존 컨소시엄에 연결할 수 있습니다. 자세한 내용은 [VS Code Marketplace의 Azure Blockchain Development Kit](https://aka.ms/vscodebcextension) 및 [Azure Blockchain Development Kit 사용자 가이드](https://aka.ms/vscodebcextensionwiki )를 참조하세요.

## <a name="support-and-feedback"></a>지원 및 피드백

도움이 필요하거나 피드백이 필요한가요?

* [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하고, [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain) 및 [Azure Blockchain 포럼](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)을 참조하세요.
* 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계

시작하려면 빠른 시작을 시도하거나 이러한 리소스에서 자세한 정보를 확인하세요.
* [Azure Portal을 사용하여 블록체인 멤버를 만들거나 ](create-member.md) [Azure CLI를 사용하여 블록체인 멤버를 만듭니다](create-member-cli.md).
* 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/blockchain-service)를 참조하세요.
* [Azure Blockchain 개발 키트](https://github.com/Azure-Samples/blockchain-devkit)를 사용하여 첫 번째 앱을 빌드합니다.
* Azure Blockchain VSCode 확장 [사용자 가이드](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
