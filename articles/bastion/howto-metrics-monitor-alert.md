---
title: Azure Monitor를 사용 하 여 모니터링 및 메트릭 구성
titleSuffix: Azure Bastion
description: Azure에서 Azure Monitor 사용 하는 Azure 방호 모니터링 및 메트릭에 대해 알아보고, 메트릭, 경고, Azure 전반의 진단 로그에 대 한 솔루션입니다.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417946"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Azure Monitor를 사용 하 여 Azure 방호의 모니터링 및 메트릭을 구성 하는 방법

이 문서를 통해 Azure Monitor를 사용 하 여 Azure 방호의 모니터링 및 메트릭을 사용할 수 있습니다.

>[!NOTE]
>**클래식 메트릭** 을 사용하면 좋지 않습니다.
>

## <a name="about-metrics"></a>메트릭 정보

Azure 방호에는 사용 가능한 다양 한 메트릭이 있습니다. 다음 표에서는 사용 가능한 각 메트릭의 범주와 크기를 보여 줍니다.

|**메트릭**|**범주**|**개 차원**|
| --- | --- | --- |
|요새 통신 상태 * *|[가용성](#availability)|해당 없음|
|총 메모리|[가용성](#availability)|인스턴스|
|사용 되는 CPU|[트래픽](#traffic)|인스턴스
|사용 되는 메모리|[트래픽](#traffic)|인스턴스
|세션 수|[성능](#performance)|인스턴스|

* * 요새 통신 상태는 11 월 2020 일 이후에 배포 된 요새 호스트에만 적용 됩니다.

### <a name="availability-metrics"></a><a name="availability"></a>가용성 메트릭

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>요새 통신 상태

요새 호스트를 구성 하는 모든 인스턴스에 걸쳐 집계 된 Azure 방호의 통신 상태를 볼 수 있습니다.

* 값 **1** 은 해당 하는 요새를 사용할 수 있음을 나타냅니다.
* 값이 **0** 이면 요새 서비스를 사용할 수 없음을 나타냅니다.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="통신 상태를 보여 주는 스크린샷":::

#### <a name="total-memory"></a><a name="total-memory"></a>총 메모리

각 요새 인스턴스 간에 분할 된 Azure 방호의 총 메모리를 볼 수 있습니다.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="총 메모리를 보여 주는 스크린샷":::

### <a name="traffic-metrics"></a><a name="traffic"></a>트래픽 메트릭

#### <a name="used-cpu"></a><a name="used-cpu"></a>사용 되는 CPU

Azure 방호의 CPU 사용률을 확인 하 고 각 요새 인스턴스에 걸쳐 분할할 수 있습니다. 이 메트릭을 모니터링 하면 Azure 방호를 구성 하는 인스턴스의 가용성 및 용량을 측정 하는 데 도움이 됩니다.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="사용 되는 CPU를 보여 주는 스크린샷":::

#### <a name="used-memory"></a><a name="used-memory"></a>사용 되는 메모리

각 요새 인스턴스에서 메모리 사용률을 볼 수 있으며 각 요새 인스턴스 간에 분할 됩니다. 이 메트릭을 모니터링 하면 Azure 방호를 구성 하는 인스턴스의 가용성 및 용량을 측정 하는 데 도움이 됩니다.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="사용 되는 메모리를 보여 주는 스크린샷":::

### <a name="performance-metrics"></a><a name="performance"></a>성능 메트릭

#### <a name="session-count"></a>세션 수

각 세션 유형 (RDP 및 SSH)에서 집계 된 방호 인스턴스당 활성 세션 수를 볼 수 있습니다. 각 Azure 방호는 다양 한 활성 RDP 및 SSH 세션을 지원할 수 있습니다. 이 메트릭을 모니터링 하면 요새 서비스를 실행 하는 인스턴스 수를 조정 해야 하는지 여부를 이해 하는 데 도움이 됩니다. Azure 방호에서 지원할 수 있는 세션 수에 대 한 자세한 내용은 [Azure 방호 FAQ](bastion-faq.md)를 참조 하세요.

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="세션 수를 보여 주는 스크린샷":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>메트릭을 보는 방법

1. 메트릭을 보려면 요새 호스트로 이동 합니다.
1. **모니터링** 목록에서 **메트릭** 을 선택 합니다.
1. 매개 변수를 선택합니다. 메트릭이 설정 되지 않은 경우 **메트릭 추가** 를 클릭 한 다음 매개 변수를 선택 합니다.

   * **범위:** 기본적으로이 범위는 요새 호스트로 설정 됩니다.
   * **메트릭 네임 스페이스:** 표준 메트릭.
   * **메트릭:** 보려는 메트릭을 선택 합니다.

1. 메트릭을 선택 하면 기본 집계가 적용 됩니다. 필요에 따라 분할을 적용할 수 있습니다 .이 경우 다른 차원의 메트릭이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
  
