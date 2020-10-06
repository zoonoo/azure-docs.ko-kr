---
title: Azure 보안 벤치 마크 V2-권한 있는 액세스
description: Azure Security 벤치 마크 V2 권한 있는 액세스
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 68fdff9444286a7f304c3a3361ad33a02e87a282
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758373"
---
# <a name="security-control-v2-privileged-access"></a>보안 제어 V2: 권한 있는 액세스

권한 있는 액세스는 Azure 테 넌 트 및 리소스에 대 한 권한 있는 액세스를 보호 하는 컨트롤을 다룹니다. 여기에는 계획적이 고 실수로 인 한 위험 으로부터 관리 모델, 관리 계정 및 권한 있는 액세스 워크스테이션을 보호 하는 다양 한 컨트롤이 포함 됩니다.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: 권한이 높은 사용자를 보호 하 고 제한 합니다.

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-1 | 4.3, 4.8 | AC-2 |

권한 수준이 높은 사용자 계정 수를 제한 하 고 관리자 권한으로 이러한 계정을 보호 합니다. 이러한 두 역할에 할당 된 사용자는 관리자 역할을 위임할 수 있으므로 Azure AD의 가장 중요 한 기본 제공 역할은 전역 관리자 및 권한 있는 역할 관리자입니다. 이러한 권한을 통해 사용자는 Azure 환경의 모든 리소스를 직접 또는 간접적으로 읽고 수정할 수 있습니다.

- 전역 관리자/회사 관리자:이 역할의 사용자는 azure ad id를 사용 하는 서비스 뿐만 아니라 Azure AD의 모든 관리 기능에 액세스할 수 있습니다.

- 권한 있는 역할 관리자:이 역할의 사용자는 Azure AD 뿐만 아니라 Azure AD Privileged Identity Management (PIM) 내에서 역할 할당을 관리할 수 있습니다. 또한이 역할을 통해 PIM 및 관리 단위의 모든 측면을 관리할 수 있습니다.

참고: 권한 있는 특정 권한이 할당 된 사용자 지정 역할을 사용 하는 경우 관리 해야 하는 다른 중요 한 역할이 있을 수 있습니다. 또한 중요 한 비즈니스 자산의 관리자 계정에 비슷한 컨트롤을 적용할 수 있습니다.  

Azure AD Privileged Identity Management (PIM)를 사용 하 여 Azure 리소스 및 Azure AD에 대 한 JIT (just-in-time) 권한 있는 액세스를 사용 하도록 설정할 수 있습니다. JIT는 사용자가 필요한 경우에만 권한 있는 작업을 수행할 수 있는 임시 권한을 부여 합니다. 또한 PIM은 Azure AD 조직에 의심 스러운 또는 안전 하지 않은 활동이 있을 때 보안 경고를 생성할 수 있습니다.

- [Azure AD의 관리자 역할 권한](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Azure Privileged Identity Management 보안 경고 사용](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [보안 작업](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 업무상 중요 한 시스템에 대 한 관리 액세스 제한

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-2 | 13.2, 2.10 | AC-2, SC-3, SC-7 |

사용 중인 구독 및 관리 그룹에 대 한 권한 있는 액세스 권한이 부여 된 계정을 제한 하 여 업무상 중요 한 시스템에 대 한 액세스를 격리 합니다. 또한 비즈니스에 중요 한 시스템에 설치 된 에이전트를 사용 하 여 Dc (Active Directory 도메인 컨트롤러), 보안 도구 및 시스템 관리 도구와 같이 업무상 중요 한 자산에 대 한 관리 권한이 있는 관리, id 및 보안 시스템에 대 한 액세스를 제한 해야 합니다. 이러한 관리 및 보안 시스템을 손상 시키는 공격자는 비즈니스에 중요 한 자산을 손상 시키기 위해 즉시 weaponize 수 있습니다. 

모든 종류의 액세스 제어는 일관 된 액세스 제어를 보장 하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다. 

전자 메일, 탐색 및 생산성 작업에 사용 되는 표준 사용자 계정과는 다른 별도의 권한 있는 계정을 할당 해야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [관리 그룹 액세스](../../governance/management-groups/overview.md#management-group-access)

- [Azure 구독 관리자](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 사용자 액세스를 정기적으로 검토 및 조정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-3 | 4.1, 16.9, 16.10 | AC-2 |

사용자 계정 및 액세스 할당을 정기적으로 검토 하 여 계정 및 해당 액세스 수준이 유효한 지 확인 합니다. Azure AD 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 검토할 수 있습니다. Azure AD reporting은 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공할 수 있습니다. 또한 Azure AD Privileged Identity Management를 사용 하 여 검토 프로세스를 용이 하 게 하는 액세스 검토 보고서 워크플로를 만들 수 있습니다.
또한 Azure Privileged Identity Management은 과도 한 수의 관리자 계정이 생성 될 때 경고 하도록 구성 하 고, 부실 하거나 부적절 하 게 구성 된 관리자 계정을 식별할 수 있습니다. 

참고: 일부 Azure 서비스는 Azure AD를 통해 관리 되지 않는 로컬 사용자 및 역할을 지원 합니다. 이러한 사용자는 별도로 관리 해야 합니다.

- [Privileged Identity Management (PIM)에서 Azure 리소스 역할에 대 한 액세스 검토 만들기](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD에서 응급 액세스 설정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

실수로 Azure AD 조직에서 잠기는 것을 방지 하려면 일반 관리 계정을 사용할 수 없는 경우 액세스를 위한 응급 액세스 계정을 설정 합니다. 응급 액세스 계정은 일반적으로 매우 특권 이며 특정 사용자에 게 할당 되 면 안 됩니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다.
응급 액세스 계정에 대 한 자격 증명 (예: 암호, 인증서 또는 스마트 카드)을 안전 하 게 유지 하 고 비상 시에만 사용할 권한이 있는 개인 에게만 알려집니다.

- [Azure AD에서 응급 액세스 계정 관리](../../active-directory/users-groups-roles/directory-emergency-access.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [보안 작업 (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: 자격 관리 자동화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Azure AD 자격 관리 기능을 사용 하 여 액세스 권한 부여, 검토, 만료 등의 액세스 요청 워크플로를 자동화할 수 있습니다. 이중 또는 다단계 승인도 지원 됩니다.
- [Azure AD 액세스 검토 란?](../../active-directory/governance/access-reviews-overview.md) 

- [Azure AD 자격 관리 란?](../../active-directory/governance/entitlement-management-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-6 | 4.6, 11.6, 12.12 | AC-2, SC-3, SC-7 |

안전 하 고 격리 된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요 한 역할의 보안에 매우 중요 합니다. 관리 작업에는 매우 안전한 사용자 워크스테이션 및/또는 Azure 방호를 사용 합니다. Azure Active Directory, Microsoft Defender ATP (Advanced Threat Protection) 및/또는 Microsoft Intune를 사용 하 여 관리 작업을 위한 안전 하 고 관리 되는 사용자 워크스테이션을 배포할 수 있습니다. 보안 된 워크스테이션을 중앙에서 관리 하 여 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한 된 논리 및 네트워크 액세스를 비롯 한 보안 구성을 적용할 수 있습니다. 

- [권한 있는 액세스 워크스테이션 이해](../../active-directory/devices/concept-azure-managed-workstation.md)

- [권한 있는 액세스 워크스테이션 배포](../../active-directory/devices/howto-azure-managed-workstation.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [보안 작업 (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분 한 관리 수행 (최소 권한 원칙)

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-7 | 14.6 | AC-2, AC-3, SC-3 |

Azure RBAC (역할 기반 액세스 제어)를 통해 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 및 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다. Azure RBAC를 통해 리소스에 할당 하는 권한은 항상 역할에 필요한 것으로 제한 되어야 합니다. 제한 된 권한은 Azure AD Privileged Identity Management (PIM)의 JIT (just-in-time) 접근 방식을 보완 하며 이러한 권한은 정기적으로 검토 해야 합니다.
기본 제공 역할을 사용 하 여 사용 권한을 할당 하 고 필요한 경우에만 사용자 지정 역할을 만듭니다. 

- [Azure 역할 기반 access control (Azure RBAC) 이란?](../../role-based-access-control/overview.md)

- [Azure RBAC를 구성 하는 방법](../../role-based-access-control/role-assignments-portal.md)

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft 지원에 대 한 승인 프로세스 선택 

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

Microsoft에서 고객 데이터에 액세스 해야 하는 지원 시나리오에서는 각 고객 데이터 액세스 요청을 명시적으로 검토 하 고 승인 또는 거부할 수 있는 기능을 고객 Lockbox 제공 합니다.

- [고객 Lockbox 이해](../fundamentals/customer-lockbox-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Id 및 키 관리](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

