---
title: Azure Resource Health 개요
description: Azure Resource Health 개요
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159254"
---
# <a name="resource-health-overview"></a>리소스 상태 개요
 
Azure 리소스 상태를 사용하면 Azure 리소스에 영향을 주는 서비스 문제를 진단하고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 보고합니다.

[광범위한 Azure](https://status.azure.com) 고객 집합에 영향을 주는 서비스 문제에 대한 Azure 상태 보고합니다. 리소스 상태는 리소스의 상태를 개인화된 대시보드로 제공합니다. 리소스 상태는 Azure 서비스 문제로 인해 리소스를 사용할 수 없는 모든 시간을 표시합니다. 이 데이터를 사용하면 SLA가 위반되었는지 쉽게 확인할 수 있습니다.

## <a name="resource-definition-and-health-assessment"></a>리소스 정의 및 상태 평가

*리소스는* 가상 컴퓨터, 웹 앱 또는 SQL 데이터베이스와 같은 Azure 서비스의 특정 인스턴스입니다. 리소스 상태는 리소스가 정상인지 여부를 평가하기 위해 서로 다른 Azure 서비스의 신호를 사용합니다. 리소스가 정상이 아니면 Resource Health에서 추가 정보를 분석하여 문제 원인을 파악합니다. 또한 Microsoft가 문제를 해결하기 위해 취하는 작업에 대해보고하고 문제를 해결하기 위해 수행할 수 있는 작업을 식별합니다.

상태를 평가하는 방법에 대한 자세한 내용은 [Azure 리소스 상태](resource-health-checks-resource-types.md)의 리소스 유형 및 상태 확인 목록을 참조하십시오.

## <a name="health-status"></a>상태

리소스 상태는 다음 상태 중 하나로 표시됩니다.

### <a name="available"></a>사용 가능

*사용 가능한* 것은 리소스의 상태와 영향을 주는 검색된 이벤트가 없음을 의미합니다. 지난 24시간 동안 예기치 않은 가동 중지 시간에서 리소스가 복구된 경우 "최근에 해결된" 알림이 표시됩니다.

!["최근에 해결됨" 알림이 있는 가상 시스템의 *사용 가능* 상태](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>사용할 수 없음

*사용할 수 없음을* 의미하는 것은 서비스가 리소스의 상태와 영향을 주는 진행 중인 플랫폼 또는 비플랫폼 이벤트를 검색했다는 것을 의미합니다.

#### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예약된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 지정된 시간 기간 이후에 실패할 것으로 예상되는 계획되지 않은 호스트 재부팅 또는 성능 저하된 호스트 하드웨어)가 모두 포함됩니다.

리소스 상태는 이벤트 및 복구 프로세스에 대한 추가 세부 정보를 제공합니다. 또한 활성 지원 계약이 없는 경우에도 Microsoft 지원에 문의할 수 있습니다.

![플랫폼 이벤트로 인해 가상 시스템에 대해 *사용할 수 없음*의 상태](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>비-플랫폼 이벤트

비 플랫폼 이벤트는 사용자 작업에 의해 트리거됩니다. 예를 들어 가상 컴퓨터를 중지하거나 Redis에 대한 Azure 캐시에 대한 최대 연결 수에 도달하는 경우를 들 수 있습니다.

![플랫폼이 아닌 이벤트로 인해 가상 시스템에 대해 "사용할 수 없음" 상태](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>알 수 없음

*알 수 없음은* 리소스 상태에서 리소스에 대한 정보를 10분 이상 받지 못했는것을 의미합니다. 이 상태는 리소스의 상태를 확실하게 나타내는 것은 아니지만 문제 해결을 위한 중요한 데이터 포인트입니다.

리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 *사용 가능*으로 변경됩니다.

리소스에 문제가 있는 경우 *알 수 없는* 상태 상태는 플랫폼의 이벤트가 리소스에 영향을 미치고 있음을 의미할 수 있습니다.

![가상 시스템의 *알 수 없음* 상태](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>성능 저하됨

*성능이 저하되면* 리소스에서 성능 이 저하가 감지되었지만 여전히 사용할 수 있습니다.

리소스마다 성능이 저하되었다는 보고를 받을 때 고유한 기준이 있습니다.

![가상 시스템의 *성능 저하* 상태](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>잘못된 상태 보고

현재 상태가 올바르지 않다고 생각되면 **잘못된 상태 보고를**선택하여 알려주실 수 있습니다. Azure 문제가 영향을 미치는 경우 리소스 상태의 지원팀에 문의하는 것이 좋습니다.

![잘못된 상태에 대한 정보를 제출하는 양식](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>이력 정보

리소스 건강의 **상태 기록** 섹션에서 최대 30일의 기록에 액세스할 수 있습니다.

![지난 2주 동안의 Resource Health 이벤트 목록](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>시작

하나의 리소스에 대한 Resource Health를 열려면:

1. Azure Portal에 로그인합니다.
2. 리소스를 찾습니다.
3. 왼쪽 창의 리소스 메뉴에서 **Resource Health**를 선택합니다.

![리소스 뷰에서 Resource Health 열기](./media/resource-health-overview/from-resource-blade.png)

**모든 서비스**를 선택하고 필터 텍스트 상자에 **Resource Health**를 입력하여 Resource Health에 액세스할 수도 있습니다. **도움말 + 지원** 창에서 [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)를 선택합니다.

!["모든 서비스"에서 Resource Health 열기](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>다음 단계

리소스 상태에 대해 자세히 알아보려면 다음 참조를 확인하십시오.
-  [Azure 리소스 상태에서 리소스 유형 및 상태 확인](resource-health-checks-resource-types.md)
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.md)
