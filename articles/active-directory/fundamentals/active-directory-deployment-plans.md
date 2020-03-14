---
title: 배포 계획 - Azure Active Directory | Microsoft Docs
description: 많은 Azure Active Directory 기능을 배포하는 방법에 대한 엔드투엔드 지침입니다.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262218"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 배포 계획
Azure Active Directory (Azure AD) 기능 배포에 대 한 종단 간 지침을 찾으십니까? Azure AD 배포 계획은 일반적인 Azure AD 기능을 성공적으로 배포 하는 데 필요한 비즈니스 가치, 계획 고려 사항 및 운영 절차를 안내 합니다.

계획 페이지에서 브라우저의 PDF 인쇄 기능을 사용 하 여 문서의 최신 오프 라인 버전을 만듭니다.
## <a name="include-the-right-stakeholders"></a>올바른 관련자 포함

새 기능에 대 한 배포 계획을 시작할 때 조직 전반에 주요 관련자를 포함 하는 것이 중요 합니다. 다음의 각 역할을 수행 하는 사람 또는 사람을 식별 하 고 문서화 하는 것이 좋습니다. 이러한 역할을 사용 하 여 프로젝트의 참여를 확인 하는 것이 좋습니다.  

역할에는 다음이 포함 될 수 있습니다. 

|역할 |Description |
|-|-|
|최종 사용자|기능이 구현 될 사용자의 담당자 그룹입니다. 자주 파일럿 프로그램의 변경 내용을 미리 봅니다.
|IT 지원 관리자|기술 지원팀 관점에서이 변경의 지원 가능성에 대 한 입력을 제공할 수 있는 조직 담당자를 지원 합니다.  
|Id 설계자 또는 Azure 전역 관리자|Id 관리 팀 담당자는 조직의 핵심 id 관리 인프라에 이러한 변경을 적용 하는 방법을 정의 합니다.|
|응용 프로그램 비즈니스 소유자 |영향을 받는 응용 프로그램의 전반적인 비즈니스 소유자입니다. 여기에는 액세스 관리 작업이 포함 될 수 있습니다.  최종 사용자의 관점에서 이러한 변경의 사용자 환경 및 유용성에 대 한 입력을 제공할 수도 있습니다.
|보안 소유자|보안 팀에서 조직의 보안 요구 사항을 충족 하는 계획을 받을 수 있는 담당자입니다.|
|준수 관리자|조직 내 사용자는 회사, 산업 또는 정부 요구 사항을 준수 하는지 확인 해야 합니다.|

**참여 수준에는 다음이 포함 될 수 있습니다.**

- 프로젝트 계획 및 결과 구현에 대 한 **R**esponsible 

- 프로젝트 계획 및 결과의 pproval 

- 프로젝트 계획 및 결과에 대 한 **C**ontributor 

- 프로젝트 계획 및 결과의 형식입니다.


## <a name="best-practices-for-a-pilot"></a>파일럿에 대 한 모범 사례
파일럿을 통해 모든 사용자에 대 한 기능을 설정 하기 전에 작은 그룹으로 테스트할 수 있습니다. 테스트의 일부로 조직 내의 각 사용 사례가 철저 하 게 테스트 되었는지 확인 합니다. 조직 전체에 배포 하기 전에 특정 파일럿 사용자 그룹을 대상으로 지정 하는 것이 가장 좋습니다.

첫 번째 wave에서 테스트 하 고 피드백을 제공할 수 있는 IT, 유용성 및 기타 적절 한 사용자를 대상으로 합니다. 이 피드백은 사용자에 게 보내는 통신 및 지침을 추가로 개발 하 고 지원 담당자가 볼 수 있는 문제 유형에 대 한 정보를 제공 하는 데 사용 됩니다. 

더 큰 사용자 그룹에 대 한 롤아웃 확대는 대상 그룹의 범위를 늘려 수행 해야 합니다. [동적 그룹 멤버 자격](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)을 통해 또는 대상 그룹에 사용자를 수동으로 추가 하 여이 작업을 수행할 수 있습니다.


## <a name="deploy-authentication"></a>인증 배포

| 기능 | Description|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. 관리자가 승인한 인증 방법을 사용 하 여 Azure MFA는 간단한 로그인 프로세스에 대 한 수요를 충족 하면서 데이터와 응용 프로그램에 대 한 액세스를 보호 합니다. |
| [조건부 액세스](https://aka.ms/deploymentplans/ca)| 조건부 액세스를 사용 하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사용자에 대 한 자동화 된 액세스 제어 결정을 구현할 수 있습니다. |
| [셀프 서비스 암호 재설정](https://aka.ms/deploymentplans/sspr)| 셀프 서비스 암호 재설정 기능을 사용 하면 사용자가 관리자 개입 없이 암호를 다시 설정할 수 있습니다 (필요한 경우). |
| [암호 없는](https://aka.ms/deploymentplans/passwordless) | 조직에서 Microsoft Authenticator 앱 또는 FIDO2 보안 키를 사용 하 여 암호 없는 인증 구현 |

## <a name="deploy-application-management"></a>응용 프로그램 관리 배포

| 기능 | Description|
| -| - |
| [Single Sign-On](https://aka.ms/deploymentplans/sso)| Single sign-on을 사용 하면 사용자가 한 번만 로그인 하면 비즈니스를 수행 하는 데 필요한 앱과 리소스에 액세스할 수 있습니다. 로그인 한 후에는 두 번째로 자격 증명을 입력 하지 않고도 Microsoft Office에서 SalesForce to Box로 이동 하 여 내부 응용 프로그램에 연결할 수 있습니다. |
| [액세스 패널](https://aka.ms/deploymentplans/accesspanel)| 사용자에게 모든 애플리케이션을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 앱 및 그룹에 대 한 액세스를 요청 하거나 다른 사용자를 대신 하 여 리소스에 대 한 액세스를 관리 하는 등의 셀프 서비스 기능을 사용 하 여 생산성을 높일 수 있습니다. |


## <a name="deploy-hybrid-scenarios"></a>하이브리드 시나리오 배포

| 기능 | Description|
| -| -|
| [암호 해시 동기화에 ADFS 사용](https://aka.ms/deploymentplans/adfs2phs)| 암호 해시 동기화를 사용 하 여 사용자 암호의 해시는 온-프레미스 Active Directory에서 Azure AD로 동기화 되므로 Azure AD에서 온-프레미스와 상호 작용 하지 않고 사용자를 인증할 수 있습니다 Active Directory |
| [통과 인증에 ADFS 사용](https://aka.ms/deploymentplans/adfs2pta)| Azure AD 통과 인증을 사용 하면 사용자가 동일한 암호를 사용 하 여 온-프레미스 및 클라우드 기반 응용 프로그램에 로그인 할 수 있습니다. 이 기능을 사용 하면 사용자에 게 더 나은 환경을 제공할 수 있으며, 사용자가 로그인 하는 방법을 잊은 경우를 줄일 수 있기 때문에 IT 기술 지원팀 비용을 줄일 수 있습니다. 사람들이 Azure AD를 사용하여 로그인할 때 이 기능은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다. |
| [Azure AD 애플리케이션 프록시](https://aka.ms/deploymentplans/appproxy)| 요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 클라우드 및 회사 앱 온-프레미스에서 SaaS 앱에 액세스 해야 합니다. Azure AD 응용 프로그램 프록시는 비용이 많이 들고 복잡 한 Vpn (가상 사설망) 또는 완충 영역 (Dmz) 없이 이러한 강력한 액세스를 가능 하 게 합니다. |
| [매끄러운 SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자를 자동으로 서명합니다. 이 기능을 사용 하면 사용자가 Azure AD에 로그인 하기 위해 암호를 입력할 필요가 없으며 일반적으로 사용자 이름을 입력할 필요가 없습니다. 이 기능을 사용 하면 추가 온-프레미스 구성 요소가 없어도 권한 있는 사용자가 클라우드 기반 응용 프로그램에 쉽게 액세스할 수 있습니다. |

## <a name="deploy-user-provisioning"></a>사용자 프로 비전 배포

| 기능 | Description|
| -| -|
| [사용자 프로비전](https://aka.ms/deploymentplans/userprovisioning)| Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드 애플리케이션(SaaS)에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. |
| [클라우드 HR 사용자 프로 비전](https://aka.ms/deploymentplans/cloudhr)| Active Directory에 대 한 클라우드 HR 사용자 프로 비전은 지속적인 id 거 버 넌 스를 위한 토대를 만들고 권한 있는 id 데이터를 사용 하는 비즈니스 프로세스의 품질을 향상 시킵니다. Workday 또는 Successfactors와 같은 클라우드 HR 제품에서이 기능을 사용 하는 경우, Leaver 프로세스 (예: 새 고용, 종료, 새 고용)를 매핑하는 규칙을 구성 하 여 직원과 불확정 작업자의 id 수명 주기를 원활 하 게 관리할 수 있습니다. 전송)으로 IT 프로 비전 작업 (예: 만들기, 사용, 사용 안 함) |

## <a name="deploy-governance-and-reporting"></a>거 버 넌 스 및 보고 배포

| 기능 | Description|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD PIM(Privileged Identity Management)을 사용하면 Azure AD, Azure 리소스 및 기타 Microsoft 온라인 서비스 간에 권한 있는 관리자 역할을 관리할 수 있습니다. PIM은 권한 있는 역할의 악성 활동을 실시간으로 식별, 발견 및 방지할 수 있도록 Just-In-Time 액세스, 요청 승인 워크플로 및 완전히 통합된 액세스 검토와 같은 솔루션을 제공합니다. |
| [보고 및 모니터링](https://aka.ms/deploymentplans/reporting)| Azure AD 보고 및 모니터링 솔루션의 디자인은 법적 요구 사항, 보안 및 운영 요구 사항 뿐만 아니라 기존 환경 및 프로세스에 따라 달라 집니다. 이 문서에서는 다양 한 디자인 옵션을 제공 하 고 올바른 배포 전략에 대 한 지침을 제공 합니다. |
