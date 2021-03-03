---
title: Azure 리소스 그래프의 azure 보안 기준
description: Azure 리소스 그래프 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5e01c8d1ac16e5e8be405660a0726796789e645
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738976"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Azure 리소스 그래프의 azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../../../security/benchmarks/overview-v1.md) 의 지침을 Azure 리소스 그래프로 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure 리소스 그래프에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure 리소스 그래프에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

 
Azure 리소스 그래프가 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 보려면 [전체 Azure 리소스 그래프 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../../../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure 리소스 그래프는 azure RBAC (역할 기반 액세스 제어)를 기반으로 하는 리소스 유형 및 속성에 대 한 액세스를 제공 합니다. 정기적으로 보안 주체 (사용자, 그룹 및 서비스 계정)에 부여 된 액세스를 감사 하 고 검토 하 여 쿼리가 적절 한 리소스에 대 한 결과를 반환 하는지 확인 합니다.

- [Azure Resource Graph의 권한](https://docs.microsoft.com/azure/governance/resource-graph/overview#permissions-in-azure-resource-graph)

- [Azure ID 액세스 검토를 사용하는 방법](../../../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../../../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어 

**지침**: Azure RBAC를 사용 하 여 데이터 및 리소스에 대 한 액세스를 제어 합니다. Azure 리소스 그래프를 사용 하려면 쿼리 하려는 리소스에 대 한 적절 한 액세스 권한도 있어야 합니다. 이 액세스는 읽기 전용으로 한정 되어야 하며 필요한 담당자 에게만 부여 되어야 합니다.

- [Azure Resource Graph의 권한](https://docs.microsoft.com/azure/governance/resource-graph/overview#permissions-in-azure-resource-graph)

- [Azure RBAC를 구성 하는 방법](../../../role-based-access-control/role-assignments-rest.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../../../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독, 관리 그룹 및 테 넌 트 내에서 지원 되는 모든 리소스를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 권한이 있는지 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거할 수 있습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../first-query-portal.md)

- [Azure RBAC 이해](../../../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다. Azure 리소스 그래프를 사용 하 여 승인 된 Azure 리소스 및 변경 내용 (미리 보기)을 쿼리하여 스냅숏을 검토 하 고 변경 된 내용을 확인 합니다.

- [Azure 리소스 그래프를 사용 하 여 Azure 리소스 쿼리](../first-query-portal.md)

- [리소스 변경 내용 가져오기](../how-to/get-resource-changes.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 리소스 그래프를 사용 하 여 구독, 관리 그룹 및 테 넌 트의 리소스를 쿼리하고 검색 합니다. 환경의 모든 Azure 리소스가 승인 되었는지 확인 합니다.

- [Azure 리소스 그래프를 사용 하 여 Azure 리소스 쿼리](../first-query-portal.md)

- [샘플: Azure 리소스 그래프에 대 한 스타터 쿼리](../samples/starter.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
