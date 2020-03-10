---
title: Azure Resource Health 개요
description: Azure Resource Health 개요
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: f22b41e66f15dd12d973c5744870969fae795bc1
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924956"
---
# <a name="resource-health-overview"></a>Resource Health 개요
 
Azure Resource Health를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단 하 고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 보고 합니다.

[Azure 상태](https://status.azure.com) 는 광범위 한 azure 고객 집합에 영향을 주는 서비스 문제에 대해 보고 합니다. Resource Health는 리소스의 상태에 대 한 개인 설정 된 대시보드를 제공 합니다. Resource Health는 Azure 서비스 문제로 인해 리소스를 사용할 수 없었던 모든 시간을 보여 줍니다. 이 데이터를 사용 하면 SLA를 위반 했는지 쉽게 확인할 수 있습니다.

## <a name="resource-definition-and-health-assessment"></a>리소스 정의 및 상태 평가

*리소스* 는 가상 머신, 웹 앱 또는 SQL database와 같은 Azure 서비스의 특정 인스턴스입니다. Resource Health는 서로 다른 Azure 서비스의 신호에 의존 하 여 리소스가 정상 인지 여부를 평가 합니다. 리소스가 정상이 아니면 Resource Health에서 추가 정보를 분석하여 문제 원인을 파악합니다. 또한 Microsoft가 문제를 해결 하기 위해 수행 하는 작업을 보고 하 고 해결 하기 위해 수행할 수 있는 작업을 식별 합니다.

상태를 평가 하는 방법에 대 한 자세한 내용은 [Azure Resource Health](resource-health-checks-resource-types.md)에서 리소스 유형 및 상태 검사 목록을 참조 하세요.

## <a name="health-status"></a>상태

리소스 상태는 다음 상태 중 하나로 표시됩니다.

### <a name="available"></a>사용 가능

*사용 가능* 은 리소스의 상태에 영향을 주는 검색 된 이벤트가 없음을 의미 합니다. 지난 24 시간 동안 계획 되지 않은 가동 중지 시간에서 리소스가 복구 되는 경우 "최근에 해결 됨" 알림이 표시 됩니다.

!["최근에 해결 됨" 알림이 있는 가상 머신의 상태 * 사용 가능 *](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>사용할 수 없음

*사용할 수 없음* 은 서비스에서 리소스 상태에 영향을 주는 진행 중인 플랫폼 또는 비-플랫폼 이벤트를 검색 했음을 의미 합니다.

#### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 이러한 작업에는 예약 된 작업 (예: 계획 된 유지 관리)과 예기치 않은 인시던트 (예: 계획 되지 않은 호스트 다시 부팅 또는 지정 된 기간 후에 실패 하는 것으로 예상 되는 호스트 하드웨어)가 모두 포함 됩니다.

Resource Health는 이벤트 및 복구 프로세스에 대 한 추가 정보를 제공 합니다. 또한 활성 지원 계약이 없는 경우에도 Microsoft 지원에 연결할 수 있습니다.

![플랫폼 이벤트로 인 한 가상 머신의 * 사용할 수 없음 * 상태](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>비-플랫폼 이벤트

비-플랫폼 이벤트는 사용자 작업에 의해 트리거됩니다. 예를 들면 가상 컴퓨터를 중지 하거나 Azure Cache for Redis에 대 한 최대 연결 수에 도달 하는 작업이 포함 됩니다.

![비-플랫폼 이벤트로 인 한 가상 머신의 "사용할 수 없음" 상태](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>알 수 없음

*Unknown* 은 Resource Health에서 리소스에 대 한 정보를 10 분 넘게 수신 하지 못했음을 의미 합니다. 이 상태는 리소스의 상태를 명확 하 게 표시 하지는 않지만 문제 해결을 위한 중요 한 데이터 요소입니다.

리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 *사용 가능*으로 변경됩니다.

리소스에 문제가 발생 하는 경우 알 수 *없는* 상태는 플랫폼의 이벤트가 리소스에 영향을 주는 것을 의미할 수 있습니다.

![가상 컴퓨터의 * Unknown * 상태](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>성능 저하됨

*저하* 됨은 리소스가 여전히 사용할 수 있지만 성능이 저하 되었음을 감지 했음을 의미 합니다.

리소스의 성능이 저하 되었음을 보고 하는 경우 다른 리소스에 대 한 고유한 기준이 있습니다.

![가상 컴퓨터에 대 한 * 성능 저하 * 상태](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>잘못된 상태 보고

현재 상태가 잘못 되었다고 생각 되는 경우 **잘못 된 상태 보고**를 선택 하 여 알려주세요. Azure 문제가 사용자에 게 영향을 주는 경우 Resource Health 지원 서비스에 문의 하는 것이 좋습니다.

![잘못 된 상태에 대 한 정보를 제출 하는 양식](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>기록 정보

Resource Health의 **상태 기록** 섹션에서 14 일 동안의 기록에 액세스할 수 있습니다.

![지난 2주 동안의 Resource Health 이벤트 목록](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>시작하기

하나의 리소스에 대한 Resource Health를 열려면:

1. Azure Portal에 로그인합니다.
2. 리소스를 찾습니다.
3. 왼쪽 창의 리소스 메뉴에서 **Resource Health**를 선택합니다.

![리소스 뷰에서 Resource Health 열기](./media/resource-health-overview/from-resource-blade.png)

**모든 서비스**를 선택하고 필터 텍스트 상자에 **Resource Health**를 입력하여 Resource Health에 액세스할 수도 있습니다. **도움말 + 지원** 창에서 [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)를 선택합니다.

!["모든 서비스"에서 Resource Health 열기](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>다음 단계

Resource Health에 대 한 자세한 내용은 다음 참조를 확인 하세요.
-  [Azure Resource Health에서 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.md)
