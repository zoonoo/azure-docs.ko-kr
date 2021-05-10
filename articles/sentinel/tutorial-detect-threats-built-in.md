---
title: Azure Sentinel에서 기본 제공 분석 규칙을 사용하여 위협 감지 | Microsoft Docs
description: 의심스러운 상황이 발생할 때 사용자에게 알리는 기본 제공 템플릿을 기준으로 하는 기본 제공 위협 검색 규칙을 사용하는 방법을 알아봅니다.
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
ms.date: 03/19/2021
ms.author: yelevin
ms.openlocfilehash: 951c616961ff68b810ca135d09a6f6253cb2b7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773558"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>자습서: 처음부터 위협 감지

Azure Sentinel에 [데이터 원본을 연결](quickstart-onboard.md)한 후 의심스러운 상황이 발생하면 알림이 표시됩니다. 바로 이때문에 Azure Sentinel에서는 위협 검색 규칙을 만드는 데 도움이 되는 기본 제공 템플릿을 제공하고 있습니다. 이러한 템플릿은 알려진 위협, 일반적인 공격 벡터 및 의심스러운 활동 에스컬레이션 체인을 바탕으로 Microsoft의 보안 전문가 및 분석가 팀이 설계한 템플릿을 사용합니다. 이러한 템플릿에서 생성된 규칙은 사용자 환경에서 의심스러운 활동을 자동으로 검색합니다. 많은 템플릿을 사용자 지정하여 활동을 검색하거나, 필요하다면 필터를 지정할 수 있습니다. 이러한 규칙을 통해 생성되는 경고는 사용자 환경에서 할당하고 조사할 수 있는 인시던트를 만듭니다.

이 자습서는 Azure Sentinel로 위협을 감지하는 데 도움이 되는 설명을 제공합니다.

> [!div class="checklist"]
> * 기본 제공 위협 검색 사용
> * 위협 응답 자동화

## <a name="about-out-of-the-box-detections"></a>기본 제공 검색 정보

기본 제공되는 탐지를 모두 보려면 **Analytics** 로 이동한 다음, **규칙 템플릿** 으로 이동합니다. 이 탭에는 Azure Sentinel 기본 제공 규칙이 모두 포함됩니다.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="기본 제공 탐지를 사용하여 Azure Sentinel로 위협 요소 찾기":::

사용할 수 있는 템플릿 유형은 다음과 같습니다.

- **Microsoft 보안**
   
   Microsoft 보안 템플릿은 다른 Microsoft 보안 솔루션에서 생성된 경고에서 실시간으로 Azure Sentinel 인시던트를 자동으로 만듭니다. Microsoft 보안 규칙을 템플릿으로 사용하여 유사한 논리로 새 규칙을 만들 수 있습니다. 보안 규칙에 대한 자세한 내용은 [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)을 참조하세요.

- **Fusion** 

    Fusion 기술을 기준으로 하는 Azure Sentinel의 고급 다단계 공격 감지는 여러 제품에서 발생하는 여러 가지 낮은 충실도 경고 및 이벤트를 높은 충실도 및 실행 가능한 인시던트에 상호 연관 지을 수 있는 스케일링 가능한 머신 러닝 알고리즘을 사용합니다. Fusion은 기본적으로 사용하도록 설정되어 있습니다. 논리는 숨겨져 있어서 사용자 지정할 수 없으므로 이 템플릿을 사용하여 규칙을 하나만 만들 수 있습니다.

    > [!IMPORTANT]
    > Fusion 규칙 템플릿의 일부 검색은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
    >
    > 미리 보기로 제공되는 검색을 확인하려면 [Azure Sentinel에서 고급 다단계 공격 감지](fusion.md)를 참조하세요.

- **기계 학습 동작 분석**

    이러한 템플릿은 독점적인 Microsoft 머신 러닝 알고리즘을 기준으로 하므로 작동 방법 및 실행 시기에 대한 내부 논리를 볼 수 없습니다. 논리는 숨겨져 있어서 사용자 지정할 수 없으므로 이 형식의 각 템플릿을 사용하여 규칙을 하나만 만들 수 있습니다.

    > [!IMPORTANT]
    > - 머신 러닝 동작 분석 규칙 템플릿은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
    >
    > - ML 동작 분석 템플릿을 기준으로 하는 규칙을 만들고 사용하도록 설정하여 머신 러닝 엔진과 모델로 처리하는 데 필요한 대로 **Azure Sentinel 작업 영역의 지리적 위치 외부에서 수집한 데이터를 복사하기 위한 Microsoft 권한을 부여** 합니다.

- **예약**

    예약된 분석 규칙은 Microsoft 보안 전문가가 작성한 기본 제공 쿼리를 기준으로 합니다. 쿼리 논리를 보고 변경할 수 있습니다. 예약된 규칙 템플릿을 사용하고 쿼리 논리 및 일정 설정을 사용자 지정하여 새 규칙을 만들 수 있습니다.

## <a name="use-out-of-the-box-detections"></a>기본 제공 검색 사용

1. 기본 제공 템플릿을 사용하려면 템플릿 이름을 클릭한 다음, 세부 정보 창에서 **규칙 만들기** 단추를 클릭하여 해당 템플릿을 기준으로 하는 새 활성 규칙을 만듭니다. 각 템플릿에는 필요한 데이터 원본 목록이 있습니다. 템플릿을 열면 데이터 원본의 가용성이 자동으로 검사됩니다. 가용성 문제가 있는 경우 **규칙 만들기** 단추가 사용하지 않도록 설정되거나 해당 효과에 대한 경고가 표시될 수 있습니다.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="검색 규칙 미리 보기 패널":::
 
1. **규칙 만들기** 단추를 클릭하면 선택한 템플릿에 따라 규칙 만들기 마법사가 열립니다. 모든 세부 정보가 자동으로 채워지며, **예약** 또는 **Microsoft 보안** 템플릿을 사용하여 논리 및 기타 규칙 설정을 사용자 지정하여 특정 요구에 더 적합하게 만들 수 있습니다. 이 프로세스를 반복하여 기본 제공 템플릿을 기준으로 하는 추가 규칙을 만들 수 있습니다. 규칙 만들기 마법사의 단계를 끝까지 완료한 후에는 템플릿을 기준으로 하는 규칙 만들기가 완료됩니다. **활성 규칙** 탭에 새 규칙이 표시됩니다.

    규칙 만들기 마법사에서 규칙을 사용자 지정하는 방법에 대한 자세한 내용은 [자습서: 위협 검색을 위한 사용자 지정 분석 규칙 만들기](tutorial-detect-threats-custom.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Sentinel을 사용하여 위협 검색을 시작하는 방법을 배웠습니다. 

위협에 대한 응답을 자동화하는 방법에 대해 알아보려면 [Azure Sentinel에서 자동화된 위협 응답 설정](tutorial-respond-threats-playbook.md)을 참조하세요.
