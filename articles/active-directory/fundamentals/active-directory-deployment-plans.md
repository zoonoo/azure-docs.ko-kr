---
title: 배포 계획 - Azure Active Directory | Microsoft Docs
description: 많은 Azure Active Directory 기능을 배포하는 방법에 대한 엔드투엔드 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa7c4865313a48ae5d9041d2dceb6c91787bb6be
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733544"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 배포 계획
Azure AD(Azure Active Directory) 기능을 배포하는 방법에 대한 엔드투엔드 지침을 찾고 계신가요? 다음 배포 계획은 보다 일반적인 Azure AD 기능을 성공적으로 출시하기 위해 필요한 비즈니스 가치, 계획 고려 사항, 디자인 및 운영 절차를 안내합니다. 

이 문서에서는 이메일 템플릿, 시스템 아키텍처 다이어그램, 일반적인 테스트 사례 등을 찾을 수 있습니다. 

문서에 대한 피드백을 주시면 감사하겠습니다. 이 문서가 도움이 되었는지 조사하는 이 짧은 [설문 조사](https://aka.ms/deploymentplanfeedback)에 참여해 주세요. 

## <a name="include-the-right-stakeholders"></a>오른쪽 이해 관계자를 포함 합니다.

배포 하는 새로운 기능에 대 한 계획 수립부터 때 조직 전체에서 주요 이해 관계자를 포함 해야 합니다. 식별 하 고 사용자 또는 사용자에 게 다음 역할 중 각 수행 하 고 프로젝트에 참여를 결정 하기 위해 작업을 문서화 하는 것이 좋습니다.  

역할은 다음과 같습니다. 

|역할 |설명 |
|-|-|
|최종 사용자|대표 사용자 그룹에 기능을 구현할 수 있습니다. 종종 파일럿 프로그램의 변경 내용을 미리 봅니다.
|IT 지원 관리자|IT 지원 조직이 지원 담당자에 게 기술 지원팀의 관점에서이 변경의 지원 가능성에 입력을 제공할 수 있습니다.  
|Identity 설계자 또는 Azure 전역 관리자|관리 팀 담당자를 identity 조직의 핵심 id 관리 인프라를 사용 하 여이 변경 정렬 되는 방법을 정의 해야 합니다.|
|응용 프로그램 비즈니스 소유자 |액세스 관리를 포함할 수 있는 영향을 받는 응용 프로그램의 전반적인 비즈니스 소유자입니다.  사용자 환경 및 최종 사용자의 관점에서이 변경의 유용성에 입력을 제공할 수도 있습니다.
|보안 소유자|계획을 해제 하는 로그인 할 수 있는 보안 팀의 담당자는 조직의 보안 요구 사항을 충족 합니다.|
|준수 관리자|회사를 사용 하 여 규정 준수, 업계 또는 정부 요구 사항 책임이 조직 내의 사람입니다.|

**참여 수준이 포함 될 수 있습니다.**

- **R**esponsible 프로젝트 계획 및 결과 구현 합니다. 

- **** pproval 프로젝트 계획 및 결과 

- **C**ontributor 프로젝트 계획 및 결과 

- **I**nformed 프로젝트 계획 및 결과
 
## <a name="deployment-plans"></a>배포 계획



|시나리오 |설명 |
|-|-|
|[Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. 관리자가 승인한 인증 방법을 사용하는 Azure MFA는 간단한 로그인 프로세스에 대한 요구를 충족하는 동시에 데이터와 애플리케이션에 대한 액세스를 보호합니다.|
|[조건부 액세스](https://aka.ms/deploymentplans/ca)|조건부 액세스를 사용하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사용자를 결정하는 자동 액세스 제어 결정 시스템을 구현할 수 있습니다.|
|[셀프 서비스 암호 재설정](https://aka.ms/SSPRDPDownload)|셀프 서비스 암호 재설정을 사용하면 사용자가 필요할 때 필요한 장소에서 관리자 개입 없이 자신의 암호를 재설정할 수 있습니다.|
|[Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)|Azure AD PIM(Privileged Identity Management)을 사용하면 Azure AD, Azure 리소스 및 기타 Microsoft 온라인 서비스 간에 권한 있는 관리자 역할을 관리할 수 있습니다. PIM은 권한 있는 역할의 악성 활동을 실시간으로 식별, 발견 및 방지할 수 있도록 Just-In-Time 액세스, 요청 승인 워크플로 및 완전히 통합된 액세스 검토와 같은 솔루션을 제공합니다.|
|[Single Sign-On](https://aka.ms/SSODPDownload)|Single Sign-On은 단일 사용자 계정을 사용하여 한 번만 로그인하면 비즈니스 수행에 필요한 모든 앱과 리소스에 액세스할 수 있게 해줍니다. 로그인한 후에는 다시 인증(예: 암호 입력)할 필요 없이 Microsoft Office에서 SalesForce로, Box로 이동할 수 있습니다.|
|[매끄러운 SSO](https://aka.ms/SeamlessSSODPDownload)|Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자를 자동으로 서명합니다. 이 기능을 켜면 사용자가 Azure AD에 로그인하기 위해 암호를 입력할 필요가 없으며, 일반적으로 사용자 이름도 입력할 필요가 있습니다. 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 애플리케이션에 쉽게 액세스할 수 있습니다.|
|[액세스 패널](https://aka.ms/AccessPanelDPDownload)|사용자에게 모든 응용 프로그램을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 새 앱 및 그룹에 대한 액세스를 요청하거나 다른 사용자를 대신하여 이러한 리소스에 대한 액세스를 관리하는 기능과 같은 셀프 서비스 기능을 통해 생산성을 높일 수 있습니다.|
|[암호 해시 동기화에 ADFS 사용](https://aka.ms/deploymentplans/adfs2phs)|암호 해시 동기화를 사용하면 사용자 암호의 해시가 온-프레미스 Active Directory에서 Azure AD로 동기화되므로, 온-프레미스 Active Directory와 상호 작용 없이 Azure AD가 사용자를 인증할 수 있습니다.|
|[통과 인증에 ADFS 사용](https://aka.ms/deploymentplans/adfs2pta)|Azure AD 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 애플리케이션 둘 다에서 동일한 암호로 로그인할 수 있습니다. 이 기능은 하나 적은 기억할 암호로 사용자에게 더 나은 환경을 제공하고 사용자는 로그인하는 방법을 잊을 가능성이 적기 때문에 IT 기술 지원팀 비용을 줄입니다. 사람들이 Azure AD를 사용하여 로그인할 때 이 기능은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다.|
|[Azure AD 애플리케이션 프록시](https://aka.ms/deploymentplans/appproxy)|요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 태블릿, 휴대폰 또는 랩톱을 막론하고 자신의 디바이스에서 일하기를 원합니다. 직원은 해당하는 모든 애플리케이션인 클라우드의 SaaS 앱 및 회사 앱 온-프레미스 모두에도 액세스할 수 있다고 예상합니다. 온-프레미스 애플리케이션에 대한 액세스를 제공하려면 일반적으로 VPN(가상 사설망) 또는 완충 영역(DMZ)이 필요했습니다. 이러한 솔루션은 복잡하고 안전하게 만들기도 어려울 뿐만 아니라 설정과 관리에도 비용이 많이 듭니다. 더 나은 방법이 있습니다! - Azure AD 애플리케이션 프록시|
|[사용자 프로비전](https://aka.ms/UserProvisioningDPDownload)|Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드 애플리케이션(SaaS)에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다.|
|[Workday 기반 인바운드 사용자 프로비저닝](https://aka.ms/WorkdayDeploymentPlan)|Workday 기반 인바운드 사용자를 Active Directory에 프로비전하면 지속적인 ID 거버넌스를 위한 기반을 만들고, 신뢰할 수 있는 ID 데이터를 사용하는 비즈니스 프로세스의 품질을 향상시킬 수 있습니다. 이 기능을 사용하면 연결기-이동기-이탈기(Joiner-Mover-Leaver) 프로세스(예: 새 고용, 해지, 이전)를 IT 프로비전 작업(예: 계정 만들기, 사용, 사용 안 함, 삭제)에 매핑하는 규칙을 구성하여 직원 및 비정규 작업자의 ID 수명 주기를 원활하게 관리할 수 있습니다.|
