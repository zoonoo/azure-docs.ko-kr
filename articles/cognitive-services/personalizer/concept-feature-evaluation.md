---
title: 기능 평가 - 개인화
titleSuffix: Azure Cognitive Services
description: Azure 포털에서 개인 설정 자 리소스에서 평가를 실행 하는 경우 개인 설정 은 컨텍스트 및 작업의 기능에 대 한 정보를 제공 합니다 모델에 영향을 줍니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242424"
---
# <a name="feature-evaluation"></a>기능 평가

[Azure 포털에서](https://portal.azure.com)개인 설정 자 리소스에서 평가를 실행 하는 경우 개인 설정 은 컨텍스트 및 작업의 기능에 대 한 정보를 제공 합니다 모델에 영향을 줍니다. 

이 기능은 다음을 수행하기 위해 유용합니다.

* 모델에서 더 중요한 기능에서 영감을 얻어 사용할 수 있는 추가 기능을 상상해 보십시오.
* 중요하지 않은 기능을 확인하고 잠재적으로 제거하거나 사용량에 영향을 줄 수 있는 기능을 추가로 분석할 수 있습니다.
* 에디토리얼 또는 큐레이션 팀에게 카탈로그에 가져올 가치가 있는 새로운 콘텐츠 나 제품에 대한 지침을 제공합니다.
* 피처를 개인에게 보낼 때 발생하는 일반적인 문제와 실수를 해결합니다.

더 중요한 피쳐는 모델에서 더 강한 가중치를 갖습니다. 이러한 기능은 무게가 더 강하기 때문에, 개인화자가 더 높은 보상을 획득할 때 존재하는 경향이 있습니다.

## <a name="getting-feature-importance-evaluation"></a>기능 중요도 평가 받기

피처 중요도 결과를 보려면 평가를 실행해야 합니다. 평가는 평가 기간 동안 관찰된 피쳐 이름을 기반으로 사람이 읽을 수 있는 피쳐 레이블을 만듭니다.

기능 중요도에 대한 결과 정보는 현재 맞춤 설정 온라인 모델을 나타냅니다. 평가는 평가 기간의 종료 일에 저장된 모델의 특징 중요성을 분석하며, 평가 중에 수행된 모든 교육을 수행한 후 현재 온라인 학습 정책을 통해 분석합니다. 

피쳐 중요도 결과는 평가 중에 테스트하거나 만든 다른 정책 및 모델을 나타내지 않습니다.  평가에는 평가 기간이 끝난 후 개인 설정에 전송된 기능이 포함되지 않습니다.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>기능 중요도 평가를 해석하는 방법

개인 설정은 비슷한 중요성을 가진 기능의 "그룹"을 만들어 기능을 평가합니다. 한 그룹은 다른 그룹보다 전반적으로 더 중요하다고 할 수 있지만 그룹 내에서 기능 순서는 사전순으로 정렬됩니다.

각 기능에 대한 정보는 다음과 같습니다.

* 피처가 컨텍스트 또는 작업에서 제공되는지 여부입니다.
* 기능 키 및 값입니다.

예를 들어 아이스크림 가게 주문 앱에는 "Context.Weather:Hot"이 매우 중요한 기능으로 표시될 수 있습니다.

Personalizer는 함께 고려하면 더 높은 보상을 생성하는 기능의 상관 관계를 표시합니다.

예를 들어 "Context.Weather:Hot *with* Action.MenuItem:IceCream" 및 "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>기능 평가를 기반으로 수행할 수 있는 작업

### <a name="imagine-additional-features-you-could-use"></a>사용할 수 있는 추가 기능 상상해 보십시오.

모델의 더 중요한 기능에서 영감을 얻으십시오. 예를 들어 비디오 모바일 앱에서 "Context.MobileBattery:Low"가 표시되는 경우 연결 유형으로 인해 고객이 한 비디오 클립을 다른 비디오 클립위에 표시하도록 선택한 다음 앱에 연결 유형 및 대역폭에 대한 기능을 추가할 수도 있습니다.

### <a name="see-what-features-are-not-important"></a>중요하지 않은 기능 보기

중요하지 않은 기능을 제거하거나 사용에 영향을 줄 수 있는 기능을 추가로 분석할 수 있습니다. 여러 가지 이유로 기능이 낮을 수 있습니다. 하나는 진정으로 기능이 사용자 동작에 영향을 미치지 않는다는 것입니다. 그러나 이 기능이 사용자에게 명확하지 않음을 의미할 수도 있습니다. 

예를 들어 비디오 사이트에서는 "Action.VideoResolution=4k"가 사용자 연구와 모순되는 중요도가 낮은 기능임을 알 수 있습니다. 그 원인은 응용 프로그램이 비디오 해상도를 언급하거나 표시하지 않기 때문에 사용자가 그에 따라 동작을 변경하지 않기 때문에 발생할 수 있습니다.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>편집 또는 큐레이션 팀에 지침 제공

카탈로그에 가져올 가치가 있는 새로운 콘텐츠 또는 제품에 대한 지침을 제공합니다. Personalizer는 인간의 통찰력과 팀을 강화하는 도구로 설계되었습니다. 한 가지 방법은 행동을 유도하는 제품, 기사 또는 콘텐츠에 대한 정보를 에디토리얼 그룹에 제공하는 것입니다. 예를 들어 비디오 응용 프로그램 시나리오에는 "Action.VideoEntities.Cat:true"라는 중요한 기능이 있음을 보여 주어 편집 팀이 더 많은 고양이 비디오를 가져오도록 유도할 수 있습니다.

### <a name="troubleshoot-common-problems-and-mistakes"></a>일반적인 문제와 실수 문제 해결

응용 프로그램 코드를 변경하여 일반적인 문제와 실수를 해결할 수 있으므로 부적절하거나 잘못 형식이 지정된 기능을 Personalizer에 보내지 않습니다. 

기능을 보낼 때 일반적인 실수는 다음과 같습니다.

* 개인 식별 정보(PII) 전송. 한 개인(이름, 전화 번호, 신용 카드 번호, IP 주소 등)에 특정한 PII를 Personalizer와 함께 사용해서는 안 됩니다. 응용 프로그램에서 사용자를 추적해야 하는 경우 식별되지 않는 UUID 또는 기타 UserID 번호를 사용합니다. 대부분의 시나리오에서 이것은 또한 문제가 됩니다.
* 사용자 수가 많으면 각 사용자의 상호 작용이 모든 모집단의 상호 작용보다 더 중요할 가능성은 없으므로 PII가 아닌 경우에도 사용자 아이디를 보내는 것이 모델에 값보다 더 많은 노이즈를 추가할 수 있습니다.
* 날짜-시간 필드를 위업시간 값 대신 정확한 타임스탬프로 전송합니다. Context.TimeStamp.Day=월요일 또는 "Context.TimeStamp.Hour"="13"과 같은 기능을 갖는 것이 더 유용합니다. 각각에 대해 7 개 또는 24 개의 피처 값이 있을 것입니다. 그러나 "Context.TimeStamp":"1985-04-12T23:20:50.52Z"는 너무 정확해서 다시는 발생하지 않기 때문에 배울 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

개인화를 통해 [확장성과 성능을 이해합니다.](concepts-scalability-performance.md)

