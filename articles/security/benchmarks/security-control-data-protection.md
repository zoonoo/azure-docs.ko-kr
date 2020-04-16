---
title: Azure 보안 제어 - 데이터 보호
description: Azure 보안 제어 데이터 보호
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408555"
---
# <a name="security-control-data-protection"></a>보안 제어: 데이터 보호

데이터 보호 권장 사항은 암호화, 액세스 제어 목록, ID 기반 액세스 제어 및 데이터 액세스에 대한 감사 로깅과 관련된 문제를 해결하는 데 중점을 둡니다.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.1 | 13.1 | Customer |

태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있습니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.2 | 13.2, 2.10 | Customer |

환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다. 응용 프로그램 및 엔터프라이즈 환경이 요구하는 Azure 리소스에 대한 액세스 수준을 제한할 수 있습니다. Azure Active Directory 역할 기반 액세스 제어를 통해 Azure 리소스에 대한 액세스를 제어할 수 있습니다. 

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.3 | 13.3 | Shared |

Azure Marketplace의 타사 솔루션을 네트워크 경계에서 활용하여 중요한 정보의 무단 전송을 모니터링하고 정보 보안 전문가에게 경고하면서 이러한 전송을 차단합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중의 모든 민감한 정보 암호화

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.4. | 14.4 | Shared |

전송 중의 모든 중요한 정보를 암호화합니다. Azure 리소스에 연결하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인합니다.

해당되는 경우 미사용 암호화 및 전송 중 암호화에 대한 Azure Security Center 권장 사항을 따릅니다.

- [Azure를 사용한 전송 중 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.5. | 14.5 | Shared |

Azure에서 특정 서비스에 사용할 수 있는 기능이 없는 경우 타사 활성 검색 도구를 사용하여 구내 또는 원격 서비스 공급자를 포함하여 조직의 기술 시스템에서 저장, 처리 또는 전송되는 모든 중요한 정보를 식별하고 조직의 중요한 정보 인벤토리를 업데이트합니다.

Office 365 문서 내에서 중요한 정보를 식별하기 위해 Azure 정보 보호를 사용합니다.

Azure SQL 정보 보호를 사용하여 Azure SQL 데이터베이스에 저장된 정보의 분류 및 레이블 지정을 지원합니다.

- [Azure SQL 데이터 검색을 구현하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Azure 정보 보호를 구현하는 방법](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스를 제어합니다.

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.6 | 14.6 | Customer |

Azure AD RBAC를 사용하여 데이터 및 리소스에 대한 액세스를 제어하고 서비스 특정 액세스 제어 방법을 사용합니다.

- [Azure에서 RBAC를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.7 | 14.7 | Shared |

컴퓨팅 리소스를 준수하는 데 필요한 경우 자동화된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현하여 시스템에서 데이터를 복사하는 경우에도 데이터에 대한 액세스 제어를 적용합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 정보 암호화

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.8 | 14.8 | Customer |

모든 Azure 리소스에서 미사용 암호화를 사용합니다. Azure에서 암호화 키를 관리할 수 있도록 허용하는 것이 좋습니다. 

- [Azure의 미사용 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [고객 관리 암호화 키를 구성하는 방법](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 4.9 | 14.9 | Customer |

Azure 활동 로그와 함께 Azure 모니터를 사용하여 중요한 Azure 리소스에 대한 변경 이 수행되는 시기에 대한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [취약점 관리](security-control-vulnerability-management.md) 보기