---
title: Azure Monitor를 사용하여 모니터링 및 메트릭 구성
titleSuffix: Azure Bastion
description: Azure Monitor를 사용하는 Azure Bastion 모니터링 및 메트릭에 대해 알아봅니다.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: bed26390a2a64b7bbb39f1df014d0d63ccce6a5f
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110534776"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Azure Monitor를 사용하여 Azure Bastion에 대한 모니터링 및 메트릭을 구성하는 방법

이 문서를 통해 Azure Monitor를 사용하여 Azure Bastion의 모니터링 및 메트릭을 사용할 수 있습니다.

>[!NOTE]
>**클래식 메트릭** 을 사용하면 좋지 않습니다.
>

## <a name="about-metrics"></a>메트릭 정보

Azure Bastion에는 사용 가능한 다양한 메트릭이 있습니다. 다음 표에서는 사용 가능한 각 메트릭의 범주와 차원을 보여줍니다.

|**메트릭**|**범주**|**차원**|
| --- | --- | --- |
|Bastion 통신 상태**|[가용성](#availability)|해당 없음|
|총 메모리|[가용성](#availability)|인스턴스|
|사용된 CPU|[트래픽](#traffic)|인스턴스
|사용된 메모리|[트래픽](#traffic)|인스턴스
|세션 수|[성능](#performance)|인스턴스|

** Bastion 통신 상태는 2020년 11월 이후에 배포된 베스천 호스트에만 적용됩니다.

### <a name="availability-metrics"></a><a name="availability"></a>가용성 메트릭

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Bastion 통신 상태

베스천 호스트를 구성하는 모든 인스턴스에 걸쳐 집계된 Azure Bastion의 통신 상태를 볼 수 있습니다.

* 값 **1** 은 베스천을 사용할 수 있음을 나타냅니다.
* 값 **0** 은 베스천 서비스를 사용할 수 없음을 나타냅니다.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="통신 상태를 보여주는 스크린샷.":::

#### <a name="total-memory"></a><a name="total-memory"></a>총 메모리

각 베스천 인스턴스 간에 분할된 Azure Bastion의 총 메모리를 볼 수 있습니다.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="총 메모리를 보여주는 스크린샷.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>트래픽 메트릭

#### <a name="used-cpu"></a><a name="used-cpu"></a>사용된 CPU

각 베스천 인스턴스 간에 분할된 Azure Bastion의 CPU 사용률을 볼 수 있습니다. 이 메트릭을 모니터링하면 Azure Bastion을 구성하는 인스턴스의 가용성과 용량을 측정하는 데 도움이 됩니다.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="사용된 CPU를 보여주는 스크린샷.":::

#### <a name="used-memory"></a><a name="used-memory"></a>사용된 메모리

각 베스천 인스턴스 간에 분할된 각 베스천 인스턴 간에 메모리 사용률을 볼 수 있습니다. 이 메트릭을 모니터링하면 Azure Bastion을 구성하는 인스턴스의 가용성과 용량을 측정하는 데 도움이 됩니다.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="사용된 메모리를 보여주는 스크린샷.":::

### <a name="performance-metrics"></a><a name="performance"></a>성능 메트릭

#### <a name="session-count"></a>세션 수

각 세션 유형(RDP 및 SSH)에서 집계된 베스천 인스턴스당 활성 세션 수를 볼 수 있습니다. 각 Azure Bastion은 다양한 활성 RDP 및 SSH 세션을 지원할 수 있습니다. 이 메트릭을 모니터링하면 베스천 서비스를 실행하는 인스턴스 수를 조정해야 하는지 여부를 이해하는 데 도움이 됩니다. Azure Bastion에서 지원할 수 있는 세션 수에 대한 자세한 내용은 [Azure Bastion FAQ](bastion-faq.md)를 참조하세요.

이 메트릭의 구성에 권장되는 값은 다음과 같습니다.

* **집계:** 평균
* **세분성:** 5분 또는 15 분
* 인스턴스를 기준으로 분할하여 보다 정확한 개수를 얻는 것이 좋습니다.

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="세션 수를 보여주는 스크린샷.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>메트릭을 보는 방법

1. 메트릭을 보려면 베스천 호스트로 이동합니다.
1. **모니터링** 목록에서 **메트릭** 을 선택합니다.
1. 매개 변수를 선택합니다. 메트릭이 설정되지 않은 경우 **메트릭 추가** 를 클릭한 다음, 매개 변수를 선택합니다.

   * **범위:** 기본적으로 이 범위는 베스천 호스트로 설정됩니다.
   * **메트릭 네임스페이스:** 표준 메트릭.
   * **메트릭:** 보려는 메트릭을 선택합니다.

1. 메트릭을 선택하면 기본 집계가 적용됩니다. 필요에 따라 분할을 적용하여 차원이 다른 메트릭을 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
  
