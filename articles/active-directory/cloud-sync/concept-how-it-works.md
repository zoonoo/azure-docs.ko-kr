---
title: Azure AD Connect 클라우드 동기화 심층 이해-작동 방법
description: 이 항목에서는 클라우드 동기화가 작동 하는 방식에 대 한 심층 정보를 제공 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613842"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>클라우드 동기화 심층 이해-작동 방식

## <a name="overview-of-components"></a>구성 요소 개요

![작동 방식](media/concept-how-it-works/how-1.png)

클라우드 동기화는 Azure AD 서비스를 기반으로 구축 되었으며 다음과 같은 두 가지 주요 구성 요소가 있습니다.

- **프로 비전 에이전트**: Azure AD Connect 클라우드 프로 비전 에이전트는 Workday 인바운드와 동일한 에이전트 이며 앱 프록시와 동일한 서버 쪽 기술을 기반으로 하며 인증을 통과 합니다. 및 아웃 바운드 연결만 필요 하 고 에이전트는 자동으로 업데이트 됩니다. 
- **프로 비전 서비스**: 스케줄러 기반 모델을 사용 하는 아웃 바운드 프로 비전 및 Workday 인바운드 프로 비전과 동일한 프로 비전 서비스입니다. 클라우드 동기화의 경우 변경 내용은 2 분 마다 프로 비전 됩니다.


## <a name="initial-setup"></a>초기 설정
초기 설치 중에 몇 가지 작업을 수행 하면 클라우드 동기화가 수행 됩니다.  해당 경고는 다음과 같습니다. 

- **에이전트를 설치 하는 동안**: 프로 비전 하려는 AD 도메인에 대 한 에이전트를 구성 합니다.  이 구성은 하이브리드 id 서비스에 도메인을 등록 하 고 요청을 수신 대기 하는 service bus에 대 한 아웃 바운드 연결을 설정 합니다.
- **프로 비전을 사용 하는 경우**: AD 도메인을 선택 하 고 2 분 마다 실행 되는 프로 비전을 사용 하도록 설정 합니다. 필요에 따라 암호 해시 동기화를 선택 취소 하 고 알림 전자 메일을 정의할 수 있습니다. Microsoft Graph Api를 사용 하 여 특성 변환을 관리할 수도 있습니다.


## <a name="agent-installation"></a>에이전트 설치
다음은 클라우드 프로 비전 에이전트가 설치 될 때 발생 하는 작업의 연습입니다.

- 먼저 설치 관리자는 가상 서비스 계정 (NETWORK SERVICE\AADProvisioningAgent)에서 실행 되는 에이전트 이진 파일 및 에이전트 서비스를 설치 합니다.  가상 서비스 계정은 암호가 없고 Windows에 의해 관리되는 특수한 유형의 계정입니다.
- 그러면 설치 관리자에서 마법사를 시작 합니다.
- 마법사가 Azure AD 자격 증명을 묻는 메시지를 표시 하 고, 인증 하 고, 토큰을 검색 합니다.
- 그러면 마법사가 현재 컴퓨터 도메인 관리자 자격 증명을 묻는 메시지를 표시 합니다.
- 이러한 자격 증명을 사용 하 여이 도메인에 대 한 에이전트 일반 관리 서비스 계정 (GMSA)을 만들거나 이미 있는 경우 다시 사용 합니다.
- 이제 에이전트 서비스가 GMSA에서 실행 되도록 다시 구성 되었습니다.
- 이제 마법사는 에이전트에서 서비스 하도록 할 각 도메인에 대해 EA (Enterprise Admin)/Domain Admin (DA) 계정과 함께 도메인 구성을 요청 합니다.
- 그런 다음 GMSA 계정이 위에 입력 된 각 도메인에 액세스할 수 있는 권한으로 업데이트 됩니다.
- 그런 다음 마법사에서 에이전트 등록을 트리거합니다.
- 에이전트는 인증서를 만들고 Azure AD 토큰을 사용 하 여 자체 및 인증서를 해당 등록 서비스에 등록 합니다.
- 이 마법사는 AgentResourceGrouping 호출을 트리거합니다. 관리자 서비스에 대 한이 호출은 해당 구성에서 하나 이상의 AD 도메인에 에이전트를 할당 하는 것입니다.
- 이제 마법사가 에이전트 서비스를 다시 시작 합니다.
- 에이전트는 다시 시작 시 및 10 분 마다 부트스트랩 서비스를 호출 하 여 구성 업데이트를 확인 합니다.  부트스트랩 서비스는 에이전트 id의 유효성을 검사 합니다.  또한 마지막 부트스트랩 시간을 업데이트 합니다.  이는 에이전트가 부트스트랩 되지 않는 경우 Service Bus 끝점을 업데이트 하지 않으며 요청을 받지 못할 수 있기 때문에 중요 합니다. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>SCIM(System for Cross-domain Identity Management)이란?

[Scim 사양은](https://tools.ietf.org/html/draft-scim-core-schema-01) Azure AD와 같은 id 도메인 간에 사용자 또는 그룹 id 정보를 교환 하는 것을 자동화 하는 데 사용 되는 표준입니다. 사실상 프로비저닝의 표준으로 자리를 잡아 가고 있는 SCIM은 SAML 또는 OpenID Connect 같은 페더레이션 표준과 함께 사용하면 액세스 관리를 위한 엔드투엔드 표준 기반 솔루션을 관리자에게 제공합니다.

Azure AD Connect 클라우드 프로 비전 에이전트는 Azure AD와 함께 SCIM을 사용 하 여 사용자 및 그룹을 프로 비전 하 고 프로 비전 해제 합니다.

## <a name="synchronization-flow"></a>동기화 흐름
![프로 비전 ](media/concept-how-it-works/provisioning-4.png) 에이전트를 설치 하 고 프로 비전을 사용 하도록 설정한 후에는 다음 흐름이 발생 합니다.

1.  구성 되 면 Azure AD 프로 비전 서비스는 Azure AD 하이브리드 서비스를 호출 하 여 Service bus에 요청을 추가 합니다. 에이전트는 요청을 수신 대기 하는 Service Bus에 대 한 아웃 바운드 연결을 지속적으로 유지 관리 하 고 SCIM (도메인 간 Id 관리) 요청에 대 한 시스템을 즉시 선택 합니다. 
2.  에이전트는 개체 유형을 기반으로 하는 개별 쿼리로 요청을 나눕니다. 
3.  AD는 결과를 에이전트로 반환 하 고 에이전트는이 데이터를 Azure AD로 보내기 전에 필터링 합니다.  
4.  에이전트는 SCIM 응답을 Azure AD에 반환 합니다.  이러한 응답은 에이전트 내에서 발생 한 필터링을 기반으로 합니다.  에이전트는 범위 지정을 사용 하 여 결과를 필터링 합니다. 
5.  프로 비전 서비스는 Azure AD에 변경 내용을 기록 합니다.
6. 전체 동기화와 반대로 델타 동기화 인 경우 쿠키/워터 마크가 사용 됩니다. 새 쿼리는 해당 쿠키/워터 마크부터 변경 내용을 가져옵니다.

## <a name="supported-scenarios"></a>지원 되는 시나리오:
클라우드 동기화에 대해 지원 되는 시나리오는 다음과 같습니다.


- **새 포리스트를 사용 하는 기존 하이브리드 고객**: 동기화 Azure AD Connect 주 포리스트에 사용 됩니다. 클라우드 동기화는 AD 포리스트에서 프로 비전 하는 데 사용 됩니다 (연결 끊김 포함). 자세한 내용은 [여기](tutorial-existing-forest.md)의 자습서를 참조 하세요.

 ![기존 하이브리드](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **새 하이브리드 고객**: Azure AD Connect 동기화가 사용 되지 않습니다. 클라우드 동기화는 AD 포리스트에서 프로 비전 하는 데 사용 됩니다.  자세한 내용은 [여기](tutorial-single-forest.md)의 자습서를 참조 하세요.
 
 ![새 고객](media/tutorial-single-forest/diagram-2.png)

- **기존 하이브리드 고객**: 동기화 Azure AD Connect 주 포리스트에 사용 됩니다. 클라우드 동기화는 기본 포리스트의 소수의 사용자 집합에 대 한 [시험 운영입니다.](tutorial-existing-forest.md)

 ![기존 파일럿](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

자세한 내용은 [지원 되는 토폴로지](plan-cloud-sync-topologies.md)를 참조 하세요.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화 란?](what-is-cloud-sync.md)
