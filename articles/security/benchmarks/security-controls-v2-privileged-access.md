---
title: Azure 보안 벤치 마크 V2-권한 있는 액세스
description: Azure Security 벤치 마크 V2 권한 있는 액세스
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059344"
---
# <a name="security-control-privileged-access"></a>보안 제어: 권한 있는 액세스

권한 있는 액세스는 Azure 테 넌 트 및 리소스에 대 한 권한 있는 액세스를 보호 하는 컨트롤을 다룹니다. 여기에는 계획적이 고 실수로 인 한 위험 으로부터 관리 모델, 관리 계정 및 권한 있는 액세스 워크스테이션을 보호 하는 다양 한 컨트롤이 포함 됩니다.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: 전역 관리자를 보호 하 고 제한 합니다.

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-1 | 4.3, 4.8 | AC-2 |

전역 관리자 역할에 할당된 사용자는 Azure AD 조직의 모든 관리 설정을 읽고 수정할 수 있습니다. Azure 전역 관리자 계정의 수를 각 구독에 대해 2 개 이하로 제한 합니다. Azure AD의 가장 중요 한 기본 제공 역할은 전역 관리자이 고, 권한 있는 역할 관리자는 이러한 두 역할에 할당 된 사용자가 관리자 역할을 위임할 수 있습니다.
- 전역 관리자/회사 관리자:이 역할의 사용자는 azure ad id를 사용 하는 서비스 뿐만 아니라 Azure AD의 모든 관리 기능에 액세스할 수 있습니다.

- 권한 있는 역할 관리자:이 역할의 사용자는 Azure AD 뿐만 아니라 Azure AD Privileged Identity Management (PIM) 내에서 역할 할당을 관리할 수 있습니다. 또한이 역할을 통해 PIM 및 관리 단위의 모든 측면을 관리할 수 있습니다.

참고: 권한 있는 특정 권한이 할당 된 사용자 지정 역할을 사용 하는 경우 관리 해야 하는 다른 중요 한 역할이 있을 수 있습니다. 또한 중요 한 비즈니스 자산의 관리자 계정에 비슷한 컨트롤을 적용할 수 있습니다.  

Azure AD Privileged Identity Management (PIM)를 사용 하 여 Azure 리소스 및 Azure AD에 대 한 JIT (just-in-time) 권한 있는 액세스를 사용 하도록 설정할 수 있습니다. JIT는 사용자가 필요한 경우에만 권한 있는 작업을 수행할 수 있는 임시 권한을 부여 합니다. 또한 PIM은 Azure AD 조직에 의심 스러운 또는 안전 하지 않은 활동이 있을 때 보안 경고를 생성할 수 있습니다.

참고: azure SQL과 같은 일부 Azure 서비스는 Azure AD 인증 외에도 로컬 사용자 인증을 지원 합니다. 이 유형의 로컬 사용자 인증은 Azure AD를 통해 관리 되지 않습니다. 이러한 사용자는 별도로 관리 해야 합니다.

- [Azure AD의 관리자 역할 권한](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)

- [Azure Privileged Identity Management 보안 경고 사용](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Azure Security Center를 사용 하 여 id 및 액세스 모니터링](../../security-center/security-center-identity-access.md)

**책임**: Customer

**고객 보안 관련자**:

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: 사용자 액세스를 정기적으로 검토 및 조정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-2 | 4.1, 16.9, 16.10 | AC-2 |

사용자 계정 및 액세스 자격을 정기적으로 검토 하 여 사용자 계정 및 액세스 권한이 올바른지 확인 합니다. 

Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 관리할 수 있습니다. Azure AD reporting은 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공할 수 있습니다. 또한 Azure AD Privileged Identity Management를 사용 하 여 검토 프로세스를 용이 하 게 하는 액세스 검토 보고서 워크플로를 만들 수 있습니다.

Azure 서비스 및 워크 로드 수준에서 관리자의 경우 더 자주 수행 해야 하는 사용자 및 액세스 검토를 수행 해야 합니다. 또한 Azure Privileged Identity Management 보안 경고를 사용 하 여 관리자 계정이 생성 되는 시기를 검색 하 고, 부실 하거나 부적절 하 게 구성 된 관리자 계정을 찾을 수 있습니다. 

참고: azure SQL과 같은 일부 Azure 서비스는 Azure AD를 통해 관리 되지 않는 로컬 사용자를 지원 합니다. 이러한 사용자는 별도로 관리 해야 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Azure Security Center를 사용 하 여 id 및 액세스 모니터링](../../security-center/security-center-identity-access.md)

- [Azure Privileged Identity Management 보안 경고 사용](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [팀 간에 관리 책임 맞추기](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management-Azure AD 역할에 대 한 액세스 검토](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center-id 및 액세스 모니터링](../../security-center/security-center-identity-access.md)

**책임**: Customer

**고객 보안 관련자**:

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [파트너 규정 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: Azure AD에서 응급 액세스 계정 설정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

실수로 Azure AD 조직에서 잠기는 것을 방지 하려면 일반 관리 계정을 사용할 수 없는 경우 액세스를 위한 응급 액세스 계정을 설정 합니다. 응급 액세스 계정은 일반적으로 매우 특권 이며 특정 사용자에 게 할당 되 면 안 됩니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다.

응급 액세스 계정에 대 한 자격 증명 (예: 암호, 인증서 또는 스마트 카드)을 안전 하 게 유지 하 고 비상 시에만 사용할 권한이 있는 개인 에게만 알려집니다.

- [Azure AD에서 응급 액세스 계정 관리](../../active-directory/users-groups-roles/directory-emergency-access.md)

**책임**: Customer

**고객 보안 관련자**:

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [파트너 규정 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [SOC(보안 운영 센터)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: Azure id 및 액세스 요청 워크플로 자동화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-4 | 해당 없음 | AC-2, AC-5, PM-10 |

Azure AD 자격 관리 기능을 사용 하 여 액세스 할당, 검토 및 만료를 비롯 한 Azure 액세스 요청 워크플로를 자동화 합니다. 이중 또는 다단계 승인도 지원 됩니다.  

- [Azure AD 자격 관리 란?](../../active-directory/governance/entitlement-management-overview.md)

- [액세스 요청 및 승인 프로세스를 설정 하는 방법](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**책임**: Customer

**고객 보안 관련자**:

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [파트너 규정 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: 관리 작업에 매우 안전한 컴퓨터 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-5 | 4.6, 11.6, 12.12 | AC-2, SC-7 |

안전 하 고 격리 된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요 한 역할의 보안에 매우 중요 합니다. 관리 작업에는 매우 안전한 사용자 워크스테이션 및/또는 Azure 방호를 사용 합니다.
- Azure Active Directory, Microsoft Defender ATP (Advanced Threat Protection) 및/또는 Microsoft Intune를 사용 하 여 관리 작업을 위한 안전 하 고 관리 되는 사용자 워크스테이션을 배포할 수 있습니다. 보안 워크스테이션은 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한 된 논리적 및 네트워크 액세스를 포함 하 여 보안 구성을 적용 하도록 중앙에서 관리할 수 있습니다. 

- RDP 또는 SSH를 통해 가상 컴퓨터에 액세스 하는 안전한 경로를 위해 Azure 방호 기능을 사용 합니다. Azure 방호는 새 가상 네트워크를 만들지 않고 가상 네트워크 별로 프로 비전 할 수 있는 완전히 관리 되는 PaaS 서비스입니다. 

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../../active-directory/devices/concept-azure-managed-workstation.md)

- [안전 하 고 Azure로 관리 되는 워크스테이션 배포](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Azure 방호 호스트 사용](../../bastion/bastion-create-host-portal.md)

**책임**: Customer

**고객 보안 관련자**:

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [SOC(보안 운영 센터)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: Azure RBAC를 사용 하 여 리소스에 대 한 권한 할당

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-6 | 14.6 | AC-2, AC-3 |

Azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 역할 할당을 통해 Azure 리소스 액세스에 대 한 권한을 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대 한 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다. 

- [Azure 역할 기반 access control (Azure RBAC) 이란?](../../role-based-access-control/overview.md)

- [Azure에서 RBAC를 구성 하는 방법](../../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**고객 보안 관련자**:

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [파트너 규정 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: Microsoft 지원에 대 한 승인 프로세스 선택

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Microsoft에서 고객 데이터에 액세스 해야 하는 지원 시나리오에서는 각 고객 데이터 액세스 요청을 명시적으로 검토 하 고 승인 또는 거부할 수 있는 기능을 고객 Lockbox 제공 합니다.

- [고객 Lockbox 이해](../fundamentals/customer-lockbox-overview.md)

**책임**: Customer

**고객 보안 관련자**:

- [애플리케이션 보안 및 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [파트너 규정 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [ID 및 키](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

