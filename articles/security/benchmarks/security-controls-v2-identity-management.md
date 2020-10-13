---
title: Azure Security 벤치 마크 V2-Id 관리
description: Azure 보안 벤치 마크 V2 Identity Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e7447d03fc231d8d96b71c7d944a380c10add0d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757939"
---
# <a name="security-control-v2-identity-management"></a>보안 제어 V2: Id 관리

Id 관리는 Azure Active Directory를 사용 하 여 보안 id 및 액세스 제어를 설정 하는 컨트롤을 포함 합니다. 여기에는 응용 프로그램, 조건부 액세스 및 계정 변칙 모니터링에 대 한 Single Sign-On, 강력한 인증, 관리 되는 id (및 서비스 원칙)의 사용이 포함 됩니다.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: 중앙 id 및 인증 시스템으로 Azure Active Directory 표준화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-1 | 16.1, 16.2, 16.4, 16.5 | IA-2, IA-8, AC-2, AC-3 |

Azure AD (Azure Active Directory)는 Azure의 기본 id 및 액세스 관리 서비스입니다. 에서 조직의 id 및 액세스 관리를 관리 하려면 Azure AD를 표준화 해야 합니다.
- Azure Portal, Azure Storage, Azure Virtual Machines (Linux 및 Windows), Azure Key Vault, PaaS 및 SaaS 응용 프로그램과 같은 Microsoft 클라우드 리소스

- Azure의 응용 프로그램 또는 회사 네트워크 리소스와 같은 조직의 리소스

Azure AD 보안은 조직의 클라우드 보안 관행에서 높은 우선 순위를 두어야 합니다. Azure AD는 Microsoft의 모범 사례 권장 사항에 따라 id 보안 상태를 평가 하는 데 도움이 되는 id 보안 점수를 제공 합니다. 점수를 사용 하 여 구성이 모범 사례 권장 사항에 얼마나 근접 하 게 일치 하는지 측정 하 고 보안 상태를 개선할 수 있습니다.

참고: Azure AD는 외부 id를 사용 하 여 응용 프로그램 및 리소스에 로그인 할 수 있는 Microsoft 계정 없는 사용자를 허용 하는 외부 id 공급자를 지원 합니다.

- [Azure AD의 테 넌 트](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD 테 넌 트 정의](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [응용 프로그램에 외부 id 공급자 사용](/azure/active-directory/b2b/identity-providers)

- [Azure AD에서 id 보안 점수는 무엇 인가요?](../../active-directory/fundamentals/identity-secure-score.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 응용 프로그램 id를 안전 하 게 자동으로 관리

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-2 | N/A | AC-2, AC-3, IA-2, IA-4, IA-9 |

서비스 또는 자동화와 같은 비 사용자 계정의 경우 리소스에 액세스 하거나 코드를 실행 하는 데 더 강력한 인간 계정을 만드는 대신 Azure 관리 id를 사용 합니다. Azure 관리 되는 id는 azure AD 인증을 지 원하는 Azure 서비스 및 리소스에 인증할 수 있습니다. 소스 코드 또는 구성 파일에서 하드 코드 된 자격 증명을 방지 하기 위해 미리 정의 된 액세스 권한 부여 규칙을 통해 인증을 사용할 수 있습니다. 

관리 id를 지원 하지 않는 서비스의 경우 Azure AD를 사용 하 여 리소스 수준에서 제한 된 권한으로 서비스 주체를 만듭니다.  인증서 자격 증명을 사용 하 여 서비스 주체를 구성 하 고 클라이언트 암호를 대체 하는 것이 좋습니다. 두 경우 모두 Azure 관리 id와 함께 Azure Key Vault를 사용 하 여 런타임 환경 (예: Azure 함수)이 키 자격 증명 모음에서 자격 증명을 검색할 수 있습니다.

- [Azure 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 서비스 주체](/powershell/azure/create-azure-service-principal-azureps)

- [인증서를 사용 하 여 서비스 주체 만들기](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

보안 주체 등록에 Azure Key Vault 사용: 인증 # 권한 부여-보안 주체-키-자격 증명 모음

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 응용 프로그램 액세스에 Azure AD Single Sign-On (SSO) 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-3 | 4.4. | IA-2, IA-4 |

Azure AD는 Azure 리소스, 클라우드 응용 프로그램 및 온-프레미스 응용 프로그램에 id 및 액세스 관리를 제공 합니다. Id 및 액세스 관리는 파트너, 공급 업체 및 공급 업체와 같은 외부 id 뿐만 아니라 직원 등의 엔터프라이즈 id에 적용 됩니다.

Azure AD Single Sign-On (SSO)를 사용 하 여 온-프레미스 및 클라우드에서 조직의 데이터와 리소스에 대 한 액세스를 관리 하 고 보호 합니다. 모든 사용자, 응용 프로그램 및 장치를 Azure AD에 연결 하 여 원활 하 고 안전한 액세스를 강화 하 고 가시성과 제어를 강화 하세요. 

- [Azure AD를 사용 하 여 응용 프로그램 SSO 이해](../../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 대해 강력한 인증 제어 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-4 | 4.2, 4.4 4.5, 11.5, 12.11, 16.3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD는 MFA (multi-factor authentication) 및 강력한 암호 없는 메서드를 통한 강력한 인증 제어를 지원 합니다.  
- Multi-factor authentication: Azure AD MFA를 사용 하도록 설정 하 고 MFA 설정에 대 한 Azure Security Center id 및 액세스 관리 권장 사항을 따릅니다. MFA는 모든 사용자에 게 적용 하거나, 사용자를 선택 하거나, 로그인 조건 및 위험 요소에 따라 사용자 단위 수준에서 적용할 수 있습니다. 

- 암호 없는 인증: 3 개의 암호 없는 인증 옵션을 사용할 수 있습니다. Windows Hello for Business, Microsoft Authenticator 앱 및 스마트 카드와 같은 온-프레미스 인증 방법입니다. 

관리자 및 권한 있는 사용자의 경우 가장 높은 수준의 강력한 인증 방법을 사용 하 고 그 다음에는 적절 한 강력한 인증 정책을 다른 사용자에 게 배포 해야 합니다.

Azure AD 인증에 레거시 암호 기반 인증을 여전히 사용 하는 경우 클라우드 전용 계정 (Azure에서 직접 만든 사용자 계정)에 기본 기준 암호 정책이 있습니다. 하이브리드 계정 (온-프레미스 Active Directory에서 제공 하는 사용자 계정)은 온-프레미스 암호 정책을 따릅니다. 암호 기반 인증을 사용 하는 경우 Azure AD는 사용자가 추측 하기 쉬운 암호를 설정할 수 없도록 하는 암호 보호 기능을 제공 합니다. Microsoft는 원격 분석을 기반으로 업데이트 되는 금지 된 암호의 전체 목록을 제공 하며, 고객은 필요에 따라 (예: 브랜딩, 문화적 참조 등) 목록을 확장할 수 있습니다. 이 암호 보호는 클라우드 전용 및 하이브리드 계정에 사용할 수 있습니다. 

참고: 암호 자격 증명을 기반으로 하는 인증은 널리 사용 되는 공격 방법의 영향을 받습니다. 보안을 강화 하려면 MFA 및 강력한 암호 정책과 같은 강력한 인증을 사용 합니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 marketplace 서비스의 경우 초기 서비스를 설정 하는 동안 변경 해야 합니다. 

- [Azure에서 MFA를 사용하도록 설정하는 방법](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory에 대 한 암호 없는 인증 옵션 소개](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 기본 암호 정책](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD 암호 보호를 사용 하 여 잘못 된 암호 제거](../../active-directory/authentication/concept-password-ban-bad.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 계정 변칙에 대 한 모니터링 및 경고

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-5 | 4.8, 4.9, 16.12, 16.13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD는 다음과 같은 데이터 원본을 제공 합니다. 
-   로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.

-   감사 로그-Azure AD의 다양 한 기능을 통해 수행 된 모든 변경 내용에 대 한 로그를 통해 추적 기능을 제공 합니다. 기록 된 변경 감사 로그의 예로는 사용자, 앱, 그룹, 역할 및 정책을 추가 하거나 제거 합니다.

-   위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

-   위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이러한 데이터 원본은 Azure Monitor, Azure 센티널 또는 타사 SIEM 시스템과 통합할 수 있습니다.

Azure Security Center은 과도 한 인증 시도 횟수와 같은 의심 스러운 특정 활동, 구독에서 사용 되지 않는 계정에 대해 경고할 수도 있습니다. 

Azure ATP (Advanced Threat Protection)는 온-프레미스 Active Directory 신호를 사용 하 여 고급 위협, 손상 된 id 및 악의적인 참가자 작업을 식별 하 고 검색 하 고 조사할 수 있는 보안 솔루션입니다.

- [Azure AD의 감사 활동 보고서](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../../security-center/security-center-identity-access.md)

- [Azure Security Center의 위협 인텔리전스 보호 모듈의 경고](/azure/security-center/alerts-reference)

- [Azure Monitor에 Azure 활동 로그를 통합 하는 방법](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD ID 보호에서 데이터 연결](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-6 | N/A | AC-2, AC-3 |

사용자 정의 조건에 따라 보다 세부적인 액세스 제어를 위해 Azure AD 조건부 액세스를 사용 합니다 (예: 특정 IP 범위의 사용자 로그인에 MFA를 사용 하도록 요구). 다양 한 사용 사례에 대 한 Azure AD 조건부 액세스 정책을 통해 세부적인 인증 세션 관리를 사용할 수도 있습니다. 

- [Azure 조건부 액세스 개요](../../active-directory/conditional-access/overview.md)

- [일반 조건부 액세스 정책](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [조건부 액세스를 사용하여 인증 세션 관리를 구성합니다.](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: 의도 하지 않은 자격 증명 노출 제거

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-7 | 18.1, 18.7 | IA-5 |

Azure DevOps 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색 된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동 하는 것을 권장 합니다.

GitHub의 경우 네이티브 암호 검색 기능을 사용 하 여 코드 내에서 자격 증명 또는 다른 형식의 암호를 식별할 수 있습니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 암호 검색](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: 레거시 응용 프로그램에 대 한 보안 사용자 액세스

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| IM-8 | 14.6 | AC-2, AC-3, SC-11 |

레거시 응용 프로그램 및 저장 하 고 처리 하는 데이터에 대 한 최신 액세스 제어 및 세션 모니터링이 있는지 확인 합니다. Vpn은 일반적으로 레거시 응용 프로그램에 액세스 하는 데 사용 되지만 기본 액세스 제어 및 제한 된 세션 모니터링만 포함 하는 경우가 많습니다.

Azure AD 응용 프로그램 프록시를 사용 하면 Azure AD 조건부 액세스를 사용 하 여 원격 사용자와 장치 모두에 대 한 신뢰성을 명시적으로 검사 하면서 레거시 온-프레미스 응용 프로그램을 Single Sign-On (SSO)를 사용 하는 원격 사용자에 게 게시할 수 

또는 기존 온-프레미스 응용 프로그램과 클라우드 SaaS (software as a service) 응용 프로그램 모두에 대 한 사용자의 응용 프로그램 세션 및 차단 작업을 모니터링 하는 컨트롤을 제공할 수 있는 CASB (cloud access Security broker) 서비스 Microsoft Cloud App Security입니다. 

- [Azure AD 응용 프로그램 프록시](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security 모범 사례](/cloud-app-security/best-practices)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
