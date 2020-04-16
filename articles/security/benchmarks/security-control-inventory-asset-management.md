---
title: Azure 보안 제어 - 인벤토리 및 자산 관리
description: Azure 보안 제어 인벤토리 및 자산 관리
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408368"
---
# <a name="security-control-inventory-and-asset-management"></a>보안 관리: 재고 및 자산 관리

인벤토리 및 자산 관리 권장 사항은 권한이 부여된 리소스만 액세스권한을 부여하고 권한이 없고 관리되지 않는 리소스가 식별되고 제거되도록 모든 Azure 리소스를 능동적으로 관리(인벤토리, 추적 및 수정)와 관련된 문제를 해결하는 데 중점을 둡니다.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다.  테넌트의 적절한(읽기) 권한을 확인하고 구독 내의 모든 Azure 구독및 리소스를 등록합니다.

리소스 그래프를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로 Azure 리소스 관리자 리소스를 만들고 사용하는 것이 좋습니다.

- [Azure 리소스 그래프를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.2 | 1.5 | Customer |

메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류로 구성합니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.3 | 1.6 | Customer |

적절한 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 자산을 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고 승인되지 않은 리소스가 적시에 구독에서 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.4 | 2.1 | Customer |

조직의 필요에 따라 계산 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어의 인벤토리를 만듭니다.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.5 | 2.3, 2.4 | Customer |

Azure 정책을 사용하여 구독에서 만들 수 있는 리소스 유형에 제한을 두십시오.

Azure 리소스 그래프를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure 정책을 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 그래프로 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Azure 가상 시스템 인벤토리를 사용하여 가상 컴퓨터의 모든 소프트웨어에 대한 정보 수집을 자동화합니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure 포털에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대한 액세스 권한을 얻으려면 게스트 수준 진단을 사용하도록 설정하고 Windows 이벤트 로그를 로그 분석 작업 영역으로 가져옵니다.

- [Azure 가상 시스템 인벤토리를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.7 | 2.5 | Customer |

Azure 보안 센터의 파일 무결성 모니터링(변경 정보 추적) 및 가상 시스템 인벤토리를 사용하여 가상 컴퓨터에 설치된 모든 소프트웨어를 식별합니다. 권한이 있는 소프트웨어를 제거하기 위한 자체 프로세스를 구현할 수 있습니다. 타사 솔루션을 사용하여 승인되지 않은 소프트웨어를 식별할 수도 있습니다.

- [파일 무결성 모니터링 사용 방법](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Azure 변경 변경 추적 이해](https://docs.microsoft.com/azure/automation/change-tracking)

- [Azure 가상 시스템 인벤토리를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: 승인된 신청서만 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.8 | 2.6 | Customer |

Azure 보안 센터 적응형 응용 프로그램 컨트롤을 사용하여 권한이 부여된 소프트웨어만 실행되고 권한이 없는 모든 소프트웨어가 Azure 가상 컴퓨터에서 실행되지 못하도록 차단됩니다.

- [Azure 보안 센터 적응형 응용 프로그램 컨트롤을 사용하는 방법](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.9 | 2.6 | Customer |

Azure 정책을 사용하여 사용자 환경에서 프로비전할 수 있는 서비스를 제한합니다.

- [Azure 정책을 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 정책을 사용하여 특정 리소스 유형을 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.10 | 2.7 | Customer |

Azure 보안 센터 적응형 응용 프로그램 컨트롤을 사용하여 규칙이 적용될 수도 있거나 적용되지 않을 수 있는 파일 형식을 지정합니다.

요구 사항을 충족하지 않는 경우 타사 솔루션을 구현합니다.

- [Azure 보안 센터 적응형 응용 프로그램 컨트롤을 사용하는 방법](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure 리소스 관리자와 상호 작용하는 사용자의 기능 제한

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.11 | 2.9 | Customer |

Azure 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대해 "액세스 차단"을 구성하여 사용자가 Azure 리소스 관리자와 상호 작용하는 기능을 제한합니다.

- [Azure 리소스 관리자에 대한 액세스를 차단하기 위해 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.12 | 2.9 | Customer |

스크립트 유형에 따라 운영 체제 별 구성 또는 타사 리소스를 사용하여 Azure 계산 리소스 내에서 스크립트를 실행하는 사용자의 기능을 제한할 수 있습니다.  또한 Azure Security Center 적응형 응용 프로그램 컨트롤을 활용하여 권한이 있는 소프트웨어만 실행되고 권한이 없는 모든 소프트웨어가 Azure 가상 컴퓨터에서 실행되지 못하도록 차단할 수 있습니다.

- [Windows 환경에서 PowerShell 스크립트 실행을 제어하는 방법](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Azure 보안 센터 적응형 응용 프로그램 컨트롤을 사용하는 방법](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 6.13 | 2.9 | Customer |

비즈니스 작업에 필요하지만 조직에 더 높은 위험이 발생할 수 있는 소프트웨어는 자체 가상 시스템 및/또는 가상 네트워크 내에서 격리되어야 하며 Azure 방화벽 또는 네트워크 보안 그룹으로 충분히 보호되어야 합니다.

- [가상 네트워크를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [보안 구성으로 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [보안 구성](security-control-secure-configuration.md) 보기