---
title: 작업 영역 및 테 넌 트 간에 Azure 센티널 확장 | Microsoft Docs
description: 작업 영역 및 테 넌 트에서 Azure 센티널의 분석 기능을 확장 하는 방법입니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 9e0fe46e0a7382c0adcfa1f1f781f282e9e77942
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019328"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>작업 영역 및 테넌트에 걸쳐 Azure Sentinel 확장

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>여러 Azure 센티널 작업 영역을 사용 해야 하는 경우

Azure 센티널은 Log Analytics 작업 영역을 기반으로 빌드됩니다. Azure 센티널을 등록 하는 첫 번째 단계는 해당 목적으로 사용 하려는 Log Analytics 작업 영역을 선택 하는 것입니다.

단일 작업 영역을 사용 하는 경우 Azure 센티널 환경의 모든 이점을 얻을 수 있습니다. 하지만 여러 작업 영역을 포함 해야 하는 경우도 있습니다. 다음 표에서는 이러한 상황 중 일부를 나열 하 고 가능한 경우 단일 작업 영역을 사용 하 여 요구 사항을 충족 하는 방법을 제안 합니다.

| 요구 사항 | Description | 작업 영역 수를 줄이는 방법 |
|-------------|-------------|--------------------------------|
| 주권 및 규정 준수 | 작업 영역은 특정 영역에 연결 됩니다. 규정 요구 사항을 충족 하기 위해 데이터를 다른 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/) 에 유지 해야 하는 경우 별도의 작업 영역으로 분할 해야 합니다. |  |
| 데이터 소유권 | 자회사 또는 관련 회사 등의 데이터 소유권 경계는 개별 작업 영역을 사용 하 여 더 잘 구분 됩니다. |  |
| 여러 Azure 테 넌 트 | Azure 센티널은 자체 Azure Active Directory (Azure AD) 테 넌 트 경계 내 에서만 Microsoft 및 Azure SaaS 리소스의 데이터 수집을 지원 합니다. 따라서 각 Azure AD 테 넌 트는 별도의 작업 영역이 필요 합니다. |  |
| 세부적인 데이터 액세스 제어 | 조직에서 조직의 내부 또는 외부에 있는 다른 그룹을 허용 하 여 Azure 센티널에서 수집 된 데이터에 액세스 해야 할 수 있습니다. 다음은 그 예입니다. <br><ul><li>리소스 소유자의 리소스에 관련 된 데이터 액세스</li><li>지역 또는 자회사 Soc ' 조직의 부분과 관련 된 데이터에 대 한 액세스</li></ul> | [리소스 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) 또는 [테이블 수준 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) 사용 |
| 세분화 된 보존 설정 | 지금까지 여러 작업 영역이 여러 데이터 형식에 대해 서로 다른 보존 기간을 설정 하는 유일한 방법 이었습니다. 이는 테이블 수준 보존 설정의 도입으로 인해 더 이상 필요 하지 않습니다. | [테이블 수준 보존 설정](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) 사용 또는 [데이터 삭제](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) 자동화 |
| 분할 청구 | 별도의 구독에 작업 영역을 배치 하면 다른 당사자에 게 요금이 청구 될 수 있습니다. | 사용 보고 및 교차 요금 |
| 레거시 아키텍처 | 여러 작업 영역을 사용 하는 것은 더 이상 적용 되지 않는 제한 사항 또는 모범 사례를 고려 하는 과거 디자인에서 기인 합니다. 또한 Azure 센티널에 맞게 수정할 수 있는 임의 디자인 선택이 될 수 있습니다.<br><br>다음은 이러한 템플릿의 예입니다.<br><ul><li>Azure Security Center 배포할 때 구독 당 기본 작업 영역 사용</li><li>비교적 새로운 솔루션에 대 한 세분화 된 액세스 제어 또는 보존 설정의 필요성</li></ul> | 작업 영역 다시 설계 |

### <a name="managed-security-service-provider-mssp"></a>MSSP (관리 되는 보안 서비스 공급자)

여러 작업 영역을 요구 하는 특정 사용 사례는 MSSP Azure 센티널 서비스입니다. 이 경우 여러 테 넌 트에 걸쳐 여러 작업 영역을 만드는 것이 가장 좋습니다. MSSP는 [Azure Lighthouse](../lighthouse/overview.md) 를 사용 하 여 테 넌 트 간에 azure 센티널 상호 작업 영역 기능을 확장할 수 있습니다.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure 센티널 다중 작업 영역 아키텍처

위의 요구 사항에 암시 된 대로 여러 Azure 센티널 작업 영역을 Azure Active Directory (Azure AD) 테 넌 트에 걸쳐 잠재적으로 단일 SOC에서 중앙 집중식으로 모니터링 하 고 관리 해야 하는 경우가 있습니다.

- MSSP Azure 센티널 서비스.

- 여러 자회사에 서비스를 제공 하는 글로벌 SOC 이며 각각 자체 로컬 SOC가 있습니다.

- 조직 내에서 여러 Azure AD 테 넌 트를 모니터링 하는 SOC

이러한 요구 사항을 해결 하기 위해 Azure 센티널은 중앙 모니터링, 구성 및 관리를 가능 하 게 하는 여러 작업 영역 기능을 제공 하 여 아래 다이어그램에 표시 된 대로 SOC에 포함 된 모든 항목에 대해 단일 창을 제공 합니다.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="작업 영역 간 아키텍처":::

이 모델은 모든 데이터가 단일 작업 영역에 복사 되는 완전히 중앙 집중화 된 모델에 비해 상당한 이점을 제공 합니다.

- 전역 및 로컬 Soc 또는 MSSP 고객에 게 유연한 역할 할당.

- 데이터 소유권, 데이터 개인 정보 보호 및 규정 준수와 관련 하 여 문제가 줄어듭니다.

- 최소 네트워크 대기 시간 및 요금.

- 새 자회사 또는 고객의 간편한 온 보 딩 및 오프 보 딩.

다음 섹션에서는이 모델을 운영 하는 방법과 특히 다음 작업을 수행 하는 방법을 설명 합니다.

- 여러 작업 영역을 중앙에서 모니터링 하 여 여러 테 넌 트 간에 잠재적으로 단일 창으로 SOC를 제공 합니다.

- 자동화를 사용 하 여 테 넌 트 간에 잠재적으로 여러 작업 영역을 중앙에서 구성 하 고 관리 합니다.

## <a name="cross-workspace-monitoring"></a>작업 영역 간 모니터링

### <a name="manage-incidents-on-multiple-workspaces"></a>여러 작업 영역에서 인시던트 관리

Azure 센티널은 여러 작업 영역에서 중앙 인시던트 모니터링과 관리를 용이 하 게 하는 [여러 작업 영역 인시던트 보기](./multiple-workspace-view.md) 를 지원 합니다. 중앙 집중식 인시던트 보기를 사용 하면 인시던트를 직접 관리 하거나 원래 작업 영역의 컨텍스트에서 인시던트 세부 정보를 투명 하 게 드릴 다운할 수 있습니다.

### <a name="cross-workspace-querying"></a>작업 영역 간 쿼리

Azure 센티널은 단일 쿼리에서 [여러 작업 영역](../azure-monitor/log-query/cross-workspace-query.md)쿼리를 지원 하므로 단일 쿼리에서 여러 작업 영역의 데이터를 검색 하 고 상관 관계를 지정할 수 있습니다. 

- [작업 영역 () 식을](../azure-monitor/log-query/workspace-expression.md) 사용 하 여 다른 작업 영역에 있는 테이블을 참조 합니다. 
- 작업 영역 () 식과 함께 [union 연산자](https://docs.microsoft.com/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) 를 사용 하 여 여러 작업 영역에 있는 테이블 간에 쿼리를 적용 합니다.

저장 된 [함수](../azure-monitor/log-query/functions.md) 를 사용 하 여 작업 영역 간 쿼리를 단순화할 수 있습니다. 예를 들어 작업 영역에 대 한 참조가 긴 경우 식을 이라는 함수로 저장 하는 것이 좋습니다 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` `SecurityEventCustomerA` . 그런 다음 쿼리를로 작성할 수 있습니다 `SecurityEventCustomerA | where ...` .

함수는 일반적으로 사용 되는 공용 구조체를 단순화할 수도 있습니다. 예를 들어 다음 식을 이라는 함수로 저장할 수 있습니다 `unionSecurityEvent` .

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

그런 다음부터 시작 하 여 두 작업 영역에 걸쳐 쿼리를 작성할 수 있습니다 `unionSecurityEvent | where ...` .

#### <a name="scheduled-alerts"></a>예약 된 경고

이제 작업 영역 간 쿼리를 분석 규칙의 예약 된 경고에 포함할 수 있으며, 다음과 같은 제한 사항이 적용 됩니다.

- 단일 쿼리에 최대 10 개의 작업 영역을 포함할 수 있습니다.
- Azure 센티널은 쿼리에서 참조 되는 모든 작업 영역에 배포 되어야 합니다.

> [!NOTE] 
> 동일한 쿼리에서 여러 작업 영역을 쿼리하면 성능에 영향을 줄 수 있으므로 논리에이 기능이 필요한 경우에만 권장 됩니다.

### <a name="using-cross-workspace-workbooks"></a>작업 영역 간 통합 문서 사용

[통합 문서](./overview.md#workbooks) 는 Azure 센티널에 대시보드 및 앱을 제공 합니다. 여러 작업 영역에서 작업 하는 경우 작업 영역 간에 모니터링 및 작업을 제공 합니다.

통합 문서는 다음과 같은 세 가지 방법 중 하나에서 작업 영역 쿼리를 제공할 수 있습니다. 각 메서드는 최종 사용자 전문 기술 수준에 따라 달라 집니다.

| 메서드  | Description | 언제를 사용 해야 하나요? |
|---------|-------------|--------------------|
| 작업 영역 간 쿼리 작성 | 통합 문서 작성자는 통합 문서에서 작업 영역 간 쿼리 (위에 설명 된)를 작성할 수 있습니다. | 이 옵션을 사용 하면 통합 문서 작성자가 작업 영역 구조에서 사용자를 완전히 보호할 수 있습니다. |
| 통합 문서에 작업 영역 선택기 추가 | 통합 문서 작성자는 [여기](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)에 설명 된 대로 통합 문서의 일부로 작업 영역 선택기를 구현할 수 있습니다. | 이 옵션은 사용 하기 쉬운 드롭다운 상자를 통해 통합 문서에 표시 되는 작업 영역에 대 한 제어를 사용자에 게 제공 합니다. |
| 대화형으로 통합 문서 편집 | 기존 통합 문서를 수정 하는 고급 사용자는 편집기에서 작업 영역 선택기를 사용 하 여 대상 작업 영역을 선택 하는 쿼리를 편집할 수 있습니다. | 이 옵션을 사용 하면 고급 사용자가 여러 작업 영역에서 작업 하도록 기존 통합 문서를 쉽게 수정할 수 있습니다. |
|

### <a name="cross-workspace-hunting"></a>작업 영역 간 구하기

Azure 센티널은 사용자를 시작 하기 위해 설계 된 미리 로드 된 쿼리 샘플을 제공 하 고 테이블 및 쿼리 언어에 대해 알아봅니다. 이러한 기본 제공 검색 쿼리는 새 쿼리를 추가 하 고 기존 쿼리를 세부적으로 조정 하는 것과 같은 방식으로 Microsoft 보안 연구원 들에 의해 개발 됩니다. 진입점을 제공 하 여 새 검색을 찾고 보안 도구에서 감지 하지 못할 수 있는 침입의 징후를 식별할 수 있습니다.  

작업 영역 간 구하기 기능을 사용 하면 위에 표시 된 것 처럼 union 연산자와 workspace () 식을 사용 하 여 새로운 구하기 쿼리를 만들거나 기존 요소를 적용 하 여 여러 작업 영역을 처리할 수 있습니다.

## <a name="cross-workspace-management-using-automation"></a>자동화를 사용 하 여 작업 영역 간 관리

여러 Azure 센티널 작업 영역을 구성 하 고 관리 하려면 Azure 센티널 관리 API의 사용을 자동화 해야 합니다. 경고 규칙, 구하기 쿼리, 통합 문서 및 플레이 북을 비롯 한 Azure 센티널 리소스의 배포를 자동화 하는 방법에 대 한 자세한 내용은 [Azure 센티널 확장: api, 통합 및 관리 자동화](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)를 참조 하세요.

Azure 센티널을 코드로 배포 하 고 관리 하 고 개인 GitHub 리포지토리에서 리소스를 배포 및 구성 하는 데 사용할 수 있는 통합 된 커뮤니티 제공 방법론에 대해 azure [Lighthouse](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) 를 [코드로 배포 및 관리](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) (영문)를 참조 하세요. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Azure Lighthouse를 사용 하 여 테 넌 트 간 작업 영역 관리

위에서 설명한 것 처럼 다양 한 azure 센티널 작업 영역은 다른 Azure AD 테 넌 트에 있을 수 있습니다. [Azure Lighthouse](../lighthouse/overview.md) 를 사용 하 여 테 넌 트 경계 전체에서 작업 영역 간 활동을 모두 확장할 수 있습니다. 그러면 관리 테 넌 트의 사용자가 모든 테 넌 트의 Azure 센티널 작업 영역에서 작업할 수 있습니다. Azure Lighthouse가 [등록](../lighthouse/how-to/onboard-customer.md)되 면 Azure Portal에서 [디렉터리 + 구독 선택기](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-from-other-tenants) 를 사용 하 여 관리 하려는 작업 영역이 포함 된 모든 구독을 선택 하 여 포털의 다른 작업 영역 선택기에서 모두 사용할 수 있도록 합니다.

Azure Lighthouse를 사용 하는 경우 각 Azure 센티널 역할에 대 한 그룹을 만들고 각 테 넌 트의 권한을 해당 그룹에 위임 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 여러 작업 영역 및 테 넌 트에서 Azure 센티널의 기능을 확장 하는 방법에 대해 알아보았습니다. Azure 센티널의 작업 영역 간 아키텍처 구현에 대 한 실질적인 지침은 다음 문서를 참조 하세요.

- Azure Lighthouse를 사용 하 여 Azure 센티널에서 [여러 테 넌 트로 작업](./multiple-tenants-service-providers.md) 하는 방법에 대해 알아봅니다.
- [여러 작업 영역에서 인시던트](./multiple-workspace-view.md) 를 원활 하 게 보고 관리 하는 방법을 알아봅니다.