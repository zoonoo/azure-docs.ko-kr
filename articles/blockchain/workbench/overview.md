---
title: Azure Blockchain Workbench 미리 보기 개요
description: Azure Blockchain Workbench 미리 보기 및 관련 기능에 대해 간략하게 설명합니다.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 384fa553204a5a86ad479f2cc623ebb70a9a6f5f
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434141"
---
# <a name="what-is-azure-blockchain-workbench"></a>Azure Blockchain Workbench란?

Azure Blockchain Workbench 미리 보기는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. Azure Blockchain Workbench는 개발자가 비즈니스 논리 및 스마트 계약을 만드는 데 집중할 수 있도록 블록체인 애플리케이션을 빌드하기 위한 인프라 스캐폴딩을 제공합니다. 또한 일반적인 개발 작업을 자동화할 수 있도록 여러 Azure 서비스와 기능을 통합하여 블록체인 애플리케이션을 쉽게 만들 수도 있습니다.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>블록체인 애플리케이션 만들기

Blockchain Workbench를 통해 구성 및 스마트 컨트랙트 코드 작성을 사용하여 블록체인 애플리케이션을 정의할 수 있습니다. 블록체인 애플리케이션 개발에 착수하고, 인프라 및 설정 지원 서비스를 작성하는 대신 계약을 정의하고 비즈니스 로직를 작성하는 데 집중할 수 있습니다.

## <a name="manage-applications-and-users"></a>애플리케이션 및 사용자 관리

Azure Blockchain Workbench는 블록체인 애플리케이션과 사용자를 관리하기 위한 웹 애플리케이션 및 REST API를 제공합니다. Blockchain Workbench 관리자는 애플리케이션 액세스를 관리하고 사용자를 애플리케이션 역할에 할당할 수 있습니다. Azure AD 사용자는 애플리케이션의 멤버에 자동으로 매핑됩니다.

## <a name="integrate-blockchain-with-applications"></a>블록체인과 애플리케이션의 통합

Blockchain Workbench REST API와 메시지 기반 API를 사용하여 기존 시스템과 통합할 수 있습니다. API는 여러 분산 원장 기술, 스토리지 및 데이터베이스 제공물을 대체하거나 사용할 수 있도록 하는 인터페이스를 제공합니다.

Blockchain Workbench는 메시지 기반 API에 보내진 메시지를 변환하여 트랜잭션을 해당 블록체인의 네이티브 API에 필요한 형식으로 빌드할 수 있습니다.  Workbench는 트랜잭션을 적절한 블록체인에 서명하고 라우팅할 수 있습니다. 

Workbench는 이벤트를 Service Bus 및 Event Grid에 자동으로 전달하여 다운스트림 소비자에게 메시지를 보냅니다. 개발자는 이러한 메시징 시스템 중 하나와 통합하여 트랜잭션을 구동하고 결과를 살펴볼 수 있습니다.

## <a name="deploy-a-blockchain-network"></a>블록체인 네트워크 배포

Azure Blockchain Workbench는 Azure Resource Manager 솔루션 템플릿을 사용하여 컨소시엄 블록체인 네트워크 설정을 미리 구성된 솔루션으로 간소화합니다. 이 템플릿은 컨소시엄을 운영하는 데 필요한 모든 구성 요소를 배포하는 간소화된 배포를 제공합니다. Blockchain Workbench는 현재 Ethereum을 지원합니다.

## <a name="use-active-directory"></a>Active Directory 사용

기존 블록체인 프로토콜을 사용하면 블록체인 ID가 네트워크의 주소로 표시됩니다. Azure Blockchain Workbench는 Active Directory ID와 연결하여 블록체인 ID를 추상화하므로, Active Directory ID를 사용하여 엔터프라이즈 애플리케이션을 더 쉽게 빌드할 수 있습니다.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>온 체인 데이터를 오프 체인 스토리지와 동기화

Azure Blockchain Workbench를 사용하면 블록체인의 데이터를 오프 체인 스토리지에 자동으로 동기화하여 블록체인 이벤트와 데이터를 더 쉽게 분석할 수 있습니다. 블록체인에서 데이터를 직접 추출하는 대신 SQL Server와 같은 오프 체인 데이터베이스 시스템을 쿼리할 수 있습니다. 데이터 분석 작업을 수행하는 최종 사용자에게는 블록체인에 대한 전문 지식이 필요하지 않습니다.

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Blockchain 뉴스의 경우 [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하여 Azure Blockchain 엔지니어링 팀의 블록체인 서비스 제공 및 정보를 최신 상태로 유지하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어를 게시하거나 투표하세요.

### <a name="community-support"></a>커뮤니티 지원

Microsoft 엔지니어 및 Azure Blockchain 커뮤니티 전문가와 소통하세요.

* [Azure Blockchain Workbench에 대한 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html)
* [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 아키텍처](architecture.md)
