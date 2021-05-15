---
title: Azure AD Connect 클라우드 동기화 심층 분석 - 작동 방법
description: 이 항목에서는 클라우드 동기화가 작동하는 방식에 대한 심층 정보를 제공합니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613842"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>클라우드 동기화 심층 분석 - 작동 방식

## <a name="overview-of-components"></a>구성 요소 개요

![작동 방법](media/concept-how-it-works/how-1.png)

클라우드 동기화는 Azure AD 서비스를 기반으로 하며 다음과 같은 두 가지 주요 구성 요소를 포함합니다.

- **프로비전 에이전트**: Azure AD Connect 클라우드 프로비저닝 에이전트는 Workday 인바운드와 동일한 에이전트이며 앱 프록시와 동일한 서버 쪽 기술과 통과 인증을 기반으로 합니다. 아웃바운드 연결만 필요하고 에이전트는 자동으로 업데이트됩니다. 
- **프로비전 서비스**: 아웃바운드 프로비전 및 스케줄러 기반 모델을 사용하는 Workday 인바운드 프로비전과 동일한 프로비전 서비스입니다. 클라우드 동기화의 경우 변경 내용은 2 분마다 프로비전됩니다.


## <a name="initial-setup"></a>초기 설정
초기 설치 진행 중에 몇 가지 작업을 통해 클라우드 동기화가 수행됩니다.  이러한 항목은 다음과 같습니다. 

- **에이전트 설치 중**: 프로비전닝 출발지인 AD 도메인에 대한 에이전트를 구성합니다.  이 구성은 하이브리드 ID 서비스에 도메인을 등록하고 요청을 수신 대기하는 Service Bus에 대한 아웃바운드 연결을 설정합니다.
- **프로비전을 사용하는 경우**: AD 도메인을 선택하고 2 분마다 실행하는 프로비전을 사용하도록 설정합니다. 선택에 따라 암호 해시 동기화를 선택 취소하고 알림 이메일을 정의할 수 있습니다. Microsoft Graph API를 사용하여 특성 변환을 관리할 수도 있습니다.


## <a name="agent-installation"></a>에이전트 설치
다음은 클라우드 프로비저닝 에이전트 설치에 관한 상세 설명입니다.

- 먼저, 설치 프로그램이 에이전트 이진 데이터와 가상 서비스 계정(NETWORK SERVICE\AADProvisioningAgent)에서 실행되는 에이전트 서비스를 설치합니다.  가상 서비스 계정은 암호가 없고 Windows에 의해 관리되는 특수한 유형의 계정입니다.
- 설치 프로그램이 마법사를 시작합니다.
- 마법사가 Azure AD 자격 증명을 묻는 메시지를 표시한 뒤 인증을 실시하고 토큰을 검색합니다.
- 그런 다음 마법사가 현재 컴퓨터 도메인 관리자 자격 증명을 요구합니다.
- 자격 증명을 사용하여 도메인에 대한 에이전트 일반 관리되는 서비스 계정(GMSA)을 만들거나 이미 있는 경우에는 위치를 찾아내 다시 사용합니다.
- 이제 에이전트 서비스가 GMSA에서 실행되도록 다시 구성되었습니다.
- 이제 마법사는 에이전트가 제공할 각 도메인의 EA(Enterprise Admin)/DA(Domain Admin) 계정과 도메인 구성을 요청합니다.
- 그런 다음 GMSA 계정이 위에 입력된 각 도메인에 액세스할 수 있는 권한으로 업데이트됩니다.
- 그 다음, 마법사에서 에이전트 등록을 트리거합니다.
- 에이전트는 인증서를 만들고, Azure AD 토큰을 사용하여 HIS(Hybrid Identity Service) 등록 서비스를 통해 자체 등록하고 인증서를 등록합니다.
- 마법사가 AgentResourceGrouping 호출을 트리거합니다. HIS 관리자 서비스에 대한 이 호출로 HIS 구성에서 하나 이상의 AD 도메인에 에이전트를 할당합니다.
- 마법사가 에이전트 서비스를 다시 시작합니다.
- 에이전트는 다시 시작 시(이후 10 분마다) 부트스트랩 서비스를 호출하여 구성 업데이트를 확인합니다.  부트스트랩 서비스는 에이전트 ID의 유효성을 검사합니다.  또한 마지막 부트스트랩 시간을 업데이트합니다.  이는 에이전트가 부트스트랩을 실행하지 않을 경우 업데이트된 Service Bus 엔드포인트를 받지 못하고 요청을 수신하지 못할 수 있기 때문에 중요합니다. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>SCIM(System for Cross-domain Identity Management)이란?

[SCIM 사양은](https://tools.ietf.org/html/draft-scim-core-schema-01) Azure AD 같은 ID 도메인들 간 사용자 또는 그룹 ID 정보의 교환을 자동화하는 데 사용되는 표준입니다. 사실상 프로비저닝의 표준으로 자리를 잡아 가고 있는 SCIM은 SAML 또는 OpenID Connect 같은 페더레이션 표준과 함께 사용하면 액세스 관리를 위한 엔드투엔드 표준 기반 솔루션을 관리자에게 제공합니다.

Azure AD Connect 클라우드 프로비저닝 에이전트는 Azure AD와 함께 SCIM을 사용하여 사용자 및 그룹을 프로비전하고 프로비전 해제합니다.

## <a name="synchronization-flow"></a>동기화 흐름
![프로비전](media/concept-how-it-works/provisioning-4.png) 에이전트를 설치하고 프로비전을 사용하도록 설정한 후에는 다음 흐름이 발생합니다.

1.  구성이 완료되면 Azure AD 프로비저닝 서비스는 Azure AD 하이브리드 서비스를 호출하여 Service Bus에 요청을 추가합니다. 에이전트는 요청을 수신 대기하는 Service Bus에 대한 아웃바운드 연결을 유지하고 SCIM(System for Cross-domain Identity Management) 요청을 즉시 선택합니다. 
2.  에이전트는 선택한 요청을 개체 형식을 기반으로 개별 쿼리로 구분합니다. 
3.  AD는 결과를 에이전트로 반환하고 에이전트는 이 데이터를 Azure AD로 보내기 전에 필터링합니다.  
4.  에이전트는 SCIM 응답을 Azure AD에 반환합니다.  이러한 응답은 에이전트 내에서 발생한 필터링을 기반으로 합니다.  에이전트는 범위 지정을 사용하여 결과를 필터링합니다. 
5.  프로비저닝 서비스는 Azure AD에 변경 내용을 기록합니다.
6. 전체 동기화와 반대로 델타 동기화인 경우 쿠키/워터마크가 사용됩니다. 이후 새 쿼리는 계속해서 해당 쿠키/워터마크로부터 변경 내용을 수신하게 됩니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
클라우드 동기화에 지원되는 시나리오는 다음과 같습니다.


- **새 포리스트를 사용하는 기존 하이브리드 고객**: Azure AD Connect 동기화가 기본 포리스트에 사용됩니다. 클라우드 동기화는 AD 포리스트에서 프로비전하는 데 사용됩니다(연결 끊김 포함). 자세한 내용은 자습서([여기](tutorial-existing-forest.md))를 참조하십시오.

 ![기존 하이브리드](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **새 하이브리드 고객**:      Azure AD Connect 동기화는 사용되지 않습니다. 클라우드 동기화는 AD 포리스트에서 프로비전하는 데 사용됩니다.  자세한 내용은 자습서([여기](tutorial-single-forest.md))를 참조하십시오.
 
 ![신규 고객](media/tutorial-single-forest/diagram-2.png)

- **기존 하이브리드 고객**: Azure AD Connect 동기화가 기본 포리스트에 사용됩니다. 클라우드 동기화는 기본 포리스트([여기](tutorial-existing-forest.md))의 소수의 사용자 집합에 시험적으로 적용됩니다.

 ![기존 파일럿](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

자세한 내용은 [지원되는 토폴로지](plan-cloud-sync-topologies.md)를 참조하세요.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
