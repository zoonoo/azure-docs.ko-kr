---
title: Azure 보안 제어-보안 구성
description: 보안 제어 보안 구성
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564115"
---
# <a name="security-control-secure-configuration"></a>보안 제어: 보안 구성

공격자가 취약 한 서비스 및 설정을 악용 하지 못하도록 하기 위해 Azure 리소스의 보안 구성을 설정, 구현 및 적극적으로 관리 (추적, 보고, 수정) 합니다.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대 한 보안 구성을 설정 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.1 | 5.1 | Customer |

Azure Policy 또는 Azure Security Center를 사용 하 여 모든 Azure 리소스에 대 한 보안 구성을 유지 합니다.

Azure Policy를 구성 하 고 관리 하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성을 설정 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.2 | 5.1 | Customer |

Azure Security Center 권장 사항을 사용 하 여 Virtual Machines&quot;에서 보안 구성의 취약성을 수정 하 &quot;모든 계산 리소스에 대 한 보안 구성을 유지 합니다.

Azure Security Center 권장 사항을 모니터링 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center 권장 사항을 수정 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.3 | 5.2 | Customer |

Azure 정책 [거부] 및 [없는 경우 배포]를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.

Azure Policy를 구성 하 고 관리 하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 효과 이해:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.4 | 5.2 | 공유 |

Microsoft에서 기본 운영 체제 이미지를 관리 하 고 유지 관리 합니다.

그러나 Azure Resource Manager 템플릿 및/또는 필요한 상태 구성을 사용 하 여 조직에 필요한 보안 설정을 적용할 수 있습니다.

Azure Resource Manager 템플릿에서 Azure Virtual Machine을 만드는 방법:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Azure Virtual Machines에 대 한 필요한 상태 구성 이해:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전 하 게 저장

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.5 | 5.3 | Customer |

사용자 지정 Azure 정책 정의를 사용 하는 경우 Azure Azure DevOps 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

Azure DevOps에서 코드를 저장 하는 방법:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 설명서:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전 하 게 저장

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.6 | 5.3 | Customer |

사용자 지정 이미지를 사용 하는 경우 RBAC를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 컨테이너 이미지의 경우 Azure Container Registry에 저장 하 고 RBAC를 활용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

Azure의 RBAC 이해:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Container Registry에 대 한 RBAC 이해:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Azure에서 RBAC를 구성 하는 방법:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.7 | 5.4 | Customer |

Azure Policy를 사용 하 여 시스템 구성을 경고, 감사 및 적용 합니다. 또한 정책 예외를 관리 하는 프로세스와 파이프라인을 개발 합니다.

Azure Policy를 구성 하 고 관리 하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.8 | 5.4 | Customer |

PowerShell 필요한 상태 구성 (예: Windows 계산 또는 linux 용 Linux Chef 확장)과 같은 Azure 계산 확장을 사용 합니다.

Azure에 가상 머신 확장을 설치 하는 방법:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대 한 자동화 된 구성 모니터링 구현

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.9 | 5.5 | Customer |

Azure Security Center를 사용 하 여 Azure 리소스에 대 한 기준 검색을 수행 합니다.

Azure Security Center에서 권장 사항을 수정 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대 한 자동화 된 구성 모니터링 구현

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.1 | 5.5 | Customer |

Azure Security Center를 사용 하 여 컨테이너에 대 한 OS 및 Docker 설정에 대 한 기준 검색을 수행 합니다.

Azure Security Center 컨테이너 권장 사항 이해:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 암호를 안전 하 게 관리 

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.11 | 13.1 | Customer |

Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

Azure 관리 Id와 통합 하는 방법: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault를 만드는 방법: https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법: https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: id를 안전 하 게 자동으로 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.12 | 4.1 | Customer |

관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 관리 Id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

관리 Id를 구성 하는 방법: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도 하지 않은 자격 증명 노출을 제거 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.13 | 13.3 | Customer |

자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색 된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동 하는 것을 권장 합니다. 

자격 증명 스캐너를 설정 하는 방법: https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>다음 단계

다음 보안 제어를 참조 하세요. [맬웨어 방어](security-control-malware-defense.md)
