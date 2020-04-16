---
title: Azure 보안 제어 - 보안 구성
description: Azure 보안 제어 보안 구성
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 981e13e772ee21e0e4a680e6d07b4ad4892a7cee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417060"
---
# <a name="security-control-secure-configuration"></a>보안 제어: 보안 구성

공격자가 취약한 서비스 및 설정을 악용하지 못하도록 Azure 리소스의 보안 구성을 설정, 구현 및 적극적으로 관리(추적, 보고, 수정)합니다.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.1 | 5.1 | Customer |

Azure Policy 별칭을 사용하여 Azure 리소스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure 정책 정의를 사용할 수도 있습니다.

또한 Azure 리소스 관리자는 JSON(JavaScript 개체 표기설정)에서 템플릿을 내보낼 수 있으며, 구성이 조직의 보안 요구 사항을 충족 /초과하는지 확인하기 위해 검토해야 합니다.

Azure 보안 센터의 권장 사항을 Azure 리소스에 대한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure 정책 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [자습서: 규정 준수를 적용하기 위한 정책 생성 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 포털의 템플릿으로 단일 및 다중 리소스 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [보안 권장 사항 - 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.2 | 5.1 | Customer |

Azure 보안 센터 권장 사항을 사용하여 모든 계산 리소스에 대한 보안 구성을 유지 관리합니다.  또한 사용자 지정 운영 체제 이미지 또는 Azure 자동화 상태 구성을 사용하여 조직에서 요구하는 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure 보안 센터 권장 사항을 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [보안 권장 사항 - 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure Automation 상태 구성 개요](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [VHD를 업로드하고 이를 사용하여 Azure에서 새 Windows VM을 만듭니다.](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.3 | 5.2 | Customer |

Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스 간에 보안 설정을 적용합니다.  또한 Azure 리소스 관리자 템플릿을 사용하여 조직에서 요구하는 Azure 리소스의 보안 구성을 유지할 수 있습니다. 

- [Azure Policy의 영향 파악](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [규정 준수를 시행하기 위한 정책 생성 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 리소스 관리자 템플릿 개요](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 안전한 운영 체제 구성 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.4 | 5.2 | Shared |

Azure 계산 리소스에 대한 취약성 평가를 수행하는 방법에 대한 Azure 보안 센터의 권장 사항을 따릅니다.  또한 Azure 리소스 관리자 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure 자동화 상태 구성을 사용하여 조직에서 요구하는 운영 체제의 보안 구성을 유지할 수 있습니다.   Azure 자동화 원하는 상태 구성과 결합된 Microsoft 가상 컴퓨터 템플릿은 보안 요구 사항을 충족하고 유지하는 데 도움이 될 수 있습니다. 

또한 Microsoft에서 게시한 Azure 마켓플레이스 가상 머신 이미지는 Microsoft에서 관리하고 유지 관리합니다. 

- [Azure 보안 센터 취약성 평가 권장 사항을 구현하는 방법](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Azure 리소스 관리자 템플릿에서 Azure 가상 컴퓨터를 만드는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure Automation 상태 구성 개요](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Azure 포털에서 Windows 가상 컴퓨터 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [VM 템플릿을 다운로드하는 방법에 대한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.5 | 5.3 | Customer |

Azure DevOps를 사용하여 사용자 지정 Azure 정책, Azure 리소스 관리자 템플릿 및 원하는 상태 구성 스크립트와 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 특정 사용자, 기본 제공 보안 그룹 또는 Azure Active Directory(Azure AD)에 정의된 그룹에 대한 권한을 부여하거나 거부할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 에 대해](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.6 | 5.3 | Customer |

사용자 지정 이미지를 사용하는 경우 RBAC(역할 기반 액세스 제어)를 사용하여 권한이 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 공유 이미지 갤러리를 사용하면 조직 내의 여러 사용자, 서비스 주체 또는 AD 그룹에게 이미지를 공유할 수 있습니다.  컨테이너 이미지의 경우 Azure 컨테이너 레지스트리에 저장하고 RBAC를 활용하여 권한이 있는 사용자만 이미지에 액세스할 수 있도록 합니다.  

- [Azure에서 RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [컨테이너 레지스트리에 대한 RBAC 이해](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Azure에서 RBAC를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [공유 이미지 갤러리 개요](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대한 구성 관리 도구 배포

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.7 | 5.4 | Customer |

Azure 정책을 사용하여 Azure 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Policy 별칭을 사용하여 Azure 리소스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련된 기본 제공 정책 정의를 사용할 수도 있습니다.  또한 Azure 자동화를 사용하여 구성 변경 내용을 배포할 수 있습니다.

- [Azure 정책을 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [별칭 사용 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.8 | 5.4 | Customer |

Azure 자동화 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터의 원하는 상태 구성(DSC) 노드에 대한 구성 관리 서비스입니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. 

- [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.9 | 5.5 | Customer |

Azure 보안 센터를 사용하여 Azure 리소스에 대한 기준 검사를 수행합니다.  또한 Azure 정책을 사용하여 Azure 리소스 구성에 대해 경고하고 감사합니다.

- [Azure 보안 센터에서 권장 사항을 수정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.10 | 5.5 | Customer |

Azure 보안 센터를 사용하여 컨테이너에 대한 OS 및 도커 설정에 대한 기준 검사를 수행합니다.

- [Azure Security Center 컨테이너 권장 사항 이해](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.11 | 13.1 | Customer |

Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 클라우드 응용 프로그램에 대한 비밀 관리를 단순화하고 보호합니다.

- [Azure 관리 ID와 통합하는 방법](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [키 볼트를 만드는 방법](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [관리되는 ID로 키 볼트 인증을 제공하는 방법](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.12 | 4.1 | Customer |

관리되는 ID를 사용하여 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 키 볼트를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [관리되는 ID를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.13 | 18.1, 18.7 | Customer |

자격 증명 스캐너를 구현하여 코드 내의 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [맬웨어 방어](security-control-malware-defense.md) 보기