---
title: Azure 보안 제어-데이터 보호
description: 보안 제어 데이터 보호
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 914d1af9793107031af9797cb965420443e80c4a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564336"
---
# <a name="security-control-data-protection"></a>보안 제어: 데이터 보호

데이터 보호 권장 사항은 암호화, 액세스 제어 목록, id 기반 액세스 제어 및 데이터 액세스에 대 한 감사 로깅과 관련 된 문제를 해결 하는 데 중점을 둡니다.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요 한 정보 인벤토리 유지 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.1 | 13.1 | Customer |

태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스 추적을 지원 합니다.

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요 한 정보를 저장 하거나 처리 하는 시스템 격리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.2 | 13.2 | Customer |

개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현 합니다. 리소스는 VNet/서브넷으로 구분 되며, 적절 하 게 태그가 지정 되 고, NSG 또는 Azure 방화벽으로 보호 됩니다. 중요 한 데이터를 저장 하거나 처리 하는 리소스는 충분히 격리 되어야 합니다. 중요 한 데이터를 저장 하거나 처리 Virtual Machines 하는 경우 정책 및 프로시저를 구현 하 여 사용 하지 않을 때이를 해제 합니다.

추가 Azure 구독을 만드는 방법:

https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹를 만드는 방법:

https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용 하 여 NSG를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure 방화벽을 배포 하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽을 사용 하 여 경고 또는 경고 및 거부를 구성 하는 방법:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요 한 정보에 대 한 무단 전송을 모니터링 하 고 차단 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.3 | 13.3 | Customer |

중요 한 정보를 무단으로 전송 하는 것을 모니터링 하 고 정보 보안 전문가를 경고 하는 동안 이러한 전송을 차단 하는 자동 도구를 network 경계에 배포 합니다.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요 한 정보를 암호화 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.4. | 14.4 | 공유 |

전송 중인 모든 중요 한 정보를 암호화 합니다. Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

Azure를 사용 하 여 전송 중인 암호화 이해:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용 하 여 중요 한 데이터 식별

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.5. | 14.5 | Customer |

Azure에서 특정 서비스에 대 한 기능을 사용할 수 없는 경우 타사의 활성 검색 도구를 사용 하 여 사이트에 있는 서비스를 비롯 하 여 조직의 기술 시스템에서 저장, 처리 또는 전송 되는 중요 한 정보를 모두 식별할 수 있습니다. 원격 서비스 공급자를 제공 하 고 조직의 중요 한 정보 인벤토리를 업데이트 합니다.

Azure Information Protection를 사용 하 여 Office 365 문서 내에서 중요 한 정보를 식별 합니다.

Azure sql Information Protection를 사용 하 여 Azure SQL Database에 저장 된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

Azure SQL 데이터 검색을 구현 하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Azure Information Protection를 구현 하는 방법:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 제어

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.6 | 14.6 | Customer |

Azure AD RBAC를 사용 하 여 데이터 및 리소스에 대 한 액세스를 제어 하 고, 그렇지 않으면 서비스별 액세스 제어 방법을 사용 합니다.

Azure RBAC 이해:

https://docs.microsoft.com/azure/role-based-access-control/overview

Azure에서 RBAC를 구성 하는 방법:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용 하 여 액세스 제어를 적용 하십시오.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.7 | 14.7 | Customer |

시스템에서 데이터를 복사 하는 경우에도 데이터에 대 한 액세스 제어를 적용 하기 위해 자동화 된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현 합니다.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 휴지 상태의 중요 정보 암호화

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.8 | 14.8 | Customer |

모든 Azure 리소스에서 미사용 암호화를 사용 합니다. Azure에서 암호화 키를 관리 하도록 허용 하는 것이 좋지만 일부 인스턴스에서는 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다. 

Azure에서 미사용 암호화 이해:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

고객 관리 암호화 키를 구성 하는 방법:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요 한 Azure 리소스에 대 한 변경 내용을 기록 하 고 경고 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.9 | 14.9 | Customer |

Azure 활동 로그에 Azure Monitor를 사용 하 여 중요 한 Azure 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>다음 단계

다음 보안 제어: [취약성 관리](security-control-vulnerability-management.md) 를 참조 하세요.

