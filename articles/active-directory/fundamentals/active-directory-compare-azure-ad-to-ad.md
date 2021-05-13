---
title: Active Directory와 Azure Active Directory 비교
description: 이 문서에서는 ADDS(Active Directory Domain Services)와 Azure AD(Active Directory)를 비교합니다. 두 ID 솔루션의 주요 개념에 대해 간략하게 설명하고 다른 점과 유사한 점을 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371922"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Active Directory와 Azure Active Directory 비교

Azure Active Directory는 클라우드용 ID와 액세스 관리 솔루션이 다음 단계로 진화한 모습으로, Microsoft는 사용자당 단일 ID를 사용하여 여러 온-프레미스 인프라 구성 요소 및 시스템을 관리할 수 있는 기능을 조직에 제공하기 위해 Windows 2000에 Active Directory Domain Services를 도입했습니다.

Azure AD는 클라우드 및 온-프레미스에서 모든 앱에 대한 IDaaS(Identity as a Service) 솔루션을 조직에 제공하여 이 접근 방식을 한 수준 위로 끌어 올립니다.

대부분의 IT 관리자는 Active Directory Domain Services 개념을 잘 알고 있습니다. 다음 표는 Active Directory 개념과 Azure Active Directory 간의 차이점과 유사점을 간략하게 설명합니다.

|개념|AD(Active Directory)|Azure Active Directory |
|:-|:-|:-|
|**사용자**|||
|프로비저닝: 사용자 | 조직에서는 내부 사용자를 수동으로 만들거나 사내 또는 자동 프로비저닝 시스템(예: Microsoft Identity Manager)을 사용하여 HR 시스템과 통합합니다.|기존 AD 조직은 [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md)를 사용하여 ID를 클라우드에 동기화합니다.</br> Azure AD는 [클라우드 HR 시스템](../saas-apps/workday-tutorial.md)에서 사용자를 자동으로 만들 수 있도록 지원을 추가합니다. </br>Azure AD는 [SCIM 지원](../app-provisioning/use-scim-to-provision-users-and-groups.md) SaaS 앱에서 ID를 프로비저닝하여 사용자에 대한 액세스를 허용하는 데 필요한 세부 정보를 앱에 자동으로 제공합니다. |
|프로비저닝: 외부 ID| 조직에서는 외부 사용자를 전용 외부 AD 포리스트에 일반 사용자로 직접 만들어 외부 ID(게스트 사용자)의 수명 주기를 관리하는 관리 오버헤드를 생성합니다.| Azure AD는 외부 ID를 지원하는 특별한 ID 클래스를 제공합니다. [Azure AD B2B](/azure/active-directory/b2b/)는 외부 사용자 ID가 유효하도록 해당 링크를 관리합니다. |
| 권한 관리 및 그룹| 관리자가 사용자를 그룹 구성원으로 만듭니다. 그러면 앱 및 리소스 소유자가 그룹에 앱 또는 리소스에 대한 액세스 권한을 부여합니다.| [그룹](./active-directory-groups-create-azure-portal.md)은 Azure AD 에서도 사용할 수 있으며, 관리자는 그룹을 사용하여 리소스에 대한 권한을 부여할 수도 있습니다. Azure AD에서 관리자는 그룹에 멤버 자격을 수동으로 할당하거나 쿼리를 사용하여 그룹에 사용자를 동적으로 포함할 수 있습니다. </br> 관리자는 Azure AD에서 [자격 관리](../governance/entitlement-management-overview.md)를 사용하여 워크플로 및 필요한 경우 시간 기반 조건을 사용하여 앱 및 리소스 컬렉션에 대한 액세스 권한을 사용자에게 제공할 수 있습니다. |
| 관리자 관리|조직에서는 AD의 도메인, 조직 구성 단위, 그룹의 조합을 사용하여 제어하는 디렉터리와 리소스를 관리하는 관리 권한을 위임합니다.| Azure AD는 Azure AD RBAC(Azure AD 역할 기반 액세스 제어) 시스템에 [기본 제공 역할](./active-directory-users-assign-role-azure-portal.md)을 제공하며, ID 시스템, 앱과 제어하는 리소스에 대한 권한 있는 액세스를 위임하는 [사용자 지정 역할을 만드는](../roles/custom-overview.md) 기능을 제한적으로 지원합니다.</br>권한 있는 역할에 Just-In-Time, 시간 제한 또는 워크플로 기반 액세스를 제공하도록 [PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)을 사용하여 역할 관리를 향상시킬 수 있습니다. |
| 자격 증명 관리| Active Directory의 자격 증명은 암호, 인증서 인증 및 스마트 카드 인증을 기반으로 합니다. 암호 길이, 만료 및 복잡성을 기반으로 하는 암호 정책을 사용하여 암호를 관리합니다.|Azure AD는 클라우드 및 온-프레미스에 대한 지능형 [암호 보호](../authentication/concept-password-ban-bad.md)를 사용합니다. 보호에는 스마트 잠금과 일반 및 사용자 지정 암호 문구 및 대체 차단이 포함됩니다. </br>Azure AD는 FIDO2 같은 [다단계 인증](../authentication/concept-mfa-howitworks.md) 및 [암호 없는](../authentication/concept-authentication-passwordless.md) 기술을 통해 보안을 크게 강화합니다. </br>Azure AD는 사용자에게 [셀프 서비스 암호 재설정](../authentication/concept-sspr-howitworks.md) 시스템을 제공하여 지원 비용을 절감합니다. |
| **앱**|||
| 인프라 앱|Active Directory은 많은 인프라 온-프레미스 구성 요소(예: DNS, DHCP, IPSec, WiFi, NPS 및 VPN 액세스)의 기반을 형성합니다.|새로운 클라우드 환경에서 Azure AD는 앱에 액세스하고 네트워킹 제어를 사용하기 위한 새로운 제어판입니다. 사용자가 인증하는 경우 [CA(조건부 액세스)](../conditional-access/overview.md)는 필요한 조건에서 누가 어떤 앱에 액세스할 수 있는지 제어합니다.|
| 기존 앱 및 레거시 앱| 대부분의 온-프레미스 앱은 LDAP, Windows 통합 인증(NTLM 및 Kerberos) 또는 헤더 기반 인증을 사용하여 사용자에 대한 액세스를 제어합니다.| Azure AD는 온-프레미스에서 실행되는 [ Azure AD 애플리케이션 프록시](../manage-apps/application-proxy.md) 에이전트를 사용하여 이러한 유형의 온-프레미스 앱에 대한 액세스를 제공할 수 있습니다. 이 방법을 사용하는 경우 Azure AD는 마이그레이션하는 동안 또는 레거시 앱과 함께 있어야 하는 경우 Kerberos를 사용하여 온-프레미스에서 Active Directory 사용자를 인증할 수 있습니다. |
| SaaS 앱|Active Directory는 SaaS 앱을 기본적으로 지원하지 않으며 AD FS와 같은 페더레이션 시스템이 필요합니다.|OAuth2, SAML 및 WS-\*인증을 지원하는 SaaS 앱은 인증에 Azure AD를 사용하도록 통합할 수 있습니다. |
| 최신 인증을 사용하는 LOB(기간 업무) 앱|조직에서는 Active Directory와 함께 AD FS를 사용하여 최신 인증을 요구하는 LOB 앱을 지원할 수 있습니다.| 인증을 위해 Azure AD를 사용하도록 최신 인증을 필요로 하는 LOB 앱을 구성할 수 있습니다. |
| 중간 계층/디먼 서비스|온-프레미스 환경에서 실행되는 서비스는 일반적으로 AD 서비스 계정 또는 gMSA(그룹 관리 서비스 계정)를 사용하여 실행합니다. 이러한 앱은 서비스 계정의 권한을 상속합니다.| Azure AD는 클라우드에서 다른 워크로드를 실행하기 위해 [관리 ID](../managed-identities-azure-resources/index.yml)를 제공합니다. 이러한 ID의 수명 주기는 Azure AD에서 관리되고, 리소스 공급자에 연결되어 백도어 액세스를 얻는 다른 용도로 사용할 수 없습니다.|
| **디바이스**|||
| 모바일|Active Directory는 타사 솔루션 없이는 모바일 디바이스를 기본적으로 지원하지 않습니다.| Microsoft의 모바일 디바이스 관리 솔루션인 Microsoft Intune는 Azure AD와 통합됩니다. Microsoft Intune는 인증 중에 평가할 ID 시스템에 디바이스 상태 정보를 제공합니다. |
| Windows 데스크톱|Active Directory는 그룹 정책, System Center Configuration Manager 또는 기타 타사 솔루션을 사용하여 Windows 디바이스 관리할 수 있도록 Windows 디바이스의 도메인 가입 기능을 제공합니다.|Windows 디바이스는 [Azure AD에 가입](../devices/index.yml)할 수 있습니다. 조건부 액세스는 디바이스가 인증 프로세스의 일부로 Azure AD에 가입되어 있는지 확인할 수 있습니다. Windows 디바이스는 [Microsoft Intune](/intune/what-is-intune)를 사용하여 관리할 수도 있습니다. 이 경우 조건부 액세스는 앱에 대한 액세스를 허용하기 전에 디바이스가 규격을 준수하는지 여부(예: 최신 보안 패치, 바이러스 서명)를 고려합니다.|
| Windows 서버| Active Directory는 그룹 정책 또는 기타 관리 솔루션을 사용하여 온-프레미스 Windows Server에 대한 강력한 관리 기능을 제공합니다.| Azure의 Windows Server 가상 머신은 [Azure AD Domain Services](../../active-directory-domain-services/index.yml)로 관리할 수 있습니다. [관리 ID](../managed-identities-azure-resources/index.yml)는 VM이 ID 시스템 디렉터리 또는 리소스에 액세스해야 하는 경우에 사용할 수 있습니다.|
| Linux/Unix 워크로드|Active Directory를 사용하여 Kerberos 영역으로 인증하도록 Linux 머신을 구성할 수 있긴 하지만 Active Directory는 타사 솔루션 없이는 기본적으로 Windows 이외 머신은 지원하지 않습니다.|Linux/Unix VM은 [관리 ID](../managed-identities-azure-resources/index.yml)를 사용하여 ID 시스템 또는 리소스에 액세스할 수 있습니다. 일부 조직에서는 이러한 워크로드를 클라우드 컨테이너 기술로 마이그레이션하는데, 해당 기술에서도 관리 ID를 사용할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory란?](./active-directory-whatis.md)
- [자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교](../../active-directory-domain-services/compare-identity-solutions.md)
- [Azure Active Directory에 대해 자주 묻는 질문과 대답](./active-directory-faq.md)
- [Azure Active Directory의 새로운 기능](./whats-new.md)
