---
title: 메트릭 Advisor 서비스에 변칙 피드백 제공
titleSuffix: Azure Cognitive Services
description: 메트릭 Advisor 인스턴스에서 찾은 변칙에 대 한 피드백을 보내고 결과를 조정 하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 11864cb26b76d414aa2efe2643797a2f66fa30e4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047610"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>피드백을 사용하여 변칙 검색 조정

메트릭 모니터에서 제공 하는 변칙 검색 결과 중 일부에 만족 하지 않는 경우 데이터에 적용 되는 모델에 영향을 주는 피드백을 수동으로 추가할 수 있습니다. 

페이지의 오른쪽 위 모퉁이에 있는 단추를 사용 하 여 피드백 주석 모드를 활성화 합니다.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="피드백 주석 모드입니다.":::

피드백 주석 모드가 활성화 된 후에는 한 점 또는 여러 연속 점에 대 한 피드백을 제공할 수 있습니다.

## <a name="give-feedback-for-one-point"></a>한 지점에 대 한 피드백 제공 

피드백 주석 모드가 활성화 된 상태에서 한 지점을 클릭 하 여 [ **사용자 의견 추가** ] 패널을 엽니다. 적용 하려는 피드백의 유형을 설정할 수 있습니다. 이러한 피드백은 향후 포인트 검색에 통합됩니다.  

* 메트릭이 메트릭 모니터에 의해 잘못 지정 된 것으로 생각 되는 경우 **변칙** 을 선택 합니다. 점이 비정상 인지 여부를 지정할 수 있습니다. 
* 시점에서 추세 변화의 시작을 나타내는 경우에는 **Changepoint** 를 선택 합니다.
* **기간** 을 선택 하 여 계절성를 표시 합니다. 메트릭 모니터는 계절성의 간격을 자동으로 검색 하거나 수동으로 지정할 수 있습니다. 

**주석 텍스트 상자** 에 주석을 그대로 두고 **저장** 을 클릭 하 여 피드백을 저장 합니다.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="피드백 주석 모드입니다.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>여러 연속 요소에 대 한 피드백 제공

마우스를 클릭 하 고 주석을 추가할 지점에서 마우스로 끌어 여러 연속 요소에 대 한 피드백을 한 번에 제공할 수 있습니다. 위와 동일한 피드백 메뉴가 표시 됩니다. **저장**을 클릭 한 후에는 선택한 모든 요소에 동일한 피드백이 적용 됩니다.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="피드백 주석 모드입니다.":::

## <a name="how-to-view-my-feedback"></a>내 피드백을 보는 방법

지점의 변칙 검색이 변경 되었는지 확인 하려면 점을 마우스로 가리킵니다. **사용자 의견의 영향을 받는** 도구 설명이 표시 됩니다. 검색 내용이 변경 된 경우 true입니다. **False**가 표시 되 면 해당 시점에서 피드백 계산이 수행 되었지만 변칙 검색 결과가 변경 되지 않았습니다.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="피드백 주석 모드입니다.":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>"Normal"로 변칙에 주석을 추가 해야 하는 경우

변칙이 거짓 경보 인 것으로 간주할 수 있는 많은 이유가 있습니다. 다음 시나리오 중 하나가 적용 되는 경우 다음 메트릭 Advisor 기능을 사용 하는 것이 좋습니다.


|시나리오  |권장 |
|---------|---------|
|이러한 문제는 알려진 데이터 원본 변경 (예: 시스템 변경)으로 인해 발생 합니다.     | 이 시나리오가 정기적으로 다시 발생 하지 않을 것으로 예상 되는 경우이 문제에 대 한 주석을 답니다.        |
|비정상은 휴일으로 인 한 것입니다.     | 지정 된 시간에 변칙 검색에 플래그를 지정 하려면 [미리 설정 된 이벤트](configure-metrics.md#preset-events) 를 사용 합니다.       |
|비정상 상황을 감지 하는 일반 패턴이 있습니다 (예: 주말).      |사용자 의견 기능 또는 미리 설정 된 이벤트를 사용 합니다.        |

## <a name="next-steps"></a>다음 단계
- [인시던트를 진단](diagnose-incident.md)합니다.
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)
- [후크를 사용하여 경고를 구성하고 알림 가져오기](../how-tos/alerts.md)