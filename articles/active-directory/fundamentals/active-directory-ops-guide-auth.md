---
title: Azure Active Directory 인증 관리 작업 참조 가이드
description: 이 작업 참조 가이드는 인증 관리를 보호하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: f25abb70a95f559cf0cc14efa6cf9f0e81ec9ec0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876295"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory 인증 관리 작업 참조 가이드

[Azure AD 작업 참조 가이드의](active-directory-ops-guide-intro.md) 이 섹션에서는 자격 증명을 보호 및 관리하고, 인증 환경을 정의하고, 대리 할당을 수행하고, 사용량을 측정하고, 엔터프라이즈 보안 태세를 기반으로 액세스 정책을 정의하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지남에 따라 변경될 수 있습니다. Microsoft 제품 및 서비스가 시간이 지남에 따라 발전함에 따라 조직은 ID 관행을 지속적으로 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 롤아웃 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행해야 합니다. 환경을 최적화하기 위해 이러한 작업을 설정하는 것은 여전히 중요합니다. 주요 작업 및 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Azure AD에서 단일 사인온(SSO) 구성의 수명 주기 관리 | IAM 운영 팀 |
| Azure AD 응용 프로그램에 대한 조건부 액세스 정책 설계 | 인포섹 아키텍처 팀 |
| SIEM 시스템의 로그인 활동 보관 | 인포섹 운영 팀 |
| SIEM 시스템에 위험 이벤트 보관 | 인포섹 운영 팀 |
| 보안 보고서 심사 및 조사 | 인포섹 운영 팀 |
| 위험 이벤트 심사 및 조사 | 인포섹 운영 팀 |
| Azure AD ID 보호의 위험 및 취약성 보고서에 플래그가 지정된 사용자를 심사및 조사합니다. | 인포섹 운영 팀 |

> [!NOTE]
> Azure AD ID 보호에는 Azure AD 프리미엄 P2 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [Azure AD Free 및 Azure AD 프리미엄 에디션의 일반적으로 사용 가능한 기능 비교를](https://azure.microsoft.com/pricing/details/active-directory/)참조하십시오.

목록을 검토할 때 소유자가 없는 작업에 대해 소유자를 할당하거나 위의 권장 사항과 일치하지 않는 소유자와 작업에 대한 소유권을 조정해야 할 수 있습니다.

#### <a name="owner-recommended-reading"></a>소유자 추천 독서

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>자격 증명 관리

### <a name="password-policies"></a>암호 정책

암호를 안전하게 관리하는 것은 ID 및 액세스 관리의 가장 중요한 부분 중 하나이며 종종 공격의 가장 큰 대상입니다. Azure AD는 공격이 성공하지 못하도록 하는 데 도움이 되는 몇 가지 기능을 지원합니다.

아래 표를 사용하여 해결해야 하는 문제를 완화하는 데 권장되는 솔루션을 찾으십시오.

| 문제 | 권장 |
| :- | :- |
| 약한 암호로부터 보호할 수 있는 메커니즘없음 | Azure AD [셀프 서비스 암호 재설정(SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) 및 암호 [보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) 사용 |
| 유출된 암호를 감지하는 메커니즘이 없습니다. | [PHS(암호 해시 동기화)를](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) 사용하여 인사이트를 얻습니다. |
| AD FS를 사용하여 관리되는 인증으로 이동할 수 없음 | [AD FS 엑스트라넷 스마트 잠금](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) 및/ 또는 [Azure AD 스마트 잠금 사용](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| 암호 정책은 길이, 다중 문자 집합 또는 만료와 같은 복잡성 기반 규칙을 사용합니다. | [Microsoft 권장 사례를](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) 다시 고려하고 암호 관리에 대한 접근 방식을 전환하고 Azure [AD 암호 보호를](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)배포합니다. |
| 사용자가 MFA(다단계 인증)를 사용하도록 등록되지 않았습니다. | [모든 사용자의 보안 정보를 등록하여](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) 암호와 함께 사용자의 ID를 확인하는 메커니즘으로 사용할 수 있습니다. |
| 사용자 위험에 따라 암호가 해지되지 않습니다. | Azure AD [ID 보호 사용자 위험 정책](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) 배포하여 SSPR을 사용하여 유출된 자격 증명에 암호 변경을 강제로 변경합니다. |
| 식별된 IP 주소에서 오는 악의적인 공격자로부터 악의적인 인증을 보호하는 스마트 잠금 메커니즘이 없습니다. | 암호 해시 동기화 또는 통과 인증(PTA)을 통해 클라우드 관리 [인증](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) 배포 |

#### <a name="password-policies-recommended-reading"></a>암호 정책 권장 읽기

- [Azure AD 및 AD FS 모범 사례: 암호 스프레이 공격 방지 - 엔터프라이즈 이동성 + 보안](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>셀프 서비스 암호 재설정 및 암호 보호 지원

암호를 변경하거나 재설정해야 하는 사용자는 헬프 데스크 호출의 가장 큰 볼륨 및 비용 소스 중 하나입니다. 비용 외에도 사용자 위험을 완화하는 도구로 암호를 변경하는 것은 조직의 보안 상태를 개선하는 기본 단계입니다.

최소한 Azure AD [셀프 서비스 암호 재설정(SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) 및 온-프레미스 [암호 보호를](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) 배포하여 다음을 수행하는 것이 좋습니다.

- 헬프 데스크 호출을 편향합니다.
- 임시 암호 사용을 바꿉니까?
- 온-프레미스 솔루션에 의존하는 기존 셀프 서비스 암호 관리 솔루션을 교체합니다.
- 조직의 [취약한 암호를 제거합니다.](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

> [!NOTE]
> Azure AD Premium P2 구독이 있는 조직의 경우 SSPR을 배포하고 [ID 보호 사용자 위험 정책의](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)일부로 사용하는 것이 좋습니다.

### <a name="strong-credential-management"></a>강력한 자격 증명 관리

암호 자체는 악의적인 행위자가 사용자 환경에 액세스하지 못하도록 충분히 안전하지 않습니다. 최소한 권한이 있는 계정이 있는 모든 사용자는 MFA(다단계 인증)를 사용하도록 설정해야 합니다. 이상적으로는 [결합된 등록을](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) 사용하도록 설정하고 결합된 [등록 환경을](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)사용하여 모든 사용자가 MFA 및 SSPR에 등록하도록 요구하는 것이 좋습니다. 결국 예기치 않은 상황으로 인한 잠금 위험을 줄이기 위해 [복원력을 제공하는](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) 전략을 채택하는 것이 좋습니다.

![결합된 사용자 경험 흐름](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>온-프레미스 중단 인증 복원력

단순성과 유출된 자격 증명 검색의 이점 외에도 Azure AD 암호 해시 동기화(PHS) 및 Azure MFA를 사용하면 [NotPetya와](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)같은 사이버 공격으로 인한 온-프레미스 중단에도 불구하고 사용자가 SaaS 응용 프로그램 및 Office 365에 액세스할 수 있습니다. 페더레이션과 함께 PHS를 활성화할 수도 있습니다. PHS를 사용하도록 설정하면 페더레이션 서비스를 사용할 수 없는 경우 인증을 대체할 수 있습니다.

온-프레미스 조직에 중단 복원력 전략이 없거나 Azure AD와 통합되지 않은 전략이 있는 경우 Azure AD PHS를 배포하고 PHS를 포함하는 재해 복구 계획을 정의해야 합니다. Azure AD PHS를 사용하도록 설정하면 온-프레미스 활성 디렉터리를 사용할 수 없는 경우 사용자가 Azure AD에 대해 인증할 수 있습니다.

![암호 해시 동기화 흐름](./media/active-directory-ops-guide/active-directory-ops-img5.png)

인증 옵션을 더 잘 이해하려면 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택을](../hybrid/choose-ad-authn.md)참조하십시오.

### <a name="programmatic-usage-of-credentials"></a>자격 증명의 프로그래밍 방식 사용

PowerShell을 사용하는 Azure AD 스크립트 또는 Microsoft 그래프 API를 사용하는 응용 프로그램에는 보안 인증이 필요합니다. 이러한 스크립트와 도구를 실행하는 잘못된 자격 증명 관리는 자격 증명 도용의 위험을 증가시게 합니다. 하드 코딩된 암호 또는 암호 프롬프트에 의존하는 스크립트 또는 응용 프로그램을 사용하는 경우 먼저 구성 파일 또는 소스 코드의 암호를 검토한 다음 해당 종속성을 대체하고 가능하면 Azure 관리 ID, 통합 Windows 인증 또는 [인증서를](../reports-monitoring/tutorial-access-api-with-certificates.md) 사용해야 합니다. 이전 솔루션을 사용할 수 없는 응용 프로그램의 경우 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하는 것이 좋습니다.

암호 자격 증명이 있는 서비스 주체가 있다고 판단하고 스크립트 또는 응용 프로그램에서 해당 암호 자격 증명을 어떻게 보호되는지 잘 모르는 경우 응용 프로그램 소유자에게 문의하여 사용 패턴을 더 잘 이해하십시오.

또한 암호 자격 증명이 있는 서비스 주체가 있는 경우 응용 프로그램 소유자에게 문의하여 사용 패턴을 이해하는 것이 좋습니다.

## <a name="authentication-experience"></a>인증 경험

### <a name="on-premises-authentication"></a>온-프레미스 인증

IWA(통합 Windows 인증) 또는 암호 해시 동기화 또는 통과 인증을 통한 관리형 인증(SSO)을 통한 페더레이션 인증은 온-프레미스 도메인 컨트롤러에 대한 가시선이 있는 회사 네트워크 내부에서 최상의 사용자 환경입니다. 자격 증명 프롬프트 피로를 최소화하고 사용자가 피싱 공격에 빠질 위험을 줄입니다. PHS 또는 PTA를 사용하여 클라우드 관리 인증을 이미 사용하고 있지만 온-프레미스에서 인증할 때 사용자가 암호를 입력해야 하는 경우 [즉시 원활한 SSO를 배포해야](../hybrid/how-to-connect-sso.md)합니다. 반면에 현재 클라우드 관리 인증으로 마이그레이션할 계획으로 페더레이션된 경우 마이그레이션 프로젝트의 일부로 원활한 SSO를 구현해야 합니다.

### <a name="device-trust-access-policies"></a>장치 신뢰 액세스 정책

디바이스는 조직의 사용자처럼 보호해야 하는 핵심 ID입니다. 디바이스의 ID를 사용하여 언제 어디서든 리소스를 보호할 수 있습니다.장치를 인증하고 신뢰 유형을 고려하면 다음을 통해 보안 상태와 유용성이 향상됩니다.

- 장치를 신뢰할 수 있는 경우 MFA와 같은 마찰 방지
- 신뢰할 수 없는 장치에서 액세스 차단
- Windows 10 장치의 경우 [온-프레미스 리소스에 대한 단일 사인온 리소스를 원활하게](../devices/azuread-join-sso.md)제공합니다.

다음 방법 중 하나를 사용하여 장치 ID를 가져오고 Azure AD에서 관리하여 이 목표를 수행할 수 있습니다.

- 조직은 [Microsoft Intune을](https://docs.microsoft.com/intune/what-is-intune) 사용하여 장치를 관리하고 규정 준수 정책을 적용하고, 장치 상태를 증명하고, 장치가 호환되는지 여부에 따라 조건부 액세스 정책을 설정할 수 있습니다. 마이크로소프트 인튠은 iOS 장치, 맥 데스크톱 (JAMF 통합을 통해), 윈도우 데스크톱 (기본적으로 윈도우에 대 한 모바일 장치 관리를 사용 하 여 10, 그리고 마이크로소프트 엔드 포인트 구성 관리자와 공동 관리) 및 안 드 로이드 모바일 장치 관리할 수 있습니다.
- [하이브리드 Azure AD 조인은](../devices/hybrid-azuread-join-managed-domains.md) Active Directory 도메인 에 가입한 컴퓨터 장치가 있는 환경에서 그룹 정책 또는 Microsoft 엔드포인트 구성 관리자를 사용하여 관리를 제공합니다. 조직은 원활한 SSO를 통해 PHS 또는 PTA를 통해 관리되는 환경을 배포할 수 있습니다. 장치를 Azure AD로 가져오면 클라우드 및 온-프레미스 리소스 전반에 걸쳐 SSO를 통해 사용자 생산성을 극대화하는 동시에 [조건부 액세스를](../conditional-access/overview.md) 통해 클라우드 및 온-프레미스 리소스에 대한 보안을 확보할 수 있습니다.

클라우드에 등록되지 않은 도메인 조인 Windows 장치 또는 클라우드에 등록되었지만 조건부 액세스 정책이 없는 도메인 에 가입된 Windows 장치가 있는 경우 등록되지 않은 장치를 등록하고 둘 중 어느 경우든 [하이브리드 Azure AD 조인을](../conditional-access/require-managed-devices.md) 조건부 액세스 정책의 컨트롤로 사용해야 합니다.

![하이브리드 장치가 필요한 조건부 액세스 정책에서 부여스크린샷](./media/active-directory-ops-guide/active-directory-ops-img6.png)

MDM 또는 Microsoft Intune을 사용하여 장치를 관리하지만 조건부 액세스 정책에서 장치 컨트롤을 사용하지 않는 경우 해당 정책의 [컨트롤로 준수로 표시하기 위해 장치 필요를](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) 사용하는 것이 좋습니다.

![장치 준수를 요구하는 조건부 액세스 정책에서 부여스크린샷](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>장치 신뢰 액세스 정책 권장 읽기

- [방법: 하이브리드 Azure Active Directory 조인 구현 계획](../devices/hybrid-azuread-join-plan.md)
- [ID 및 디바이스 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

Windows 10에서 [비즈니스용 Windows Hello는](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) 암호를 PC에서 강력한 이중 인증으로 대체합니다. 비즈니스용 Windows Hello를 사용하면 사용자에게 보다 간소화된 MFA 환경을 구현하고 암호에 대한 의존도를 줄일 수 있습니다. Windows 10 장치를 롤아웃하기 시작하지 않았거나 일부만 배포한 경우 Windows 10으로 업그레이드하고 모든 장치에서 [비즈니스용 Windows Hello를 사용하도록 설정하는](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) 것이 좋습니다.

암호 없는 인증에 대 한 자세한 내용은 [Azure Active Directory를 사용 하](../authentication/concept-authentication-passwordless.md)여 암호가 없는 세계를 참조 하십시오.

## <a name="application-authentication-and-assignment"></a>응용 프로그램 인증 및 할당

### <a name="single-sign-on-for-apps"></a>앱에 대한 단일 사인온

전체 엔터프라이즈에 표준화된 단일 사인온 메커니즘을 제공하는 것은 최상의 사용자 경험, 위험 감소, 보고 능력 및 거버넌스에 매우 중요합니다. Azure AD에서 SSO를 지원하지만 현재 로컬 계정을 사용하도록 구성된 응용 프로그램을 사용하는 경우 해당 응용 프로그램을 Azure AD와 함께 사용하도록 다시 구성해야 합니다. 마찬가지로 Azure AD를 사용하여 SSO를 지원하지만 다른 ID 공급자를 사용하는 응용 프로그램을 사용하는 경우 해당 응용 프로그램을 Azure AD와 함께 SSO를 사용하도록 다시 구성해야 합니다. 페더레이션 프로토콜을 지원하지 않지만 양식 기반 인증을 지원하는 응용 프로그램의 경우 Azure AD 응용 프로그램 프록시를 사용하여 [암호 보관을](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) 사용하도록 응용 프로그램을 구성하는 것이 좋습니다.

![앱프록시 암호 기반 사인온](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 조직에서 관리되지 않는 응용 프로그램을 검색하는 메커니즘이 없는 경우 [Microsoft 클라우드 앱 보안과](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)같은 CASB(클라우드 액세스 보안 브로커 솔루션)를 사용하여 검색 프로세스를 구현하는 것이 좋습니다.

마지막으로 Azure AD 앱 갤러리가 있고 Azure AD를 사용하여 SSO를 지원하는 응용 프로그램을 사용하는 경우 [응용 프로그램 갤러리에 응용 프로그램을 나열하는](../azuread-dev/howto-app-gallery-listing.md)것이 좋습니다.

#### <a name="single-sign-on-recommended-reading"></a>단일 사인온 권장 읽기

- [Azure Active Directory를 사용하면 응용 프로그램 액세스 및 단일 사인온이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>AD FS 응용 프로그램을 Azure AD로 마이그레이션

[AD FS에서 Azure AD로 앱을 마이그레이션하면](../manage-apps/migrate-adfs-apps-to-azure.md) 보안, 보다 일관된 관리 용이성 및 더 나은 공동 작업 환경을 위한 추가 기능을 사용할 수 있습니다. Azure AD를 사용하여 SSO를 지원하는 AD FS에 구성된 응용 프로그램이 있는 경우 해당 응용 프로그램을 다시 구성하여 Azure AD와 함께 SSO를 사용해야 합니다. Azure AD에서 지원되지 않는 드문 구성으로 AD FS에 구성된 응용 프로그램이 있는 경우 앱 소유자에게 문의하여 특수 구성이 응용 프로그램의 절대 요구 사항인지 확인해야 합니다. 필요하지 않은 경우 Azure AD에서 SSO를 사용하도록 응용 프로그램을 다시 구성해야 합니다.

![Azure AD를 기본 ID 공급자로](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS에 대한 Azure AD 연결 상태를](../hybrid/how-to-connect-health-adfs.md) 사용하여 Azure AD로 마이그레이션할 수 있는 각 응용 프로그램에 대한 구성 세부 정보를 수집할 수 있습니다.

### <a name="assign-users-to-applications"></a>응용 프로그램에 사용자 할당

[사용자를 응용 프로그램에 할당하는](../manage-apps/assign-user-or-group-access-portal.md) 것은 대규모로 관리할 수 있는 유연성과 기능을 더 잘 허용하므로 그룹을 사용하여 매핑하는 것이 가장 좋습니다. 그룹을 사용하면 특성 [기반 동적 그룹 구성원 자격](../users-groups-roles/groups-dynamic-membership.md) 및 앱 [소유자에 대한 위임이](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)있습니다. 따라서 이미 그룹을 사용하고 관리하는 경우 대규모 관리를 개선하기 위해 다음 작업을 수행하는 것이 좋습니다.

- 그룹 관리 및 거버넌스를 응용 프로그램 소유자에게 위임합니다.
- 응용 프로그램에 대한 셀프 서비스 액세스를 허용합니다.
- 사용자 특성이 응용 프로그램에 대한 액세스를 일관되게 결정할 수 있는 경우 동적 그룹을 정의합니다.
- Azure AD 액세스 검토를 사용하여 응용 프로그램 액세스에 사용되는 그룹에 [증명을 구현합니다.](../governance/access-reviews-overview.md)

반면에 개별 사용자에게 할당된 응용 프로그램을 찾으면 해당 응용 프로그램 과 관련된 [거버넌스를](https://docs.microsoft.com/azure/active-directory/governance/index) 구현해야 합니다.

#### <a name="assign-users-to-applications-recommended-reading"></a>권장 읽기 응용 프로그램에 사용자 할당

- [Azure Active Directory에서 애플리케이션에 사용자 및 그룹 할당](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Azure Active Directory에서 앱 등록 권한 위임](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Azure Active Directory의 그룹에 대한 동적 멤버 자격 규칙](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>액세스 정책

### <a name="named-locations"></a>명명된 위치

Azure AD의 [명명된 위치를](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) 사용하면 조직의 신뢰할 수 있는 IP 주소 범위에 레이블을 지정할 수 있습니다. Azure AD는 명명된 위치를 사용하여 다음 작업을 수행합니다.

- 위험 이벤트에서 거짓 긍정을 방지합니다. 신뢰할 수 있는 네트워크 위치에서 로그인하면 사용자의 로그인 위험이 낮아집니다.
- [위치 기반 조건부 액세스를](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)구성합니다.

![명명된 위치](./media/active-directory-ops-guide/active-directory-ops-img10.png)

우선 순위에 따라 아래 표를 사용하여 조직의 요구 사항에 가장 적합한 권장 솔루션을 찾습니다.

| **Priority** | **시나리오** | **권장** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | PHS 또는 PTA를 사용하고 명명된 위치가 정의되지 않은 경우 | 위험 이벤트 탐지를 개선하기 위해 명명된 위치 정의 |
| 2 | 페더레이션되어 있고 "insideCorporateNetwork" 클레임을 사용하지 않고 명명된 위치가 정의되지 않은 경우 | 위험 이벤트 탐지를 개선하기 위해 명명된 위치 정의 |
| 3 | 조건부 액세스 정책에서 명명된 위치를 사용하지 않고 조건부 액세스 정책에 위험 또는 장치 제어가 없는 경우 | 명명된 위치를 포함하도록 조건부 액세스 정책을 구성합니다. |
| 4 | 페더레이션되어 "insideCorporateNetwork" 클레임을 사용하고 명명된 위치가 정의되지 않은 경우 | 위험 이벤트 탐지를 개선하기 위해 명명된 위치 정의 |
| 5 | 명명된 위치가 아닌 MFA에서 신뢰할 수 있는 IP 주소를 사용하고 신뢰할 수 있는 IP 주소로 표시하는 경우 | 명명된 위치를 정의하고 이를 신뢰할 수 있는 위치로 표시하여 위험 이벤트 탐지를 개선합니다. |

### <a name="risk-based-access-policies"></a>위험 기반 액세스 정책

Azure AD는 모든 로그인 및 모든 사용자에 대한 위험을 계산할 수 있습니다. 액세스 정책의 기준으로 위험을 사용하면 인증 프롬프트가 줄어들고 보안이 향상되는 등 사용자 환경이 향상될 수 있습니다( 예: 필요할 때만 사용자에게 프롬프트하고 응답 및 수정을 자동화).

![로그인 위험 정책](./media/active-directory-ops-guide/active-directory-ops-img11.png)

액세스 정책에서 위험을 사용하는 것을 지원하는 Azure AD Premium P2 라이선스를 이미 소유하고 있지만 사용되지 않는 경우 보안 태세에 위험을 추가하는 것이 좋습니다.

#### <a name="risk-based-access-policies-recommended-reading"></a>위험 기반 액세스 정책 권장 읽기

- [방법: 로그인 위험 정책 구성](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [방법: 사용자 위험 정책 구성](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>클라이언트 응용 프로그램 액세스 정책

Microsoft Intune 응용 프로그램 관리(MAM)는 Outlook Mobile과 같은 호환 되는 클라이언트 모바일 응용 프로그램에 저장소 암호화, PIN, 원격 저장소 정리 등과 같은 데이터 보호 컨트롤을 푸시하는 기능을 제공합니다. 또한 승인되거나 호환되는 앱에서 Exchange Online과 같은 클라우드 서비스에 [대한 액세스를 제한하기](../conditional-access/app-based-conditional-access.md) 위해 조건부 액세스 정책을 만들 수 있습니다.

직원이 Exchange Online 또는 SharePoint Online과 같은 회사 리소스에 액세스하기 위해 Office 모바일 앱과 같은 MAM 지원 응용 프로그램을 설치하고 BYOD(자체 장치 가져오기)를 지원하는 경우 MDM 등록 없이 개인 소유 장치에서 응용 프로그램 구성을 관리하는 응용 프로그램 MAM 정책을 배포한 다음 조건부 액세스 정책을 업데이트하여 MAM 지원 클라이언트의 액세스만 허용하는 것이 좋습니다.

![조건부 액세스 권한 부여 제어](./media/active-directory-ops-guide/active-directory-ops-img12.png)

직원이 회사 리소스에 대해 MAM 지원 응용 프로그램을 설치하고 Intune Managed 장치에서 액세스가 제한되는 경우 응용 프로그램 MAM 정책을 배포하여 개인 장치에 대한 응용 프로그램 구성을 관리하고 MAM 지원 클라이언트의 액세스만 허용하도록 조건부 액세스 정책을 업데이트해야 합니다.

### <a name="conditional-access-implementation"></a>조건부 액세스 구현

조건부 액세스는 조직의 보안 상태를 개선하는 데 필수적인 도구입니다. 따라서 다음 모범 사례를 따르는 것이 중요합니다.

- 모든 SaaS 응용 프로그램에 하나 이상의 정책이 적용되어 있는지 확인합니다.
- 모든 **앱** 필터를 **블록** 컨트롤과 결합하여 잠금 위험을 방지하지 마십시오.
- **모든 사용자를** 필터로 사용하고 실수로 **게스트를** 추가하지 마십시오.
- **모든 "레거시" 정책을 Azure 포털로 마이그레이션**
- 사용자, 장치 및 응용 프로그램에 대한 모든 기준 을 파악
- 조건부 액세스 정책을 사용하여 사용자별 [MFA를](../conditional-access/plan-conditional-access.md)사용하는 대신 **MFA를 구현합니다.**
- 여러 응용 프로그램에 적용할 수 있는 작은 핵심 정책 집합이 있습니다.
- 빈 예외 그룹을 정의하고 예외 전략을 갖도록 정책에 추가합니다.
- MFA 컨트롤없이 유리 계정 [파손](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) 계획
- Office 365 클라이언트 응용 프로그램(예: Teams, 비즈니스용 OneDrive, Outlook 등)에서 일관된 환경을 보장합니다. Exchange Online 및 Sharepoint Online과 같은 서비스에 대해 동일한 제어 집합을 구현하여
- 정책에 대한 할당은 개인이 아닌 그룹을 통해 구현되어야 합니다.
- 정책에 사용된 예외 그룹을 정기적으로 검토하여 사용자가 보안 상태를 벗어나는 시간을 제한합니다. Azure AD P2를 소유하고 있는 경우 액세스 검토를 사용하여 프로세스를 자동화할 수 있습니다.

#### <a name="conditional-access-recommended-reading"></a>조건부 액세스 권장 읽기

- [Azure Active 디렉터리에서 조건부 액세스에 대한 모범 사례](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [ID 및 디바이스 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active 디렉터리 조건부 액세스 설정 참조](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [일반 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>접근 표면적

### <a name="legacy-authentication"></a>레거시 인증

MFA와 같은 강력한 자격 증명은 레거시 인증 프로토콜을 사용하여 앱을 보호할 수 없으므로 악의적인 행위자에 의해 선호되는 공격 벡터가 됩니다. 액세스 보안 태세를 개선하려면 레거시 인증을 잠그는 것이 중요합니다.

레거시 인증은 다음과 같은 앱에서 사용하는 인증 프로토콜을 참조하는 용어입니다.

- 최신 인증을 사용하지 않는 이전 Office 클라이언트(예: Office 2010 클라이언트)
- IMAP/SMTP/POP과 같은 메일 프로토콜을 사용하는 클라이언트

공격자는 이러한 프로토콜을 강력하게 선호합니다 - 사실, 암호 스프레이 공격의 거의 [100%는](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) 레거시 인증 프로토콜을 사용합니다! 해커는 다단계 인증 및 장치 인증과 같은 추가 보안 문제에 필요한 대화형 로그인을 지원하지 않기 때문에 레거시 인증 프로토콜을 사용합니다.

사용자 환경에서 레거시 인증이 널리 사용되는 경우 가능한 한 빨리 [최신 인증을](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 지원하는 클라이언트로 레거시 클라이언트를 마이그레이션할 계획입니다. 동일한 토큰에서 일부 사용자가 이미 최신 인증을 사용하고 있지만 레거시 인증을 여전히 사용하는 사용자가 있는 경우 다음 단계를 수행하여 레거시 인증 클라이언트를 잠급해야 합니다.

1. [로그인 활동 보고서를](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) 사용하여 레거시 인증을 여전히 사용하고 있는 사용자를 식별하고 수정 계획을 수립합니다.

   a. 영향을 받는 사용자에 게 현대 인증 가능한 클라이언트로 업그레이드 합니다.
   
   b. 아래 단계당 잠금을 수행하려면 컷오버 기간을 계획합니다.
   
   다. 레거시 인증에 대한 의존성이 어려운 레거시 응용 프로그램을 식별합니다. 아래 3단계를 참조하십시오.

2. 더 많은 노출을 피하기 위해 레거시 인증을 사용하지 않는 사용자를 위해 원본에서 레거시 프로토콜을 사용하지 않도록 설정합니다(예: Exchange 사서함).
3. 나머지 계정(서비스 계정과 같은 이상적으로 사람이 아닌 ID)의 경우 조건부 액세스를 사용하여 인증 후 [레거시 프로토콜을 제한합니다.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)

#### <a name="legacy-authentication-recommended-reading"></a>레거시 인증 권장 읽기

- [Exchange 서버의 사서함에 대한 POP3 또는 IMAP4 액세스 사용 또는 비활성화](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>동의 교부금

불법 동의 권한 부여 공격에서 공격자는 연락처 정보, 전자 메일 또는 문서와 같은 데이터에 대한 액세스를 요청하는 Azure AD 등록 응용 프로그램을 만듭니다. 사용자는 악성 웹 사이트에 착륙 할 때 피싱 공격을 통해 악성 응용 프로그램에 대한 동의를 부여 할 수 있습니다.

다음은 Microsoft 클라우드 서비스에 대해 자세히 조사할 수 있는 권한이 있는 앱 목록입니다.

- 앱 또는 위임된 \*앱. 읽기 쓰기 권한
- 위임된 권한이 있는 앱은 사용자를 대신하여 전자 메일을 읽거나 보내거나 관리할 수 있습니다.
- 다음 권한을 사용하여 부여된 앱:

| 리소스 | 사용 권한 |
| :- | :- |
| Office 365 Exchange Online | Eas. 액세스아유저.모두 |
| | Ews. 액세스아유저.모두 |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | 메일.읽기.공유 |
| | 메일.읽기 쓰기 |

- 앱은 로그인한 사용자의 전체 사용자 사칭을 허용했습니다. 예를 들어:

|리소스 | 사용 권한 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

이 시나리오를 방지하려면 Office [365에서 불법 동의 권한 부여를 검색하고 수정하여](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) 필요한 것보다 많은 보조금이 있는 불법 보조금 또는 응용 프로그램을 식별하고 수정해야 합니다. 다음으로 [셀프 서비스를 완전히 제거하고](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) [거버넌스 절차를 수립합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) 마지막으로, 앱 사용 권한에 대한 정기적인 검토를 예약하고 필요하지 않을 때 제거합니다.

#### <a name="consent-grants-recommended-reading"></a>동의 교부금 권장 읽기

- [마이크로소프트 그래프 API 권한](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>사용자 및 그룹 설정

다음은 명시적인 비즈니스 요구가 없는 경우 잠글 수 있는 사용자 및 그룹 설정입니다.

#### <a name="user-settings"></a>사용자 설정

- **외부 사용자** - 팀, Power BI, Sharepoint Online 및 Azure 정보 보호와 같은 서비스를 통해 기업에서 외부 공동 작업을 유기적으로 발생할 수 있습니다. 사용자가 시작한 외부 공동 작업을 제어하는 명시적 제약 조건이 있는 경우 [Azure AD 권한 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) 또는 헬프 데스크를 통한 제어된 작업을 사용하여 외부 사용자를 사용하도록 설정하는 것이 좋습니다. 서비스에 대한 유기적 외부 공동 작업을 허용하지 않으려면 [구성원이 외부 사용자를 완전히 초대하지 못하도록 차단할](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations)수 있습니다. 또는 외부 사용자 초대에서 [특정 도메인을 허용하거나 차단할](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) 수도 있습니다.
- **앱 등록** - 앱 등록이 활성화되면 최종 사용자는 응용 프로그램을 자체적으로 온보보드하고 데이터에 대한 액세스 권한을 부여할 수 있습니다. 앱 등록의 일반적인 예로는 Outlook 플러그인또는 Alexa 및 Siri와 같은 음성 비서가 이메일과 캘린더를 읽거나 대신 전자 메일을 보낼 수 있도록 하는 사용자가 있습니다. 고객이 앱 등록을 해제하기로 결정한 경우 InfoSec 및 IAM 팀은 관리자 계정으로 응용 프로그램을 등록해야 하므로 예외(비즈니스 요구 사항에 따라 필요한 앱 등록)의 관리에 참여해야 하며 프로세스를 운영하기 위한 프로세스를 설계해야 할 가능성이 큽니다.
- **관리 포털** - 조직은 Azure 포털에서 Azure AD 블레이드를 잠그면 관리자가 Azure 포털에서 Azure AD 관리에 액세스할 수 없고 혼동할 수 없습니다. Azure AD 관리 포털의 사용자 설정으로 이동하여 액세스를 제한합니다.

![관리 포털 제한 액세스](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 관리자가 아닌 사용자는 명령줄 및 기타 프로그래밍 인터페이스를 통해 Azure AD 관리 인터페이스에 계속 액세스할 수 있습니다.

#### <a name="group-settings"></a>그룹 설정

**셀프 서비스 그룹 관리 / 사용자는 보안 그룹 / O365 그룹을 만들 수 있습니다.** 클라우드에 그룹에 대한 현재 셀프 서비스 이니셔티브가 없는 경우 고객은 이 기능을 사용할 준비가 될 때까지 이 기능을 해제할 수 있습니다.

#### <a name="groups-recommended-reading"></a>그룹 권장 읽기

- [Azure Active Directory B2B 협업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [응용 프로그램을 Azure Active 디렉터리와 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Azure Active 디렉터리에서 앱, 사용 권한 및 동의.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [그룹을 사용하여 Azure Active Directory의 리소스에 대한 액세스를 관리합니다.](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Azure Active Directory에서 셀프 서비스 응용 프로그램 액세스 관리 설정](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>예기치 않은 위치에서의 트래픽

공격자는 세계의 여러 부분에서 유래. 위치가 있는 조건부 액세스 정책을 조건으로 사용하여 이 위험을 관리합니다. 조건부 액세스 정책의 [위치 조건을](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) 사용하면 비즈니스 이유가 없는 위치에 대한 액세스를 차단할 수 있습니다.

![새 명명된 위치 만들기](./media/active-directory-ops-guide/active-directory-ops-img14.png)

가능한 경우 SIEM(보안 정보 및 이벤트 관리) 솔루션을 사용하여 지역 간 액세스 패턴을 분석하고 찾을 수 있습니다. SIEM 제품을 사용하지 않거나 Azure AD에서 인증 정보를 수집하지 않는 경우 [Azure Monitor를](https://docs.microsoft.com/azure/azure-monitor/overview) 사용하여 지역 간 액세스 패턴을 식별하는 것이 좋습니다.

## <a name="access-usage"></a>액세스 사용

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 로그가 보관되고 인시던트 대응 계획과 통합

Azure AD에 대한 로그인 활동, 감사 및 위험 이벤트에 액세스하는 것은 문제 해결, 사용 분석 및 포렌식 조사에 매우 중요합니다. Azure AD는 보존 기간이 제한된 REST API를 통해 이러한 원본에 대한 액세스를 제공합니다. 보안 정보 및 이벤트 관리(SIEM) 시스템 또는 이에 상응하는 보관 기술은 감사 및 지원 가능성을 장기적으로 저장하기 위한 핵심입니다. Azure AD 로그를 장기 저장하려면 기존 SIEM 솔루션에 추가하거나 Azure [Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)를 사용해야 합니다. 인시던트 대응 계획 및 조사의 일부로 사용할 수 있는 로그를 보관합니다.

#### <a name="logs-recommended-reading"></a>로그 권장 읽기

- [Azure Active Directory 감사 API 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Azure Active Directory 로그인 활동 보고서 API 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Azure 활성 디렉터리 ID 보호를 위한 Microsoft 그래프](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 관리 활동 API 참조](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Azure Active 디렉터리 Power BI 콘텐츠 팩 사용 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>요약

보안 ID 인프라에는 12가지 측면이 있습니다. 이 목록은 자격 증명을 더욱 안전하게 보호하고 관리하고, 인증 환경을 정의하고, 할당을 위임하고, 사용량을 측정하고, 엔터프라이즈 보안 태세를 기반으로 액세스 정책을 정의하는 데 도움이 됩니다.

- 주요 작업에 소유자를 할당합니다.
- 약하거나 유출된 암호를 감지하고, 암호 관리 및 보호를 개선하며, 리소스에 대한 사용자 액세스를 더욱 안전하게 보호하는 솔루션을 구현합니다.
- 장치의 ID를 관리하여 언제 어디서나 리소스를 보호합니다.
- 암호 없는 인증을 구현합니다.
- 조직 전체에 표준화된 단일 사인온 메커니즘을 제공합니다.
- AD FS에서 Azure AD로 앱을 마이그레이션하여 더 나은 보안과 보다 일관된 관리 용이성을 구현합니다.
- 그룹을 사용하여 사용자를 응용 프로그램에 할당하여 더 큰 유연성과 대규모 관리 기능을 허용합니다.
- 위험 기반 액세스 정책을 구성합니다.
- 레거시 인증 프로토콜을 잠급합니다.
- 불법 적인 동의 부여를 감지 하고 해결 합니다.
- 사용자 및 그룹 설정을 잠급니다.
- 문제 해결, 사용 분석 및 포렌식 조사를 위해 Azure AD 로그의 장기 저장소를 활성화합니다.

## <a name="next-steps"></a>다음 단계

ID 거버넌스 [운영 검사 및 작업을](active-directory-ops-guide-govern.md)시작하십시오.
