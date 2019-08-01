---
title: 기능 평가-Personalizer
titleSuffix: Azure Cognitive Services
description: Azure Portal에서 Personalizer 리소스에 대 한 평가를 실행 하는 경우 Personalizer는 모델에 영향을 주는 컨텍스트 및 작업 기능에 대 한 정보를 제공 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: d03c5f66f760a2bea9f99501cec478831a347c5d
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668746"
---
# <a name="feature-evaluation"></a>기능 평가

[Azure Portal](https://portal.azure.com)에서 Personalizer 리소스에 대 한 평가를 실행 하는 경우 Personalizer는 모델에 영향을 주는 컨텍스트 및 작업 기능에 대 한 정보를 제공 합니다. 

이는 다음을 수행 하는 데 유용 합니다.

* 모델에서 더 중요 한 기능을 활용 하 여 사용할 수 있는 추가 기능을 상상해 보세요.
* 중요 하지 않은 기능을 확인 하 고 잠재적으로 제거 하거나 사용에 영향을 미칠 수 있는 사항을 추가로 분석 합니다.
* 카탈로그에 가져오는 새 콘텐츠 또는 제품에 대 한 편집 또는 큐 레이 션에 대 한 지침을 제공 합니다.
* Personalizer로 기능을 전송할 때 발생 하는 일반적인 문제 및 실수를 해결 합니다.

더 중요 한 기능은 모델에서 더 강력한 가중치를 가집니다. 이러한 기능은 더 강력한 가중치를 가지 므로 Personalizer가 더 높은 보상을 얻을 때 제공 되는 경향이 있습니다.

## <a name="getting-feature-importance-evaluation"></a>기능 중요도 평가를 가져오는 중

기능 중요도 결과를 보려면 평가를 실행 해야 합니다. 기능 중요도에 대 한 결과 정보는 현재 Personalizer online 모델을 나타냅니다. 평가는 평가 기간의 종료 날짜에 저장 된 모델의 기능 중요도를 분석 합니다. 

평가 기간 동안 관찰 된 기능 이름을 기반으로 사람이 읽을 수 있는 기능 레이블을 만듭니다.

기능 중요도 결과는 평가 중에 테스트 되거나 생성 된 다른 정책과 모델을 나타내지 않습니다.  평가 기간이 끝난 후에는 Personalizer로 전송 된 기능이 평가에 포함 되지 않습니다.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>기능 중요도 평가를 해석 하는 방법

Personalizer는 유사한 중요도를 가진 "그룹" 기능을 만들어 기능을 평가 합니다. 한 그룹은 다른 그룹 보다 전체적으로 더 강력한 중요도를 갖지만 그룹 내에서는 기능 순서가 사전순으로 지정 될 수 있습니다.

각 기능에 대 한 정보는 다음과 같습니다.

* 기능이 컨텍스트나 작업에서 제공 되는지 여부입니다.
* 기능 키 및 값입니다.

예를 들어 아이스크림 주문 앱은 매우 중요 한 기능으로 "Context. 날씨: Hot"를 볼 수 있습니다.

Personalizer는 함께 고려 될 때 더 높은 보상을 생성 하는 기능의 상관 관계를 표시 합니다.

예를 들어 "context. 날씨: Hot *with* Action. Menuitem: IceCream" 뿐만 아니라 "Context. 날씨: 콜드 *With* Action. menuitem: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>기능 평가에 따라 수행할 수 있는 작업

### <a name="imagine-additional-features-you-could-use"></a>사용할 수 있는 추가 기능을 상상해 보세요.

모델에서 더 중요 한 기능을 활용 하세요. 예를 들어 비디오 모바일 앱에 "MobileBattery: Low"가 표시 되는 경우 연결 형식에서 고객이 다른 비디오 클립을 볼 수 있도록 선택한 다음, 연결 형식 및 대역폭에 대 한 기능을 앱에 추가할 수도 있습니다.

### <a name="see-what-features-are-not-important"></a>중요 하지 않은 기능 보기

잠재적으로 중요 하지 않은 기능을 제거 하거나 사용에 영향을 줄 수 있는 사항을 추가로 분석 합니다. 기능은 여러 가지 이유로 인해 부족할 수 있습니다. 그 중 하나는 기능이 사용자 동작에 영향을 주지 완전히 수 있습니다. 그러나 사용자에 게 기능이 명확 하지 않음을 의미할 수도 있습니다. 

예를 들어 비디오 사이트에서 "VideoResolution = 4k"는 낮은 중요도 기능 일치 사용자 research를 볼 수 있습니다. 응용 프로그램에서 비디오 해상도를 언급 하거나 표시 하지 않을 수 있으므로 사용자가 해당 동작에 따라 동작을 변경 하지 않는 것이 원인일 수 있습니다.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>편집 또는 큐 레이 션 팀에 대 한 지침 제공

카탈로그에 가져오는 새 콘텐츠 또는 제품에 대 한 지침을 제공 합니다. Personalizer은 사람이 이해 하 고 팀을 보강 하는 도구로 설계 되었습니다. 이 작업을 수행 하는 한 가지 방법은 동작을 구동 하는 제품, 문서 또는 콘텐츠에 대 한 정보를 편집 그룹에 제공 하는 것입니다. 예를 들어 비디오 응용 프로그램 시나리오는 "작업. i d. w i t. w i t e. Cat: true" 라는 중요 한 기능이 있음을 보여 줄 수 있습니다 .이는 편집 팀에 더 많은 Cat 비디오를 가져오도록 요청

### <a name="troubleshoot-common-problems-and-mistakes"></a>일반적인 문제 및 실수 문제 해결

응용 프로그램 코드를 변경 하 여 일반적인 문제 및 실수를 해결할 수 있으므로 부적절 하거나 형식이 잘못 된 기능을 Personalizer에 보내지 않습니다. 

기능을 전송할 때 일반적인 실수는 다음과 같습니다.

* PII (개인 식별이 가능한 정보)를 보내는 중입니다. 한 개인 (예: 이름, 전화 번호, 신용 카드 번호, IP 주소)과 관련 된 PII는 Personalizer에서 사용할 수 없습니다. 응용 프로그램에서 사용자를 추적 해야 하는 경우 식별 되지 않는 UUID 또는 다른 UserID 번호를 사용 합니다. 대부분의 시나리오에서는 문제가 발생할 수도 있습니다.
* 사용자 수가 많은 경우에는 각 사용자의 상호 작용이 모든 모집단의 상호 작용 보다 더 높은 것으로 평가 될 가능성이 거의 없으므로 사용자 Id (비 PII 인 경우에도)를 전송 하면 모델에 값 보다 더 많은 노이즈가 추가 될 수 있습니다.
* 날짜-시간 필드를 기능화 time 값 대신 정확한 타임 스탬프로 보냅니다. 컨텍스트별, Day = 월요일 또는 "Context. TimeStamp. Hour" = "13"과 같은 기능이 더 유용 합니다. 각각에 대해 최대 7 개 또는 24 개의 기능 값이 있습니다. 하지만 "50.52": "1985-04-12T23:20: Z"는 다시 발생 하지 않기 때문에 학습할 방법이 없다는 것을 정확 하 게 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

Personalizer를 사용 하 여 [확장성 및 성능](concepts-scalability-performance.md) 이해

