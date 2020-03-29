---
title: 활성 디렉터리와 Azure 활성 디렉터리 비교
description: 이 문서에서는 활성 디렉터리 도메인 서비스(ADDS)를 Azure Active Directory(AD)와 비교합니다. ID 솔루션의 주요 개념을 간략하게 설명하고 ID 솔루션이 어떻게 다르거나 유사한지 설명합니다.
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
ms.openlocfilehash: 5075ae57df6a7306f0c860690931c846e52c2a89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926895"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>활성 디렉터리와 Azure 활성 디렉터리 비교

Azure Active Directory는 클라우드에 대한 ID 및 액세스 관리 솔루션의 다음 진화입니다. Microsoft는 Windows 2000에서 Active Directory 도메인 서비스를 도입하여 조직에 사용자당 단일 ID를 사용하여 여러 온-프레미스 인프라 구성 요소 및 시스템을 관리할 수 있는 기능을 제공합니다.

Azure AD는 클라우드 및 온-프레미스에서 모든 앱에 대해 Id를 서비스(IdaaS) 솔루션으로 조직에 제공함으로써 이 접근 방식을 다음 단계로 끌어올립니다.

대부분의 IT 관리자는 Active Directory 도메인 서비스 개념을 잘 알고 있습니다. 다음 표에서는 Active Directory 개념과 Azure Active Directory 간의 차이점과 유사점을 간략하게 설명합니다.

|개념|AD(Active Directory)|Azure Active Directory |
|:-|:-|:-|
|**사용자**|||
|프로비저닝: 사용자 | 조직은 내부 사용자를 수동으로 만들거나 Microsoft ID 관리자와 같은 사내 또는 자동화된 프로비저닝 시스템을 사용하여 HR 시스템과 통합합니다.|기존 AD 조직은 [Azure AD Connect를](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 사용하여 ID를 클라우드에 동기화합니다.</br> Azure AD는 [클라우드 HR 시스템에서](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)사용자를 자동으로 만드는 지원을 추가합니다. </br>Azure AD는 [SCIM 사용](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) SaaS 앱에서 ID를 프로비전하여 사용자에게 액세스를 허용하는 데 필요한 세부 정보를 앱에 자동으로 제공할 수 있습니다. |
|프로비저닝: 외부 ID| 조직은 전용 외부 AD 포리스트에서 일반 사용자로 외부 사용자를 수동으로 생성하여 외부 ID(게스트 사용자)의 수명 주기를 관리하기 위한 관리 오버헤드를 초래합니다.| Azure AD는 외부 ID를 지원하는 특별한 ID 클래스를 제공합니다. [Azure AD B2B는](https://docs.microsoft.com/azure/active-directory/b2b/) 외부 사용자 ID에 대한 링크를 관리하여 유효한지 확인합니다. |
| 권한 관리 및 그룹| 관리자는 그룹의 사용자 구성원을 만듭니다. 그런 다음 앱 및 리소스 소유자는 그룹에 앱 또는 리소스에 대한 액세스 권한을 부여합니다.| [Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) AD에서도 그룹을 사용할 수 있으며 관리자는 그룹을 사용하여 리소스에 대한 권한을 부여할 수도 있습니다. Azure AD에서 관리자는 그룹에 수동으로 구성원자격을 할당하거나 쿼리를 사용하여 사용자를 그룹에 동적으로 포함시킬 수 있습니다. </br> 관리자는 Azure AD에서 [권한 부여 관리를](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) 사용하여 사용자에게 워크플로 및 필요한 시간 기반 기준을 사용하여 앱 및 리소스 컬렉션에 대한 액세스 권한을 부여할 수 있습니다. |
| 관리 관리|조직은 AD의 도메인, 조직 단위 및 그룹을 조합하여 관리 권한을 위임하여 제어하는 디렉터리 및 리소스를 관리합니다.| Azure AD는 역할 기반 액세스 제어(RBAC) 시스템을 사용하여 [기본 제공 역할을](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) 제공하며, ID 시스템, 앱 및 제어하는 리소스에 대한 권한 있는 액세스를 위임하기 위한 사용자 지정 역할을 [만드는](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview) 데 대한 지원이 제한적입니다.</br>권한 있는 ID [관리(PIM)를](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 사용하여 역할 관리를 향상하여 권한 있는 역할에 대한 적시, 시간 제한 또는 워크플로 기반 액세스를 제공할 수 있습니다. |
| 자격 증명 관리| Active Directory의 자격 증명은 암호, 인증서 인증 및 스마트 카드 인증을 기반으로 합니다. 암호는 암호 길이, 만료 및 복잡성을 기반으로 하는 암호 정책을 사용하여 관리됩니다.|Azure AD는 클라우드 및 온-프레미스에 지능형 [암호 보호를](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) 사용합니다. 보호에는 스마트 잠금 과 일반 및 사용자 지정 암호 구 및 대체 차단이 포함됩니다. </br>Azure AD는 FIDO2와 같은 [다단계 인증](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 및 [암호 없는](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) 기술을 통해 보안을 크게 향상시킵니다. </br>Azure AD는 사용자에게 셀프 [서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) 시스템을 제공하여 지원 비용을 절감합니다. |
| **앱**|||
| 인프라 앱|Active Directory는 DNS, DHCP, IPSec, WiFi, NPS 및 VPN 액세스와 같은 많은 인프라 온-프레미스 구성 요소의 기초를 형성합니다.|새 클라우드 세계에서 Azure AD는 네트워킹 컨트롤에 의존하는 앱에 액세스하는 대신 앱에 액세스하기 위한 새로운 제어 평면입니다. 사용자가 인증할 때[, 조건부 액세스(CA)는](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)어떤 사용자를 제어하고 필요한 조건에서 어떤 앱에 액세스할 수 있습니다.|
| 기존 및 레거시 앱| 대부분의 온-프레미스 앱은 LDAP, Windows 통합 인증(NTLM 및 Kerberos) 또는 헤더 기반 인증을 사용하여 사용자에 대한 액세스를 제어합니다.| Azure AD는 온-프레미스에서 실행되는 [Azure AD 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 에이전트를 사용하여 이러한 유형의 온-프레미스 앱에 대한 액세스를 제공할 수 있습니다. 이 방법을 사용하면 Azure AD는 레거시 앱과 공존하거나 마이그레이션해야 하는 동안 Kerberos를 사용하여 온-프레미스에서 Active Directory 사용자를 인증할 수 있습니다. |
| SaaS 앱|Active Directory는 기본적으로 SaaS 앱을 지원하지 않으며 AD FS와 같은 페더레이션 시스템이 필요합니다.|OAuth2, SAML 및 WS-인증을\* 지원하는 SaaS 앱을 통합하여 인증에 Azure AD를 사용할 수 있습니다. |
| 최신 인증을 갖춘 LOB(비즈니스 라인) 앱|조직은 Active Directory와 함께 AD FS를 사용하여 최신 인증이 필요한 LOB 앱을 지원할 수 있습니다.| 최신 인증이 필요한 LOB 앱은 인증에 Azure AD를 사용하도록 구성할 수 있습니다. |
| 미드 티어/데몬 서비스|온-프레미스 환경에서 실행되는 서비스는 일반적으로 AD 서비스 계정 또는 그룹 관리 서비스 계정(gMSA)을 사용하여 실행합니다. 그러면 이러한 앱은 서비스 계정의 권한을 상속합니다.| Azure AD는 클라우드에서 다른 워크로드를 실행하기 위해 [관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) 제공합니다. 이러한 ID의 수명 주기는 Azure AD에서 관리되며 리소스 공급자에 연결되어 백도어 액세스를 얻기 위해 다른 용도로 사용할 수 없습니다.|
| **디바이스**|||
| 휴대폰|Active Directory는 타사 솔루션없이 기본적으로 모바일 장치를 지원하지 않습니다.| 마이크로소프트의 모바일 장치 관리 솔루션, 마이크로소프트 Intune, Azure AD와 통합. Microsoft Intune은 인증 중에 평가할 ID 시스템에 장치 상태 정보를 제공합니다. |
| 윈도우 데스크톱|Active Directory는 그룹 정책, 시스템 센터 구성 관리자 또는 기타 타사 솔루션을 사용하여 Windows 장치를 도메인으로 통합하여 관리할 수 있는 기능을 제공합니다.|Windows 장치를 [Azure AD에 조인할](https://docs.microsoft.com/azure/active-directory/devices/)수 있습니다. 조건부 액세스는 장치가 인증 프로세스의 일부로 조인된 Azure AD인지 확인할 수 있습니다. 윈도우 장치는 [마이크로 소프트 인튠으로](https://docs.microsoft.com/intune/what-is-intune)관리 할 수 있습니다. 이 경우 조건부 액세스는 앱에 대한 액세스를 허용하기 전에 장치가 불만 사항(예: 최신 보안 패치 및 바이러스 서명)인지 여부를 고려합니다.|
| Windows 서버| Active Directory는 그룹 정책 또는 기타 관리 솔루션을 사용하여 온-프레미스 Windows 서버에 대한 강력한 관리 기능을 제공합니다.| Azure의 Windows 서버 가상 컴퓨터는 [Azure AD 도메인 서비스](https://docs.microsoft.com/azure/active-directory-domain-services/)로 관리할 수 있습니다. [관리되는 ID는](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) VM이 ID 시스템 디렉터리 또는 리소스에 액세스해야 하는 경우에 사용할 수 있습니다.|
| 리눅스/유닉스 워크로드|Active Directory는 타사 솔루션 없이 는 Windows 이외의 프로그램을 기본적으로 지원하지 않지만 Linux 컴퓨터는 Kerberos 영역으로 Active Directory로 인증하도록 구성할 수 있습니다.|Linux/Unix VM은 [관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) 사용하여 ID 시스템 또는 리소스에 액세스할 수 있습니다. 일부 조직에서는 이러한 워크로드를 관리되는 ID를 사용할 수도 있는 클라우드 컨테이너 기술로 마이그레이션합니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory란?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [Azure Active Directory에 대해 자주 묻는 질문과 대답](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Azure Active 디렉터리의 새로운 내용이 무엇입니까?](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
