---
title: Azure 보안 제어 - 인벤토리 및 자산 관리
description: Azure 보안 제어 인벤토리 및 자산 관리
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3079ea475c20e3ae0e78319d6c6b24ee579fd0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521308"
---
# <a name="security-control-inventory-and-asset-management"></a>보안 그룹: 인벤토리 및 자산 관리

인벤토리 및 자산 관리 권장 사항은 권한 있는 리소스에만 액세스 권한을 부여하고 권한 없는 리소스 및 관리되지 않는 리소스를 식별하고 제거하도록 모든 Azure 리소스를 적극적으로 관리(인벤토리, 추적 및 수정)하는 것과 관련돤 문제를 해결하는 데 중점을 둡니다.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다.  테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.2 | 1.5 | Customer |

메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.3 | 1.6 | Customer |

해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 자산을 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.4 | 2.1 | Customer |

조직 요구 사항에 따라 승인된 Azure 리소스 및 컴퓨팅 리소스에 대한 승인된 소프트웨어의 인벤토리를 만듭니다.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.5 | 2.3, 2.4 | Customer |

Azure Policy를 사용하여 구독에 만들 수 있는 리소스 형식에 대한 제한을 설정합니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Azure 가상 머신 인벤토리를 사용하여 Virtual Machines의 모든 소프트웨어에 대한 정보 수집을 자동화합니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대한 액세스 권한을 가져오려면 게스트 수준 진단을 사용하도록 설정하고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

- [Azure 가상 머신 인벤토리를 사용하는 방법](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.7 | 2.5 | Customer |

Azure Security Center의 파일 무결성 모니터링(변경 내용 추적) 및 가상 머신 인벤토리를 사용하여 Virtual Machines에 설치된 모든 소프트웨어를 식별할 수 있습니다. 권한 없는 소프트웨어를 제거하는 자체 프로세스를 구현할 수 있습니다. 타사 솔루션을 사용하여 승인되지 않은 소프트웨어를 식별할 수도 있습니다.

- [파일 무결성 모니터링을 사용하는 방법](../../security-center/security-center-file-integrity-monitoring.md)

- [Azure 변경 내용 추적 이해](../../automation/change-tracking/overview.md)

- [Azure 가상 머신 인벤토리를 사용하는 방법](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.8 | 2.6 | Customer |

Azure Security Center 적응형 애플리케이션 제어를 사용하여 권한이 있는 소프트웨어만 실행되도록 하고 권한이 없는 모든 소프트웨어는 Azure Virtual Machines에서 실행되지 않도록 차단합니다.

- [Azure Security Center 적응형 애플리케이션 제어를 사용하는 방법](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.9 | 2.6 | Customer |

Azure Policy를 사용하여 사용자 환경에서 프로비전할 수 있는 서비스를 제한합니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.10 | 2.7 | Customer |

Azure Security Center 적응형 애플리케이션 제어를 사용하여 규칙을 적용하거나 적용하지 않을 파일 형식을 지정합니다.

요구 사항에 맞지 않은 경우 타사 솔루션을 구현합니다.

- [Azure Security Center 적응형 애플리케이션 제어를 사용하는 방법](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.11 | 2.9 | Customer |

"Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.12 | 2.9 | Customer |

스크립트 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용하여 Azure 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능을 제한할 수 있습니다.  Azure Security Center 적응형 애플리케이션 제어를 활용하여 권한이 있는 소프트웨어만 실행되도록 하고 권한이 없는 모든 소프트웨어는 Azure Virtual Machines에서 실행되지 않도록 차단할 수 있습니다.

- [Windows 환경에서 PowerShell 스크립트 실행을 제어하는 방법](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Security Center 적응형 애플리케이션 제어를 사용하는 방법](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 6.13 | 2.9 | Customer |

비즈니스 운영에 필요하지만 조직에 높은 위험을 초래할 수 있는 소프트웨어는 자체 가상 머신 및/또는 가상 네트워크 내에 격리하고 Azure Firewall 또는 네트워크 보안 그룹으로 충분히 보호해야 합니다.

- [가상 네트워크를 만드는 방법](../../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [보안 구성](security-control-secure-configuration.md) 참조