---
title: Personalizer 작동 방식 - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer는 기계 학습을 사용하여 컨텍스트에서 사용할 작업을 검색합니다. 각 학습 루프에는 Rank(순위) 및 Reward(보상) 호출을 통해 보낸 데이터에 대해 단독으로 학습되는 모델이 있습니다. 모든 학습 루프는 서로 완전히 독립적입니다.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026661"
---
# <a name="how-personalizer-works"></a>Personalizer 작동 방식

Personalizer는 기계 학습을 사용하여 컨텍스트에서 사용할 작업을 검색합니다. 각 학습 루프에는 **Rank**(순위) 및 **Reward**(보상) 호출을 통해 보낸 데이터에 대해 단독으로 학습되는 모델이 있습니다. 모든 학습 루프는 서로 완전히 독립적입니다. 개인에 맞게 설정하려는 애플리케이션의 각 부분 또는 동작에 대한 학습 루프를 만듭니다.

다음 목록을 사용하여 각 루프에 대해 현재 컨텍스트에 따라 **Rank API를 호출**합니다.

* 가능한 작업 목록: 최상위 작업을 선택할 콘텐츠 항목입니다.
* [콘텍스트 기능 목록](concepts-features.md): 사용자, 콘텐츠 및 컨텍스트와 같은 컨텍스트 관련 데이터입니다.

**Rank** API는 다음 중 하나를 사용하도록 결정합니다.

* _활용_: 과거 데이터에 따라 최상의 작업을 결정하는 현재 모델입니다.
* _검색_: 상위 작업 대신 다른 작업을 선택합니다.

**Reward** API는 다음과 같습니다.

* 각 순위 호출의 기능 및 보상 점수를 기록하여 모델을 학습시키기 위한 데이터를 수집합니다.
* 이 데이터를 사용하여 _학습 정책_에 지정된 설정에 따라 모델을 업데이트합니다.

## <a name="architecture"></a>아키텍처

다음 이미지에서는 Rank 호출 및 Reward 호출의 아키텍처 흐름을 보여 줍니다.

![alt text](./media/how-personalizer-works/personalization-how-it-works.png "맞춤 설정 작동 방식")

1. Personalizer는 내부 AI 모델을 사용하여 작업의 순위를 결정합니다.
1. 이 서비스에서 현재 모델을 활용할지, 아니면 모델에 대한 새 선택 항목을 검색할지를 결정합니다.  
1. 순위 결과가 EventHub로 전송됩니다.
1. Personalizer에서 보상을 받으면 해당 보상을 EventHub에 보냅니다. 
1. 순위와 보상이 상호 관련됩니다.
1. AI 모델이 상관 관계 결과에 따라 업데이트됩니다.
1. 유추 엔진이 새 모델로 업데이트됩니다. 

## <a name="research-behind-personalizer"></a>Personalizer에 대한 연구

Personalizer는 Microsoft Research의 백서, 연구 활동 및 진행 중인 탐구 영역을 포함하여 [보충 학습](concepts-reinforcement-learning.md) 분야의 최첨단 과학 및 연구를 기반으로 합니다.

## <a name="terminology"></a>용어

* **학습 루프**: 맞춤 설정을 활용할 수 있는 애플리케이션의 모든 부분에 대한 학습 루프를 만들 수 있습니다. 개인에 맞게 설정하려는 환경이 둘 이상인 경우 각각에 대한 반복을 만듭니다. 

* **작업**: 작업은 제품 또는 프로모션과 같이 선택할 수 있는 콘텐츠 항목입니다. Personalizer는 Rank API를 통해 사용자에게 표시할 상위 작업(_Reward 작업_이라고 함)을 선택합니다. 각 작업에는 Rank 요청과 함께 제출되는 기능이 있을 수 있습니다.

* **Context**: 더 정확한 순위를 제공하려면 컨텍스트에 대한 정보를 제공합니다. 예를 들어 다음과 같습니다.
    * 사용자
    * 사용하는 디바이스 
    * 현재 시간입니다.
    * 현재 상황에 대한 다른 데이터
    * 사용자 또는 컨텍스트에 대한 기록 데이터

    특정 애플리케이션에 다른 컨텍스트 정보가 있을 수 있습니다. 

* **[기능](concepts-features.md)**: 콘텐츠 항목 또는 사용자 컨텍스트에 대한 정보 단위입니다.

* **보상**: 사용자가 Rank API에서 반환된 작업에 응답한 정도를 0과 1 사이의 점수로 나타낸 측정값입니다. 0~1 값은 선택이 맞춤 설정의 비즈니스 목표를 달성하는 데 도움이 되는 정도에 기반한 비즈니스 논리에 따라 설정됩니다. 

* **검색**: Personalizer 서비스는 최상의 작업을 반환하는 대신 사용자를 위해 다른 작업을 선택하는 시점을 검색합니다. Personalizer 서비스는 드리프트와 정체를 방지하고 검색을 통해 진행 중인 사용자 동작에 적응할 수 있습니다. 

* **실험 기간**: Personalizer 서비스에서 해당 이벤트에 대한 Rank 호출이 발생한 순간부터 보상을 기다리는 시간의 양입니다.

* **비활성 이벤트**: 비활성 이벤트는 Rank를 호출한 이벤트이지만, 클라이언트 애플리케이션 결정으로 인해 사용자가 결과를 볼 수 있는지가 확실하지 않습니다. 비활성 이벤트를 사용하면 맞춤 설정 결과를 만들고 저장한 다음, 나중에 기계 학습 모델에 영향을 주지 않고 삭제하도록 결정할 수 있습니다.

* **모델**: Personalizer 모델은 사용자 동작에 대해 학습된 모든 데이터를 캡처하고, Rank 및 Reward 호출에 보내는 인수와 학습 정책에 따라 결정된 학습 동작을 조합하여 학습 데이터를 가져옵니다. 

## <a name="next-steps"></a>다음 단계

[Personalizer를 사용할 수 있는 경우](where-can-you-use-personalizer.md)를 이해합니다.