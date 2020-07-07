---
title: Azure Active Directory를 사용 하 여 보안 id에 신속 하 게 대응
description: Azure AD 클라우드 기반 id를 사용 하 여 위협에 신속 하 게 대응
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713afb7b277fba65dc4c860e8bdd6b62b4e0147d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204950"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Azure AD를 사용하여 ID 보안에 신속하게 대응

특히 신속 하 게 응답 하 고 많은 서비스에 빠르게 액세스 해야 하는 경우 오늘날 전 세계에서 작업자를 보호 하는 것이 어려울 수 있습니다. 이 문서에서는 사용자가 소유 하는 라이선스 유형에 따라 Azure AD 기능을 배포 하는 순서를 식별 하 고 우선 순위를 지정 하는 데 사용할 수 있는 모든 작업을 간결 하 게 나열 하는 방법을 설명 합니다. Azure AD는 다양 한 기능을 제공 하 고 Id에 대 한 다양 한 보안 계층을 제공 하며 관련 된 기능을 탐색 하는 경우도 있습니다. 많은 조직이 이미 클라우드에 있거나 신속 하 게 클라우드로 이동 하 고 있습니다 .이 문서는 서비스를 신속 하 게 배포 하 여 기본 고려 사항으로 id를 보호할 수 있도록 하기 위한 것입니다. 

각 테이블은 사용자의 영향을 최소화 하 고 사용자 환경을 개선 하는 동시에 주 보안 공격 (위반 재생, 피싱 및 암호 스프레이)에서 관리자 및 사용자 id를 보호 하는 일관 된 보안 권장 사항을 제공 합니다. 

또한 관리자는이 지침을 통해 안전 하 고 보호 된 방식으로 SaaS 및 온-프레미스 응용 프로그램에 대 한 액세스를 구성할 수 있으며, 클라우드 또는 하이브리드 (동기화 된) id에 적용 되며 원격으로 또는 사무실에서 작업 하는 사용자에 게 적용 됩니다.

이 검사 목록은 다음 사항의 수행 방법 설명을 통해 중요한 권장 작업을 빠르게 배포하여 조직을 즉시 보호하는 데 도움이 됩니다.

- 자격 증명을 강화합니다.
- 공격 노출 영역을 줄입니다.
- 위협 응답을 자동화합니다.
- 클라우드 인텔리전스를 활용합니다.
- 최종 사용자 셀프 서비스를 사용하도록 설정합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 가이드에서는 Azure AD에서 클라우드 전용 또는 하이브리드 id가 이미 설정 되어 있다고 가정 합니다. Id 유형 선택에 대 한 도움말은 [Azure Active Directory 하이브리드 id 솔루션에 적합 한 인증 방법 선택](../hybrid/choose-ad-authn.md) 문서를 참조 하세요. 

## <a name="summary"></a>요약

보안 id 인프라에는 많은 측면이 있지만이 검사 목록은 사용자가 원격으로 작업할 수 있도록 하는 안전 하 고 안전한 id 인프라를 중심으로 설명 합니다. Id를 보호 하는 것은 보안 스토리의 일부 이며, 데이터, 응용 프로그램 및 장치를 보호 하는 것도 고려해 야 합니다.

### <a name="guidance-for-azure-ad-free-or-office-365-customers"></a>Azure AD Free 또는 Office 365 고객에 대 한 지침입니다.

사용자 id를 보호 하기 위해 고객이 사용 해야 하는 Azure AD Free 또는 Office 365 앱의 권장 사항은 다음과 같습니다. 다음 표에서는 다음 라이선스 구독의 주요 동작을 강조 합니다.

- Office 365 (O365 E1, E3, E5, F1, A1, A3, A5)
- Office 365 비즈니스 (Essentials, Business, Business Premium)
- Microsoft 365 (M365 Business, A1)
- Azure AD Free (Azure, Dynamics 365, Intune 및 Power Platform에 포함 됨)

| 권장 조치 | 세부 정보 |
| --- | --- |
| [보안 기본값 사용](concept-fundamentals-security-defaults.md) | MFA를 사용 하도록 설정 하 고 레거시 인증을 차단 하 여 모든 사용자 id 및 응용 프로그램 보호 |
| [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md) (하이브리드 id를 사용 하는 경우) | 인증에 중복성을 제공 하 고 보안을 향상 시킵니다 (스마트 잠금, IP 잠금 및 유출 된 자격 증명을 검색 하는 기능 포함). |
| [ADFS 스마트 잠금 사용](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (해당 하는 경우) | 악의적인 활동에서 엑스트라넷 계정 잠금이 발생 하지 않도록 사용자를 보호 합니다. |
| [Azure Active Directory 스마트 잠금 사용](../authentication/howto-password-smart-lockout.md) (관리 되는 id를 사용 하는 경우) | 스마트 잠금 기능을 사용 하면 사용자의 암호를 추측 하려는 잘못 된 행위자를 잠그거나 무차별 암호 대입 메서드를 사용 하 여 가져올 수 있습니다. |
| [응용 프로그램에 대 한 최종 사용자 동의를 사용 하지 않도록 설정](../manage-apps/configure-user-consent.md) | 관리자 동의 워크플로를 통해 관리자는 관리자 승인이 필요한 응용 프로그램에 대 한 액세스 권한을 부여 하 여 최종 사용자가 회사 데이터를 노출 하지 않도록 할 수 있습니다. 노출 영역을 줄이고 위험을 완화하는 데 도움이 되도록 향후 모든 사용자 동의 작업을 사용하지 않도록 설정는 것이 좋습니다. |
| [지원 되는 SaaS 응용 프로그램을 갤러리에서 Azure AD로 통합 하 고 Single sign-on을 사용 하도록 설정](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. 향상 된 사용자 환경 (SSO)을 통해 원격 및 안전 하 게 회사 SaaS 응용 프로그램에 대 한 액세스 제공 |
| [SaaS 응용 프로그램에서 사용자 프로비저닝 자동화 및 프로](../app-provisioning/user-provisioning.md) 비전 해제 (해당 하는 경우) | 사용자가 액세스 해야 하는 클라우드 (SaaS) 응용 프로그램에서 사용자 id 및 역할을 자동으로 만듭니다. 사용자 id를 만드는 것 외에도 자동 프로 비전에는 상태 또는 역할이 변경 되는 사용자 id를 유지 관리 및 제거 하 여 조직의 보안을 강화 하는 작업이 포함 됩니다. |
| [보안 하이브리드 액세스 사용: 기존 앱 배달 컨트롤러 및 네트워크를 사용 하 여 레거시 앱 보호 (해당 하는](../manage-apps/secure-hybrid-access.md) 경우) | 기존 응용 프로그램 제공 컨트롤러나 네트워크를 사용 하 여 Azure AD에 연결 하 여 온-프레미스 및 클라우드 레거시 인증 응용 프로그램을 게시 하 고 보호 합니다. |
| [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md) (클라우드 전용 계정에 적용 가능) | 이 기능을 통해 사용자가 장치 또는 응용 프로그램에 로그인 할 수 없는 경우 지원 센터에 대 한 지원 및 생산성 저하를 줄일 수 있습니다. |
| [가능한 경우 비전역 관리 역할 사용](../users-groups-roles/directory-assign-admin-roles.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Azure AD Premium 요금제 1 고객에 대 한 지침입니다.

다음 표는 다음 라이선스 구독의 주요 동작을 강조 하기 위한 것입니다.

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, A3, F1, F3)

| 권장 조치 | 세부 정보 |
| --- | --- |
| [사용자 등록 환경을 간소화 하기 위해 Azure MFA 및 SSPR에 대해 결합 된 등록 환경 사용](../authentication/howto-registration-mfa-sspr-combined.md) | 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정을 위해 하나의 공통된 환경에서 등록할 수 있도록 합니다. |
| [조직에 대 한 MFA 설정 구성](../authentication/howto-mfa-getstarted.md) | 계정이 multi-factor authentication을 사용 하 여 손상 되지 않도록 보호 합니다. |
| [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md) | 이 기능을 통해 사용자가 장치 또는 응용 프로그램에 로그인 할 수 없는 경우 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다. |
| [비밀 번호 쓰기 저장 구현](../authentication/tutorial-enable-sspr-writeback.md) (하이브리드 id를 사용 하는 경우) | 클라우드에서 암호 변경 내용을 온-프레미스 Windows Server Active Directory 환경에 다시 쓸 수 있도록 합니다. |
| 조건부 액세스 정책 만들기 및 사용 | [관리 권한이 할당 된 계정을 보호 하는 관리자 용 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [레거시 인증 프로토콜과 관련 된 위험이 증가 하 여 레거시 인증 프로토콜을 차단 합니다.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [사용자 및 응용 프로그램의 보안을 유지 하기 위해 모든 사용자 및 응용 프로그램에 대 한 MFA로, 사용자 및 응용 프로그램의 보안을 유지 합니다.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Azure 리소스에 액세스 하는 모든 사용자에 대해 multi-factor authentication을 요구 하 여 권한 있는 리소스를 보호 하려면 Azure 관리를 위한 MFA를 요구 합니다.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md) (하이브리드 id를 사용 하는 경우) | 인증에 중복성을 제공 하 고 보안을 향상 시킵니다 (스마트 잠금, IP 잠금 및 유출 된 자격 증명을 검색 하는 기능 포함). |
| [ADFS 스마트 잠금 사용](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (해당 하는 경우) | 악의적인 활동에서 엑스트라넷 계정 잠금이 발생 하지 않도록 사용자를 보호 합니다. |
| [Azure Active Directory 스마트 잠금 사용](../authentication/howto-password-smart-lockout.md) (관리 되는 id를 사용 하는 경우) | 스마트 잠금 기능을 사용 하면 사용자의 암호를 추측 하려는 잘못 된 행위자를 잠그거나 무차별 암호 대입 메서드를 사용 하 여 가져올 수 있습니다. |
| [응용 프로그램에 대 한 최종 사용자 동의를 사용 하지 않도록 설정](../manage-apps/configure-user-consent.md) | 관리자 동의 워크플로를 통해 관리자는 관리자 승인이 필요한 응용 프로그램에 대 한 액세스 권한을 부여 하 여 최종 사용자가 회사 데이터를 노출 하지 않도록 할 수 있습니다. 노출 영역을 줄이고 위험을 완화하는 데 도움이 되도록 향후 모든 사용자 동의 작업을 사용하지 않도록 설정는 것이 좋습니다. |
| [응용 프로그램 프록시를 사용 하 여 온-프레미스 레거시 응용 프로그램에 대 한 원격 액세스 사용](../manage-apps/application-proxy-add-on-premises-application.md) | Azure AD 응용 프로그램 프록시을 사용 하도록 설정 하 고 사용자가 Azure AD 계정으로 로그인 하 여 온-프레미스 응용 프로그램에 안전 하 게 액세스할 수 있도록 레거시 앱과 통합 합니다. |
| [보안 하이브리드 액세스 사용: 기존 앱 배달 컨트롤러 및 네트워크를 사용 하 여 레거시 앱을 보호](../manage-apps/secure-hybrid-access.md) 합니다 (해당 하는 경우). | 기존 응용 프로그램 제공 컨트롤러나 네트워크를 사용 하 여 Azure AD에 연결 하 여 온-프레미스 및 클라우드 레거시 인증 응용 프로그램을 게시 하 고 보호 합니다. |
| [지원 되는 SaaS 응용 프로그램을 갤러리에서 Azure AD로 통합 하 고 Single sign-on을 사용 하도록 설정](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. 향상 된 사용자 환경 (SSO)을 통해 회사 SaaS 응용 프로그램에 원격으로 안전 하 게 액세스를 제공 합니다. |
| [SaaS 응용 프로그램에서 사용자 프로비저닝 자동화 및 프로](../app-provisioning/user-provisioning.md) 비전 해제 (해당 하는 경우) | 사용자가 액세스 해야 하는 클라우드 (SaaS) 응용 프로그램에서 사용자 id 및 역할을 자동으로 만듭니다. 사용자 id를 만드는 것 외에도 자동 프로 비전에는 상태 또는 역할이 변경 되는 사용자 id를 유지 관리 및 제거 하 여 조직의 보안을 강화 하는 작업이 포함 됩니다. |
| [조건부 액세스 사용-장치 기반](../conditional-access/require-managed-devices.md) | 장치 기반 조건부 액세스를 사용 하 여 보안 및 사용자 환경을 개선 합니다. 이 단계를 통해 사용자는 보안 및 규정 준수에 대 한 표준을 충족 하는 장치 에서만 액세스할 수 있습니다. 이러한 디바이스는 관리 디바이스라고도 합니다. 관리 되는 장치는 Intune 규격 또는 하이브리드 Azure AD 조인 장치 일 수 있습니다. |
| [암호 보호 사용](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 사용자가 취약 하 고 추측 하기 쉬운 암호를 사용 하는 것을 방지 합니다. |
| [둘 이상의 전역 관리자 지정](../users-groups-roles/directory-emergency-access.md) | 비상 시 사용하기 위해 둘 이상의 클라우드 전용 영구 전역 관리자 계정을 할당합니다. 이러한 계정은 매일 사용되지는 않으며 길고 복잡한 암호가 있어야 합니다. 투명 계정은 응급 상황에서 서비스에 액세스할 수 있도록 합니다. |
| [가능한 경우 비전역 관리 역할 사용](../users-groups-roles/directory-assign-admin-roles.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. |
| [게스트 사용자 액세스에 대한 계획 만들기](../b2b/what-is-b2b.md) | 자신의 회사, 학교 또는 소셜 id를 사용 하 여 앱 및 서비스에 로그인 하도록 허용 하 여 게스트 사용자와 공동 작업 합니다. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Azure AD Premium 계획 2 고객에 대 한 지침입니다.

다음 표는 다음 라이선스 구독의 주요 동작을 강조 하기 위한 것입니다.

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| 권장 조치 | 세부 정보 |
| --- | --- |
| [사용자 등록 환경을 간소화 하기 위해 Azure MFA 및 SSPR에 대해 결합 된 등록 환경 사용](../authentication/howto-registration-mfa-sspr-combined.md) | 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정을 위해 하나의 공통된 환경에서 등록할 수 있도록 합니다. |
| [조직에 대 한 MFA 설정 구성](../authentication/howto-mfa-getstarted.md) | 계정이 multi-factor authentication을 사용 하 여 손상 되지 않도록 보호 합니다. |
| [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md) | 이 기능을 통해 사용자가 장치 또는 응용 프로그램에 로그인 할 수 없는 경우 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다. |
| [비밀 번호 쓰기 저장 구현](../authentication/tutorial-enable-sspr-writeback.md) (하이브리드 id를 사용 하는 경우) | 클라우드에서 암호 변경 내용을 온-프레미스 Windows Server Active Directory 환경에 다시 쓸 수 있도록 합니다. |
| [Id 보호 정책을 사용 하도록 설정 하 여 MFA 등록 적용](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | MFA (Azure Multi-Factor Authentication의 롤아웃)를 관리 합니다. |
| [Id 보호 사용자 및 로그인 위험 정책 사용](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Id 보호 사용자 및 로그인 정책을 사용 하도록 설정 합니다. 권장 되는 로그인 정책은 중간 위험 로그인을 대상으로 하 고 MFA를 요구 하는 것입니다. 사용자 정책의 경우 암호 변경 작업을 필요로 하는 높은 위험 사용자를 대상으로 해야 합니다. |
| 조건부 액세스 정책 만들기 및 사용 | [관리 권한이 할당 된 계정을 보호 하는 관리자 용 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [레거시 인증 프로토콜과 관련 된 위험이 증가 하 여 레거시 인증 프로토콜을 차단 합니다.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Azure 리소스에 액세스 하는 모든 사용자에 대해 multi-factor authentication을 요구 하 여 권한 있는 리소스를 보호 하려면 Azure 관리를 위한 MFA를 요구 합니다.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md) (하이브리드 id를 사용 하는 경우) | 인증에 중복성을 제공 하 고 보안을 향상 시킵니다 (스마트 잠금, IP 잠금 및 유출 된 자격 증명을 검색 하는 기능 포함). |
| [ADFS 스마트 잠금 사용](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (해당 하는 경우) | 악의적인 활동에서 엑스트라넷 계정 잠금이 발생 하지 않도록 사용자를 보호 합니다. |
| [Azure Active Directory 스마트 잠금 사용](../authentication/howto-password-smart-lockout.md) (관리 되는 id를 사용 하는 경우) | 스마트 잠금 기능을 사용 하면 사용자의 암호를 추측 하려는 잘못 된 행위자를 잠그거나 무차별 암호 대입 메서드를 사용 하 여 가져올 수 있습니다. |
| [응용 프로그램에 대 한 최종 사용자 동의를 사용 하지 않도록 설정](../manage-apps/configure-user-consent.md) | 관리자 동의 워크플로를 통해 관리자는 관리자 승인이 필요한 응용 프로그램에 대 한 액세스 권한을 부여 하 여 최종 사용자가 회사 데이터를 노출 하지 않도록 할 수 있습니다. 노출 영역을 줄이고 위험을 완화하는 데 도움이 되도록 향후 모든 사용자 동의 작업을 사용하지 않도록 설정는 것이 좋습니다. |
| [응용 프로그램 프록시를 사용 하 여 온-프레미스 레거시 응용 프로그램에 대 한 원격 액세스 사용](../manage-apps/application-proxy-add-on-premises-application.md) | Azure AD 응용 프로그램 프록시을 사용 하도록 설정 하 고 사용자가 Azure AD 계정으로 로그인 하 여 온-프레미스 응용 프로그램에 안전 하 게 액세스할 수 있도록 레거시 앱과 통합 합니다. |
| [보안 하이브리드 액세스 사용: 기존 앱 배달 컨트롤러 및 네트워크를 사용 하 여 레거시 앱을 보호](../manage-apps/secure-hybrid-access.md) 합니다 (해당 하는 경우). | 기존 응용 프로그램 제공 컨트롤러나 네트워크를 사용 하 여 Azure AD에 연결 하 여 온-프레미스 및 클라우드 레거시 인증 응용 프로그램을 게시 하 고 보호 합니다. |
| [지원 되는 SaaS 응용 프로그램을 갤러리에서 Azure AD로 통합 하 고 Single sign-on을 사용 하도록 설정](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. 향상 된 사용자 환경 (SSO)을 통해 회사 SaaS 응용 프로그램에 원격으로 안전 하 게 액세스를 제공 합니다. |
| [SaaS 응용 프로그램에서 사용자 프로비저닝 자동화 및 프로](../app-provisioning/user-provisioning.md) 비전 해제 (해당 하는 경우) | 사용자가 액세스 해야 하는 클라우드 (SaaS) 응용 프로그램에서 사용자 id 및 역할을 자동으로 만듭니다. 사용자 id를 만드는 것 외에도 자동 프로 비전에는 상태 또는 역할이 변경 되는 사용자 id를 유지 관리 및 제거 하 여 조직의 보안을 강화 하는 작업이 포함 됩니다. |
| [조건부 액세스 사용-장치 기반](../conditional-access/require-managed-devices.md) | 장치 기반 조건부 액세스를 사용 하 여 보안 및 사용자 환경을 개선 합니다. 이 단계를 통해 사용자는 보안 및 규정 준수에 대 한 표준을 충족 하는 장치 에서만 액세스할 수 있습니다. 이러한 디바이스는 관리 디바이스라고도 합니다. 관리 되는 장치는 Intune 규격 또는 하이브리드 Azure AD 조인 장치 일 수 있습니다. |
| [암호 보호 사용](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 사용자가 취약 하 고 추측 하기 쉬운 암호를 사용 하는 것을 방지 합니다. |
| [둘 이상의 전역 관리자 지정](../users-groups-roles/directory-emergency-access.md) | 비상 시 사용하기 위해 둘 이상의 클라우드 전용 영구 전역 관리자 계정을 할당합니다. 이러한 계정은 매일 사용되지는 않으며 길고 복잡한 암호가 있어야 합니다. 투명 계정은 응급 상황에서 서비스에 액세스할 수 있도록 합니다. |
| [가능한 경우 비전역 관리 역할 사용](../users-groups-roles/directory-assign-admin-roles.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. |
| [게스트 사용자 액세스에 대한 계획 만들기](../b2b/what-is-b2b.md) | 자신의 회사, 학교 또는 소셜 id를 사용 하 여 앱 및 서비스에 로그인 하도록 허용 하 여 게스트 사용자와 공동 작업 합니다. |
| [Privileged Identity Management 사용](../privileged-identity-management/pim-configure.md) | 조직에서 중요 한 리소스에 대 한 액세스를 관리, 제어 및 모니터링할 수 있으므로 관리자가 필요한 경우에만 액세스 권한을 보유 하 고 승인을 받을 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- Azure AD의 개별 기능에 대 한 자세한 배포 지침은 [AZURE ad 프로젝트 배포 계획](active-directory-deployment-plans.md)을 참조 하세요.

- 종단 간 Azure AD 배포 검사 목록에 대해서는 [Azure Active Directory 기능 배포 가이드](active-directory-deployment-checklist-p2.md) 문서를 참조 하세요.