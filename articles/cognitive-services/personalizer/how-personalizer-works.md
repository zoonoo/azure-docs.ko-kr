---
title: Personalizer 작동 방식 - Personalizer
description: 개인 설정 _루프는_ 기계 학습을 사용하여 콘텐츠에 대한 최상위 작업을 예측하는 모델을 빌드합니다. 모델은 랭크 및 리워드 호출로 전송한 데이터에 대해독점적으로 학습됩니다.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623766"
---
# <a name="how-personalizer-works"></a>Personalizer의 작동 원리

개인 설정 자 리소스, _학습 루프는_기계 학습을 사용하여 콘텐츠에 대한 상위 작업을 예측하는 모델을 빌드합니다. 모델은 **랭크** 및 **리워드** 호출로 전송한 데이터에 대해독점적으로 학습됩니다. 모든 루프는 서로 완전히 독립적입니다.

## <a name="rank-and-reward-apis-impact-the-model"></a>순위 및 보상 API는 모델에 영향을 미칩니다.

기능 및 컨텍스트 _기능이_ _있는 작업을_ Rank API로 보냅니다. **Rank** API는 다음 중 하나를 사용하도록 결정합니다.

* _악용_: 현재 모델은 과거 데이터를 기반으로 최선의 작업을 결정합니다.
* _탐색_: 최상위 작업 대신 다른 작업을 선택합니다. Azure 포털에서 개인 설정 리소스에 대해 [이 백분율을 구성합니다.](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)

보상 점수를 결정하고 해당 점수를 리워드 API로 보냅니다. **Reward** API는 다음과 같습니다.

* 각 순위 호출의 기능 및 보상 점수를 기록하여 모델을 학습시키기 위한 데이터를 수집합니다.
* 이 데이터를 사용하여 _학습 정책에_지정된 구성에 따라 모델을 업데이트합니다.

## <a name="your-system-calling-personalizer"></a>개인 설정에 전화하는 시스템

다음 이미지에서는 Rank 호출 및 Reward 호출의 아키텍처 흐름을 보여 줍니다.

![대체 텍스트](./media/how-personalizer-works/personalization-how-it-works.png "개인 화의 작동 방식")

1. 기능 및 컨텍스트 _기능이_ _있는 작업을_ Rank API로 보냅니다.

    * 개인 설정자는 현재 모델을 사용할지 아니면 모델에 대한 새로운 선택 사항을 탐색할지 결정합니다.
    * 순위 결과가 EventHub로 전송됩니다.
1. 최상위 랭크는 보상 액션 _ID로_시스템에 반환됩니다.
    시스템은 해당 콘텐츠를 제시하고 자체 비즈니스 규칙에 따라 보상 점수를 결정합니다.
1. 시스템은 보상 점수를 학습 루프로 반환합니다.
    * Personalizer에서 보상을 받으면 해당 보상을 EventHub에 보냅니다.
    * 순위와 보상이 상호 관련됩니다.
    * AI 모델이 상관 관계 결과에 따라 업데이트됩니다.
    * 유추 엔진이 새 모델로 업데이트됩니다.

## <a name="personalizer-retrains-your-model"></a>맞춤 설정은 모델을 다시 설정합니다.

개인 설정은 Azure 포털의 개인 설정 리소스에서 **모델 빈도 업데이트** 설정에 따라 모델을 다시 연결합니다.

개인 설정은 Azure 포털의 개인 설정 리소스에서 일 수의 **데이터 보존** 설정에 따라 현재 보존된 모든 데이터를 사용합니다.

## <a name="research-behind-personalizer"></a>Personalizer에 대한 연구

Personalizer는 Microsoft Research의 백서, 연구 활동 및 진행 중인 탐구 영역을 포함하여 [보충 학습](concepts-reinforcement-learning.md) 분야의 최첨단 과학 및 연구를 기반으로 합니다.

## <a name="next-steps"></a>다음 단계

맞춤 설정에 대한 [주요 시나리오에](where-can-you-use-personalizer.md) 대해 알아보기