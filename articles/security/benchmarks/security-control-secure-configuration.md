---
title: Azure 보안 컨트롤 - 보안 구성
description: Azure 보안 컨트롤 보안 구성
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b13cc6f8bb50a3c9ec3cae519db387f9f32c950d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615281"
---
# <a name="security-control-secure-configuration"></a>보안 컨트롤: 보안 구성

공격자가 취약한 서비스 및 설정을 악용하지 못하도록 Azure 리소스의 보안 구성을 설정, 구현, 적극적으로 관리(추적, 보고, 수정)합니다.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.1 | 5.1 | Customer |

Azure Policy 별칭을 사용하여 Azure 리소스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

또한 Azure Resource Manager는 구성이 조직의 보안 요구 사항을 충족하거나 초과하는지 확인하기 위해 검토해야 하는 JSON(JavaScript Object Notation)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure Policy 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Portal에서 템플릿에 대한 단일 리소스 및 다중 리소스 내보내기](../../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.2 | 5.1 | Customer |

Azure Security Center 권장 사항을 사용하여 모든 컴퓨팅 리소스에 대한 보안 구성을 유지 관리합니다.  또한 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용하여 조직에 필요한 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure Security Center 권장 사항을 모니터링하는 방법](../../security-center/security-center-recommendations.md)

- [보안 권장 사항 - 참조 가이드](../../security-center/recommendations-reference.md)

- [Azure Automation State Configuration 개요](../../automation/automation-dsc-overview.md)

- [VHD를 업로드하고 사용하여 Azure에서 새 Windows VM 만들기](../../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.3 | 5.2 | Customer |

[거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.  또한 Azure Resource Manager 템플릿을 사용하여 조직에서 요구하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.4 | 5.2 | 공유됨 |

Azure 컴퓨팅 리소스에 대한 취약성 평가를 수행하는 Azure Security Center의 권장 사항을 따릅니다.  또한 Azure Resource Manager 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용하여 조직에 필요한 운영 체제의 보안 구성을 유지 관리할 수 있습니다.   Azure Automation Desired State Configuration과 결합된 Microsoft 가상 머신 템플릿은 보안 요구 사항을 충족하고 유지 관리하는 데 도움이 될 수 있습니다. 

Microsoft에서 게시하는 Azure Marketplace 가상 머신 이미지는 Microsoft에서 관리하고 유지관리합니다. 

- [Azure Security Center 취약성 평가 권장 사항을 구현하는 방법](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Resource Manager 템플릿에서 Azure 가상 머신을 만드는 방법](../../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration 개요](../../automation/automation-dsc-overview.md)

- [Azure Portal에서 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md)

- [VM 템플릿을 다운로드 방법에 대한 정보](/previous-versions/azure/virtual-machines/windows/download-template)

- [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.5 | 5.3 | Customer |

Azure DevOps를 사용하여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿, Desired State Configuration 스크립트와 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대한 권한을 부여하거나 거부하면 됩니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.6 | 5.3 | Customer |

사용자 지정 이미지를 사용하는 경우 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 공유 이미지 갤러리를 사용하면 조직 내의 여러 사용자, 서비스 주체 또는 AD 그룹에게 이미지를 공유할 수 있습니다.  컨테이너 이미지의 경우 Azure Container Registry에 저장하고 Azure RBAC를 활용하여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.  

- [Azure RBAC 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Container Registry용 Azure RBAC 이해](../../container-registry/container-registry-roles.md)

- [Azure RBAC를 구성하는 방법](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Shared Image Gallery 개요](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.7 | 5.4 | Customer |

Azure Policy를 사용하여 Azure 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Policy 별칭을 사용하여 Azure 리소스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련된 기본 제공 정책 정의도 사용할 수 있습니다.  또한 Azure Automation을 사용하여 구성 변경 내용을 배포할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용하는 방법](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.8 | 5.4 | Customer |

Azure Automation State Configuration은 클라우드나 온-프레미스 데이터 센터의 DSC(Desired State Configuration) 노드를 위한 구성 관리 서비스입니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. 

- [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.9 | 5.5 | Customer |

Azure Security Center를 사용하여 Azure 리소스에 대한 기준 검색을 수행합니다.  또한 Azure Policy를 사용하여 Azure 리소스 구성을 경고하고 감사합니다.

- [Azure Security Center에서 권장 사항을 수정하는 방법](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.10 | 5.5 | Customer |

Azure Security Center를 사용하여 OS에 대한 기준 검색사를 수행하고 컨테이너에 대한 Docker 설정을 수행합니다.

- [Azure Security Center 컨테이너 권장 사항 이해](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.11 | 13.1 | Customer |

Azure Key Vault와 함께 관리 서비스 ID를 사용하여 클라우드 애플리케이션에 대한 비밀 관리를 간소화하고 보호합니다.

- [Azure 관리 ID와 통합하는 방법](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault를 만드는 방법](../../key-vault/secrets/quick-create-portal.md)

- [Key Vault에 인증하는 방법](../../key-vault/general/authentication.md)

- [Key Vault 액세스 정책을 할당하는 방법](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.12 | 4.1 | Customer |

관리 ID를 사용하여 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [관리 ID를 구성하는 방법](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 7.13 | 18.1, 18.7 | Customer |

자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어 참조: [맬웨어 방어](security-control-malware-defense.md)