---
title: Azure Security Control - ID 및 액세스 제어
description: Azure Security Control ID 및 액세스 제어
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dda3dcd3cd1234b2d0830010297e760201ed6160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549280"
---
# <a name="security-control-identity-and-access-control"></a>Security Control: ID 및 액세스 제어

ID 및 액세스 관리 권장 사항은 ID 기반 액세스 제어 관련 문제를 해결하고 관리 액세스를 잠그고 ID 관련 이벤트, 비정상 계정 동작, 역할 기반 액세스 제어를 경고하는 데 중점을 둡니다.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD에는 명시적으로 할당되고 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 통해 임시 쿼리를 수행하여 관리 그룹의 멤버인 계정을 검색합니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD에는 기본 암호 개념이 없습니다. 암호를 요구하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이(서비스에 따라 다름)를 준수하여 암호를 만들도록 강제합니다. 타사 애플리케이션 및 마켓플레이스 서비스의 경우 재량에 따라 기본 암호를 사용할 수 있습니다.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.3 | 4.3 | Customer |

전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 Microsoft 서비스용 Azure AD Privileged Identity Management의 권한 있는 역할과 Azure Resource Manager를 사용하여 Just-In-Time/Just-Enough-Access를 사용 설정할 수 있습니다. 

- [Privileged Identity Management에 대해 자세히 알아보기](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.4 | 4.4. | Customer |

가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure Active Directory SSO를 사용합니다. Azure Security Center ID 및 액세스 관리 권장 사항을 사용합니다.

- [Azure AD를 사용하는 SSO의 이해](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | Customer |

Azure AD MFA를 사용하도록 설정하고 Azure Security Center ID 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Customer |

MFA가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure 리소스에 로그인하고 구성합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 MFA를 사용하도록 설정하는 방법](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.7 | 4.8, 4.9 | Customer |

Azure Active Directory 보안 보고서를 사용하여 환경에서 의심스럽거나 안전하지 않은 활동이 발생하면 로그 및 경고를 생성합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.8 | 11.7 | Customer |

조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Customer |

Azure Active Directory를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.10 | 16.9, 16.10 | Customer |

Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다. 

- [Azure AD 보고 이해](../../active-directory/reports-monitoring/index.yml)

- [Azure ID 액세스 검토를 사용하는 방법](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.11 | 16.12 | Customer |

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 소스에 액세스가 가능하므로 SIEM/모니터링 툴과 통합할 수 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.12 | 16.13 | Customer |

Azure AD 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 3.13 | 16 | Customer |

Microsoft에서 고객 데이터에 액세스해야 하는 지원 시나리오에서는 고객 Lockbox가 고객 데이터 액세스 요청을 검토, 승인 또는 거부할 수 있는 인터페이스를 제공합니다.

- [고객 Lockbox의 이해](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>다음 단계

- 다음 Security Control: [데이터 보호](security-control-data-protection.md)를 참조하세요.