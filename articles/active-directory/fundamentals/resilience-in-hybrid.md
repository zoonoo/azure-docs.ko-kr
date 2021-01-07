---
title: Azure Active Directory에서 더 많은 탄력적 하이브리드 인증 빌드
description: 탄력적 하이브리드 인프라를 구축 하는 데 필요한 설계자 및 IT 관리자를 위한 가이드입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c45b362bc37df71346fc3b635c8ae4a51f62cdc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919919"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>하이브리드 아키텍처의 빌드 복원 력

하이브리드 인증을 사용 하면 사용자가 온-프레미스에서 마스터 된 id로 클라우드 기반 리소스에 액세스할 수 있습니다. 하이브리드 인프라에는 클라우드 및 온-프레미스 구성 요소가 모두 포함 되어 있습니다.

* 클라우드 구성 요소에는 Azure AD, Azure 리소스 및 서비스, 조직의 클라우드 기반 앱 및 SaaS 응용 프로그램이 포함 됩니다.

* 온-프레미스 구성 요소에는 온-프레미스 응용 프로그램, SQL 데이터베이스와 같은 리소스, Windows Server Active Directory와 같은 id 공급자가 포함 됩니다. 

> [!IMPORTANT]
> 하이브리드 인프라의 복원 력을 계획할 때 종속성 및 단일 실패 지점이 최소화 되는 것이 중요 합니다. 

Microsoft는 하이브리드 인증을 위한 세 가지 메커니즘을 제공 합니다. 이러한 옵션은 복원 력 순서로 나열 됩니다. 가능 하면 암호 해시 동기화를 구현 하는 것이 좋습니다.

* Phs ( [암호 해시 동기화](../hybrid/whatis-phs.md) )는 Azure AD Connect을 사용 하 여 암호 해시의 id와 해시를 Azure AD에 동기화 하 여 사용자가 온-프레미스로 마스터 된 암호를 사용 하 여 클라우드 기반 리소스에 로그인 할 수 있도록 합니다. PHS에는 인증에만 사용할 수 있는 온-프레미스 종속성이 있습니다.

* Pta ( [통과 인증](../hybrid/how-to-connect-pta.md) )는 로그인을 위해 사용자를 Azure AD로 리디렉션합니다. 그러면 사용자 이름 및 암호는 회사 네트워크에 배포 된 에이전트를 통해 온-프레미스 Active Directory에 대해 유효성이 검사 됩니다. PTA에는 온-프레미스 서버에 상주 하는 Azure AD PTA 에이전트의 온-프레미스 공간이 있습니다.

* [페더레이션](../hybrid/whatis-fed.md) 고객은 AD FS와 같은 페더레이션 서비스를 배포한 다음, Azure AD는 페더레이션 서비스에 의해 생성 되는 SAML 어설션의 유효성을 검사 합니다. 페더레이션은 온-프레미스 인프라에서 가장 높은 종속성을 가지 므로 더 많은 오류 지점이 있습니다. 

   
조직에서 이러한 방법 중 하나 이상을 사용할 수 있습니다. 자세한 내용은 [Azure AD 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../hybrid/choose-ad-authn.md)을 참조하세요. 이 문서에는 방법론을 결정 하는 데 도움이 되는 의사 결정 트리가 포함 되어 있습니다.

## <a name="password-hash-synchronization"></a>암호 해시 동기화

Azure AD에 대 한 가장 간단 하 고 탄력적 인 하이브리드 인증 옵션은 인증 요청을 처리할 때 온-프레미스 id 인프라 종속성이 없는 [암호 해시 동기화](../hybrid/whatis-phs.md) 입니다. 암호 해시가 포함 된 id가 Azure AD와 동기화 되 면 사용자는 온-프레미스 id 구성 요소에 대 한 종속성 없이 클라우드 리소스에 인증할 수 있습니다. 

![PHS의 아키텍처 다이어그램](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

이 인증 옵션을 선택 하면 온-프레미스 id 구성 요소를 사용할 수 없게 될 때 중단이 발생 하지 않습니다. 하드웨어 오류, 정전, 자연 재해 및 맬웨어 공격을 비롯 한 다양 한 이유로 온-프레미스 중단이 발생할 수 있습니다. 

### <a name="how-do-i-implement-phs"></a>PHS를 구현할 어떻게 할까요? 있나요?

PHS를 구현 하려면 다음 리소스를 참조 하세요.

* [Azure AD Connect를 사용 하 여 암호 해시 동기화 구현](../hybrid/how-to-connect-password-hash-synchronization.md)

* [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md)

요구 사항으로 PHS를 사용할 수 없는 경우 통과 인증을 사용 합니다.

## <a name="pass-through-authentication"></a>통과 인증

통과 인증은 서버에서 온-프레미스에 상주 하는 인증 에이전트에 종속 됩니다. 영구 연결 또는 service bus는 Azure AD와 온-프레미스 PTA 에이전트 사이에 존재 합니다. 방화벽, 인증 에이전트를 호스트 하는 서버 및 온-프레미스 Windows Server Active Directory (또는 다른 id 공급자)는 모두 잠재적인 오류 점수입니다. 

![PTA의 아키텍처 다이어그램](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>PTA를 구현할 어떻게 할까요? 있나요?

통과 인증을 구현 하려면 다음 리소스를 참조 하세요.

* [통과 인증 작동 방식](../hybrid/how-to-connect-pta-how-it-works.md)

* [통과 인증 보안 심층 분석](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Azure AD 통과 인증 설치](../hybrid/how-to-connect-pta-quick-start.md)

* PTA를 사용 하는 경우 [항상 사용 가능한 토폴로지](../hybrid/how-to-connect-pta-quick-start.md)를 정의 합니다.

 ## <a name="federation"></a>페더레이션

페더레이션에는 끝점의 교환, 토큰 서명 인증서 및 기타 메타 데이터를 포함 하는 페더레이션 서비스와 Azure AD 간의 트러스트 관계가 생성 됩니다. 요청이 Azure AD에 들어오면 구성을 읽고 구성 된 끝점으로 사용자를 리디렉션합니다. 이 시점에서 사용자는 Azure AD에서 유효성을 검사 하는 SAML 어설션을 발급 하는 페더레이션 서비스와 상호 작용 합니다. 

다음 다이어그램에서는 여러 온-프레미스 데이터 센터에 걸쳐 중복 페더레이션 및 웹 응용 프로그램 프록시 서버를 포함 하는 엔터프라이즈 Active Directory Federation Services (AD FS)의 토폴로지를 보여 줍니다. 이 구성은 DNS와 같은 엔터프라이즈 네트워킹 인프라 구성 요소, 지리적 선호도 기능을 사용 하는 네트워크 부하 분산, 방화벽 등을 사용 합니다. 모든 온-프레미스 구성 요소 및 연결에는 오류가 발생 하기 쉽습니다. 자세한 내용은 [AD FS 용량 계획 설명서](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) 를 참조 하세요.

> [!NOTE]
>  페더레이션에는 가장 많은 수의 온-프레미스 종속성이 있으므로 오류가 발생할 가능성이 가장 높습니다. 이 다이어그램은 AD FS 표시 되는 반면, 다른 온-프레미스 id 공급자는 고가용성, 확장성 및 장애 조치 (failover)를 수행 하기 위해 유사한 디자인 고려 사항을 적용 합니다.

![페더레이션 아키텍처 다이어그램](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>페더레이션을 구현할 어떻게 할까요? 있나요?

페더레이션된 인증 전략을 구현 하거나 더 많은 복원 력을 만들려면 다음 리소스를 참조 하세요.

* [페더레이션된 인증 이란?](../hybrid/whatis-fed.md)

* [페더레이션 작동 방식](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD 페더레이션 호환성 목록](../hybrid/how-to-connect-fed-compatibility.md)

* [AD FS 용량 계획 문서](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) 를 따르세요.

* [Azure IaaS에서 AD FS 배포](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* 페더레이션과 함께 [PHS 사용](../hybrid/tutorial-phs-backup.md)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [자격 증명 관리를 사용 하 여 복원 력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용 하 여 복원 력 빌드](resilience-with-device-states.md)

* [CAE (연속 액세스 평가)를 사용 하 여 복원 력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원 력 빌드](resilience-b2b-authentication.md)

* [응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드](resilience-on-premises-access.md)

개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)
