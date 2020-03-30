---
title: Azure 보안 제어 - 보안 구성
description: 보안 제어 보안 구성
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934287"
---
# <a name="security-control-secure-configuration"></a>보안 제어: 보안 구성

공격자가 취약한 서비스 및 설정을 악용하지 못하도록 Azure 리소스의 보안 구성을 설정, 구현 및 적극적으로 관리(추적, 보고, 수정)합니다.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.1 | 5.1 | Customer |

Azure 정책 또는 Azure 보안 센터를 사용하여 모든 Azure 리소스에 대한 보안 구성을 유지 관리합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.2 | 5.1 | Customer |

가상 시스템의&quot; 보안 &quot;구성에서 Azure 보안 센터 권장 사항을 해결하여 모든 계산 리소스에서 보안 구성을 유지 관리합니다.

Azure 보안 센터 권장 사항을 모니터링하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure 보안 센터 권장 사항을 수정하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.3 | 5.2 | Customer |

Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스 간에 보안 설정을 적용합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책 효과 이해:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 안전한 운영 체제 구성 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.4 | 5.2 | Shared |

기본 운영 체제 이미지는 Microsoft에서 관리하고 유지 관리합니다.

그러나 Azure 리소스 관리자 템플릿 및/또는 원하는 상태 구성을 사용하여 조직에 필요한 보안 설정을 적용할 수 있습니다.

Azure 리소스 관리자 템플릿에서 Azure 가상 컴퓨터를 만드는 방법:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Azure 가상 시스템에 대한 원하는 상태 구성 이해:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.5 | 5.3 | Customer |

사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure 리포지토리를 사용하여 코드를 안전하게 저장하고 관리합니다.

Azure DevOps에 코드를 저장하는 방법:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 리포지토리 설명서:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.6 | 5.3 | Customer |

사용자 지정 이미지를 사용하는 경우 RBAC를 사용하여 권한이 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 컨테이너 이미지의 경우 Azure 컨테이너 레지스트리에 저장하고 RBAC를 활용하여 권한이 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

Azure에서 RBAC 이해:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

컨테이너 레지스트리에 대한 RBAC 이해:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Azure에서 RBAC를 구성하는 방법:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.7 | 5.4 | Customer |

Azure 정책을 사용하여 시스템 구성에 대해 경고, 감사 및 적용합니다. 또한 정책 예외를 관리하기 위한 프로세스 및 파이프라인을 개발합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.8 | 5.4 | Customer |

Windows 계산용 PowerShell 원하는 상태 구성 또는 Linux용 Linux Chef 확장과 같은 Azure 계산 확장을 사용합니다.

Azure에서 가상 컴퓨터 확장을 설치하는 방법:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.9 | 5.5 | Customer |

Azure 보안 센터를 사용하여 Azure 리소스에 대한 기준 선 검사를 수행합니다.

Azure 보안 센터에서 권장 사항을 수정하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.1 | 5.5 | Customer |

Azure 보안 센터를 사용하여 컨테이너에 대한 OS 및 도커 설정에 대한 기준 검사를 수행합니다.

Azure 보안 센터 컨테이너 권장 사항 이해:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리 

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.11 | 13.1 | Customer |

Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 클라우드 응용 프로그램에 대한 비밀 관리를 단순화하고 보호합니다.

Azure 관리 ID와 통합하는 방법:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

키 볼트를 만드는 방법:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리되는 ID로 키 볼트 인증을 제공하는 방법:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.12 | 4.1 | Customer |

관리되는 ID를 사용하여 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 키 볼트를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

관리되는 ID를 구성하는 방법:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 7.13 | 13.3 | Customer |

자격 증명 스캐너를 구현하여 코드 내의 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다. 

자격 증명 스캐너를 설정하는 방법:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>다음 단계

다음 보안 제어 보기: [맬웨어 방어](security-control-malware-defense.md)
