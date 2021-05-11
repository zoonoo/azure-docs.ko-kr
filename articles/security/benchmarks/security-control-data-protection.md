---
title: Azure 보안 제어 - 데이터 보호
description: Azure 보안 제어 데이터 보호
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d36ed6a795c5fa2241ee71751053a4cb5986aaf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576822"
---
# <a name="security-control-data-protection"></a>보안 제어: 데이터 보호

데이터 보호 권장 사항은 암호화, 액세스 제어 목록, ID 기반 액세스 제어 및 데이터 액세스에 대한 감사 로깅과 관련된 문제를 해결하는 데 중점을 둡니다.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.1 | 13.1 | Customer |

태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.2 | 13.2, 2.10 | Customer |

환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다. 애플리케이션 및 엔터프라이즈 환경에서 요구하는 Azure 리소스에 대한 액세스 수준을 제한할 수 있습니다. Azure RBAC(역할 기반 액세스 제어)를 통해 Azure 리소스에 대한 액세스를 제어할 수 있습니다. 

- [추가 Azure 구독을 만드는 방법](../../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.3 | 13.3 | 공유됨 |

중요한 정보를 무단 전송하는지 모니터링하는 네트워크 경계에서 Azure Marketplace의 타사 솔루션을 활용하고, 정보 보안 전문가에게 경고를 보내는 동안 해당 전송을 차단합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터가 손실되거나 노출되지 않게 보호합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.4. | 14.4 | 공유됨 |

전송 중인 모든 중요한 정보를 암호화합니다. Azure 리소스에 연결하는 모든 클라이언트가 TLS 1.2 이상과 협상할 수 있는지 확인합니다.

적용 가능할 경우 미사용 암호화 및 전송 중인 암호화에 대한 Azure Security Center의 권장 사항을 따릅니다.

- [Azure를 사용한 전송 중 암호화 이해](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.5. | 14.5 | 공유됨 |

Azure에서 특정 서비스에 대한 기능을 사용할 수 없는 경우, 타사 활성 검색 도구를 사용하여 온 사이트 또는 원격 서비스 공급자를 비롯하여 조직의 기술 시스템에서 저장, 처리 또는 전송하는 중요한 정보를 모두 식별하고 조직의 중요한 정보 인벤토리를 업데이트합니다.

Azure Information Protection을 사용하여 Microsoft 365 문서 내에서 중요한 정보를 식별합니다.

Azure SQL Information Protection을 사용하여 Azure SQL Database에 저장된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

- [Azure SQL 데이터 검색을 구현하는 방법](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [Azure Information Protection 구현 방법](/azure/information-protection/deployment-roadmap)

- [Azure의 고객 데이터 보호 이해](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.6 | 14.6 | Customer |

Azure RBAC(역할 기반 액세스 제어)를 사용하여 데이터 및 리소스에 대한 액세스를 제어하거나 서비스별 액세스 제어 방법을 사용합니다.

- [Azure RBAC를 구성하는 방법](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.7 | 14.7 | 공유됨 |

컴퓨팅 리소스를 준수하기 위해 필요하다면 시스템에서 데이터를 복사하는 경우에도 데이터에 대한 액세스 제어를 적용하기 위해 자동화된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.8 | 14.8 | Customer |

모든 Azure 리소스에서 미사용 암호화를 사용합니다. Microsoft는 Azure에서 암호화 키를 관리하도록 허용하는 것을 권장하지만 일부 인스턴스에서는 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다. 

- [Azure의 저장 데이터 암호화 이해](../fundamentals/encryption-atrest.md)

- [고객 관리형 암호화 키를 구성하는 방법](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 4.9 | 14.9 | Customer |

Azure 활동 로그에 Azure Monitor를 사용하여 중요한 Azure 리소스가 변경될 때 경고를 생성합니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../../azure-monitor/alerts/alerts-activity-log.md)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [취약성 관리](security-control-vulnerability-management.md)를 참조하세요.