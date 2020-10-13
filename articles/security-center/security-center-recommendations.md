---
title: Azure Security Center의 보안 권장 사항
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0efb2c26a63418835dd2b7561a1fb9d3a9d5fee8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945563"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure Security Center의 보안 권장 사항 
이 항목에서는 Azure 리소스를 보호 하는 데 도움이 되는 Azure Security Center의 권장 사항을 확인 하 고 이해 하는 방법을 설명 합니다.


## <a name="what-are-security-recommendations"></a>보안 권장 사항이란?

권장 사항은 리소스를 보호 하기 위해 수행할 작업입니다.

Security Center는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다.

각 권장 사항은 다음을 제공 합니다.

- 문제에 대 한 간단한 설명입니다.
- 권장 사항을 구현 하기 위해 수행할 수정 단계
- 영향을 받는 리소스

## <a name="monitor-recommendations"></a>권장 사항 모니터링 <a name="monitor-recommendations"></a>

Security Center는 리소스의 보안 상태를 분석 하 여 잠재적인 취약성을 식별 합니다. 

1. Security Center의 메뉴에서 **권장** 구성 페이지를 열어 환경에 적용할 수 있는 권장 사항을 확인 합니다. 권장 사항은 보안 제어로 그룹화 됩니다.

      ![보안 제어 별로 그룹화 된 권장 사항](./media/security-center-recommendations/view-recommendations.png)

1. 컨트롤을 확장 하 고 특정 권장 사항을 선택 하 여 권장 사항 세부 정보 페이지를 표시 합니다.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="권장 사항 세부 정보 페이지입니다." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    페이지에는 다음이 포함 됩니다.

    1. 지원 되는 권장 사항에 대 한 **적용** 및 **거부** 단추 ( [적용/거부 권장 구성으로 잘못 된 구성 방지](prevent-misconfigurations.md)참조)
    1. **심각도 표시기**
    1. **새로 고침 간격**  (관련 된 경우) 
    1. **설명** -문제에 대 한 간단한 설명입니다.
    1. **수정 단계** -영향을 받는 리소스에 대 한 보안 문제를 해결 하는 데 필요한 수동 단계에 대 한 설명입니다. ' 빠른 수정 '을 사용 하는 권장 사항에 대해서는 리소스에 제안 된 픽스를 적용 하기 전에 **업데이트 관리 논리 보기** 를 선택할 수 있습니다. 
    1. **영향을 받는 리소스** -리소스는 탭으로 그룹화 됩니다.
        - **정상 리소스** – 영향을 받지 않거나 이미 문제를 해결 한 관련 리소스입니다.
        - **비정상 리소스** – 식별 된 문제의 영향을 받는 리소스입니다.
        - **해당 하지 않는 리소스** – 권장 구성이 명확한 답변을 제공할 수 없는 리소스입니다. 해당 없음 탭에는 각 리소스의 이유가 포함 됩니다. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="권장 사항 세부 정보 페이지입니다.":::
    1. 권장 사항을 재구성 하거나 논리 앱을 트리거하는 작업 단추입니다.

## <a name="preview-recommendations"></a>미리 보기 권장 사항

**미리 보기로** 플래그가 지정 된 권장 사항은 보안 점수 계산에 포함 되지 않습니다.

미리 보기 기간이 종료되면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정해야 합니다.

미리 보기 추천 사항의 예는 다음과 같습니다.

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="권장 사항 세부 정보 페이지입니다.":::
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. 권장 사항을 수정 하는 방법을 알아보려면 다음을 수행 하십시오.

- [권장 사항](security-center-remediate-recommendations.md) 수정-Azure 구독 및 리소스 그룹에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
- [강제 적용/거부 권장 구성으로 잘못 된 구성을 방지](prevent-misconfigurations.md)합니다.
