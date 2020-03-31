---
title: Azure 보안 제어 - ID 및 액세스 제어
description: 보안 제어 ID 및 액세스 제어
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934424"
---
# <a name="security-control-identity-and-access-control"></a>보안 제어: ID 및 액세스 제어

ID 및 액세스 관리 권장 사항은 ID 기반 액세스 제어와 관련된 문제를 해결하고, 관리 액세스를 잠그고, ID 관련 이벤트, 비정상적인 계정 동작 및 역할 기반 액세스 제어에 대해 경고하는 데 중점을 둡니다.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD에는 명시적으로 할당되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행하여 관리 그룹의 구성원인 계정을 검색합니다.

PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 얻는 방법:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 얻는 방법:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD에는 기본 암호의 개념이 없습니다. 암호가 필요한 다른 Azure 리소스는 서비스에 따라 달라보이는 복잡성 요구 사항및 최소 암호 길이로 암호를 만들어야 합니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 마켓플레이스 서비스에 대한 책임은 귀하에게 있습니다.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.3 | 4.3 | Customer |

전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure 보안 센터 ID 및 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 Microsoft 서비스 및 Azure 리소스 관리자에 대한 Azure AD 권한 ID 관리 권한 있는 역할을 사용하여 적시에 / 충분히 액세스하도록 설정할 수 있습니다. 

더 알아보세요:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 단일 사인온(SSO) 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.4 | 4.4. | Customer |

가능하면 서비스당 개별 독립 실행형 자격 증명을 구성하는 대신 Azure Active Directory SSO를 사용합니다. Azure 보안 센터 ID 및 액세스 관리 권장 사항을 사용합니다.

Azure AD를 통해 SSO 이해:

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | Customer |

Azure AD MFA를 사용하도록 설정하고 Azure 보안 센터 ID 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 활성화하는 방법:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure 보안 센터 내에서 ID 및 액세스를 모니터링하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Customer |

Azure 리소스에 로그인하고 구성하도록 구성된 MFA와 함께 PA(권한 있는 액세스 워크스테이션)를 사용합니다.

권한 있는 액세스 워크스테이션에 대해 자세히 알아보기:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 활성화하는 방법:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.7 | 4.8, 4.9 | Customer |

환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고 생성에 Azure Active Directory 보안 보고서를 사용합니다. Azure 보안 센터를 사용하여 ID 및 액세스 활동을 모니터링합니다.

위험한 활동에 플래그가 지정된 Azure AD 사용자를 식별하는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure 보안 센터에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.8 | 11.7 | Customer |

조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스할 수 있습니다.

Azure에서 명명된 위치를 구성하는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Azure 활성 디렉터리 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Customer |

Azure Active Directory(AAD)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. AAD는 미사용 및 전송 중 데이터에 대해 강력한 암호화를 사용하여 데이터를 보호합니다. 또한 AAD는 사용자 자격 증명을 염분, 해시 및 안전하게 저장합니다.

AAD 인스턴스를 만들고 구성하는 방법:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토하고 조정

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.10 | 16.9, 16.10 | Customer |

Azure AD는 오래된 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID Access Reviews를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스는 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

Azure 광고 보고:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID 액세스 검토를 사용하는 방법:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.11 | 16.12 | Customer |

AZURE AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으므로 SIEM/모니터링 도구와 통합할 수 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 로그 분석 작업 영역으로 전송하여 이 프로세스를 간소화할 수 있습니다. 로그 분석 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure 활동 로그를 Azure 모니터에 통합하는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.12 | 16.13 | Customer |

Azure AD 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수도 있습니다.

Azure AD 위험한 로그인을 보는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure 센티넬 온보보드 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 3.13 | 16 | Customer |

Microsoft가 고객 데이터에 액세스해야 하는 지원 시나리오에서 Customer Lockbox는 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다.

고객 잠금 상자 이해:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>다음 단계

다음 보안 제어 보기: [데이터 보호](security-control-data-protection.md)
