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
ms.openlocfilehash: d7a596454a48a1d6fcee77634363dd38f34a4d58
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603359"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 배포 계획
Azure Active Directory(Azure AD) 기능 배포에 대한 종단 간 지침을 찾고 계십니까? Azure AD 배포 계획은 일반적인 Azure AD 기능을 성공적으로 배포하는 데 필요한 비즈니스 가치, 계획 고려 사항 및 운영 절차를 안내합니다.

모든 계획 페이지에서 브라우저의 인쇄에서 PDF로 의 기능을 사용하여 최신 오프라인 버전의 설명서를 만듭니다.
## <a name="include-the-right-stakeholders"></a>올바른 이해 관계자 포함

새 기능에 대한 배포 계획을 시작할 때는 조직 전체의 주요 이해 관계자를 포함하는 것이 중요합니다. 다음 각 역할을 수행하는 사람 또는 사람을 식별하고 문서화하고 그들과 협력하여 프로젝트에 참여하는 것을 결정하는 것이 좋습니다.  

역할에는 다음이 포함될 수 있습니다. 

|역할 |Description |
|-|-|
|최종 사용자|기능이 구현될 대표 사용자 그룹입니다. 파일럿 프로그램의 변경 내용을 미리 보기하는 경우가 많습니다.
|IT 지원 관리자|헬프 데스크 관점에서 이러한 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 담당자입니다.  
|ID 설계자 또는 Azure 글로벌 관리자|이러한 변경이 조직의 핵심 ID 관리 인프라와 어떻게 일치하는지 정의하는 ID 관리 팀 담당자입니다.|
|응용 프로그램 비즈니스 소유자 |액세스 관리를 포함할 수 있는 영향을 받는 응용 프로그램의 전체 비즈니스 소유자입니다.또한 최종 사용자의 관점에서 이 변경의 사용자 환경 및 유용성에 대한 입력을 제공할 수도 있습니다.
|보안 소유자|계획이 조직의 보안 요구 사항을 충족한다고 사인오프할 수 있는 보안 팀의 담당자입니다.|
|준수 관리자|기업, 산업 또는 정부 요구 사항을 준수할 책임이 있는 조직 내 사용자입니다.|

**참여 수준에는 다음이 포함될 수 있습니다.**

- 프로젝트 계획 및 결과 구현을 위한 **R**sponsible 

- 프로젝트 계획 및 결과의 **프로발** 

- **C**프로젝트 계획 및 결과에 대한 온트리버 

- **나는**프로젝트 계획과 결과의 형성


## <a name="best-practices-for-a-pilot"></a>시험 운용을 위한 모범 사례
파일럿을 사용하면 모든 사람을 위한 기능을 켜기 전에 소규모 그룹으로 테스트할 수 있습니다. 테스트의 일환으로 조직 내의 각 사용 사례를 철저히 테스트해야 합니다. 조직 전체에 이 작업을 롤아웃하기 전에 특정 파일럿 사용자 그룹을 대상으로 지정하는 것이 가장 좋습니다.

첫 번째 물결에서 IT, 유용성 및 피드백을 테스트하고 제공할 수 있는 기타 적절한 사용자를 대상으로 합니다. 이 피드백은 사용자에게 보내는 통신 및 지침을 더욱 개발하고 지원 담당자가 볼 수 있는 문제 유형에 대한 통찰력을 제공하는 데 사용해야 합니다. 

대상 그룹의 범위를 늘려 더 큰 사용자 그룹으로 롤아웃을 확장해야 합니다. 이 작업은 [동적 그룹 구성원 자격](../users-groups-roles/groups-dynamic-membership.md)또는 대상 그룹에 사용자를 수동으로 추가하여 수행할 수 있습니다.


## <a name="deploy-authentication"></a>인증 배포

| 기능 | Description|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. Azure MFA는 관리자가 승인한 인증 방법을 사용하여 간단한 로그인 프로세스에 대한 요구를 충족하면서 데이터 및 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. |
| [조건부 액세스](../conditional-access/plan-conditional-access.md)| 조건부 액세스를 사용하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사람에 대한 자동 액세스 제어 결정을 구현할 수 있습니다. |
| [셀프 서비스 암호 재설정](../authentication/howto-sspr-deployment.md)| 셀프 서비스 암호 재설정은 사용자가 언제 어디서나 관리자의 개입 없이 암호를 재설정하는 데 도움이 됩니다. |
| [암호 없음](../authentication/howto-authentication-passwordless-deployment.md) | 조직에서 Microsoft 인증자 앱 또는 FIDO2 보안 키를 사용하여 암호 없는 인증 구현 |

## <a name="deploy-application-management"></a>응용 프로그램 관리 배포

| 기능 | Description|
| -| - |
| [Single Sign-On](../manage-apps/plan-sso-deployment.md)| Single Sign-On을 사용하면 사용자가 한 번만 로그인하는 동안 비즈니스를 수행하는 데 필요한 앱과 리소스에 액세스할 수 있습니다. 로그인한 후에는 자격 증명을 다시 입력할 필요 없이 Microsoft Office에서 SalesForce에서 Box로 내부 응용 프로그램으로 이동하여 내부 응용 프로그램으로 보낼 수 있습니다. |
| [액세스 패널](../manage-apps/access-panel-deployment-plan.md)| 사용자에게 모든 애플리케이션을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 앱 및 그룹에 대한 액세스 요청 또는 다른 사용자 대신 리소스에 대한 액세스 관리와 같은 셀프 서비스 기능을 통해 생산성을 높일 수 있습니다. |


## <a name="deploy-hybrid-scenarios"></a>하이브리드 시나리오 배포

| 기능 | Description|
| -| -|
| [암호 해시 동기화에 ADFS 사용](../hybrid/plan-migrate-adfs-password-hash-sync.md)| 암호 해시 동기화를 사용하면 사용자 암호해시가 온-프레미스 Active Directory에서 Azure AD로 동기화되므로 Azure AD는 온-프레미스 Active Directory와의 상호 작용 없이 사용자를 인증할 수 있습니다. |
| [통과 인증에 ADFS 사용](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD 통과 인증을 사용하면 사용자가 동일한 암호를 사용하여 온-프레미스 및 클라우드 기반 응용 프로그램에 모두 로그인할 수 있습니다. 이 기능은 사용자에게 기억해야 할 암호가 하나 적고 사용자가 로그인하는 방법을 잊어버릴 가능성이 적기 때문에 IT 헬프데스크 비용을 절감할 수 있는 더 나은 환경을 제공합니다. 사람들이 Azure AD를 사용하여 로그인할 때 이 기능은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다. |
| [Azure AD 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 클라우드의 SaaS 앱과 온-프레미스의 회사 앱에 액세스해야 합니다. Azure AD 응용 프로그램 프록시를 사용하면 비용이 많이 들고 복잡한 가상 사설망(VPN) 또는 비무장 영역(DMZ)없이 이 강력한 액세스를 수행할 수 있습니다. |
| [매끄러운 SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자를 자동으로 서명합니다. 이 기능을 사용하면 사용자는 Azure AD에 로그인하기 위해 암호를 입력할 필요가 없으며 일반적으로 사용자 이름을 입력할 필요가 없습니다. 이 기능은 권한이 있는 사용자에게 추가 온-프레미스 구성 요소 없이 클라우드 기반 응용 프로그램에 쉽게 액세스할 수 있도록 합니다. |

## <a name="deploy-user-provisioning"></a>사용자 프로비저닝 배포

| 기능 | Description|
| -| -|
| [사용자 프로비전](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드 애플리케이션(SaaS)에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. |
| [클라우드 HR 사용자 프로비저닝](../app-provisioning/plan-cloud-hr-provision.md)| Active Directory에 클라우드 HR 사용자 프로비전은 지속적인 ID 거버넌스를 위한 토대를 마련하고 신뢰할 수 있는 ID 데이터에 의존하는 비즈니스 프로세스의 품질을 향상시킵니다. Workday 또는 Successfactors와 같은 클라우드 HR 제품과 함께 이 기능을 사용하면 Joiner-Mover-Leaver 프로세스(예: 신규 고용, 종료, 이전)를 IT 프로비저닝 작업(예: 만들기, 사용, 사용 해제)에 매핑하는 규칙을 구성하여 직원 및 파견 근로자의 ID 수명 주기를 원활하게 관리할 수 있습니다. |

## <a name="deploy-governance-and-reporting"></a>거버넌스 및 보고 배포

| 기능 | Description|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD PIM(Privileged Identity Management)을 사용하면 Azure AD, Azure 리소스 및 기타 Microsoft 온라인 서비스 간에 권한 있는 관리자 역할을 관리할 수 있습니다. PIM은 권한 있는 역할의 악성 활동을 실시간으로 식별, 발견 및 방지할 수 있도록 Just-In-Time 액세스, 요청 승인 워크플로 및 완전히 통합된 액세스 검토와 같은 솔루션을 제공합니다. |
| [보고 및 모니터링](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD 보고 및 모니터링 솔루션의 디자인은 기존 환경 및 프로세스뿐만 아니라 법률, 보안 및 운영 요구 사항에 따라 달라집니다. 이 문서에서는 다양한 디자인 옵션을 제시하고 올바른 배포 전략에 대한 안내를 제공합니다. |
