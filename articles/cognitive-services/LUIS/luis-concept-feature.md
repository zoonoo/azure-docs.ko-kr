---
title: Azure에서 LUIS 앱의 기능 이해 | Microsoft Docs
description: LUIS 앱의 성능을 개선하는 데 도움이 되는 기능을 알아봅니다. 기능에는 정규식을 인식하기 위한 구문 목록 및 패턴이 포함됩니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: diberry
ms.openlocfilehash: 8d3f006f27d1d728f89458deba27e1c1a63b6de5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224931"
---
# <a name="phrase-list-features-in-luis"></a>LUIS의 구문 목록 기능

기계 학습에서 ‘기능’은 시스템이 관찰하는 데이터의 구별되는 특성입니다. 

언어 모델에 기능을 추가하여 레이블을 지정하거나 분류하려는 입력을 인식하는 방법에 대한 힌트를 제공합니다. 기능을 사용하면 LUIS가 의도와 엔터티를 둘 다 인식할 수 있지만, 기능은 의도 또는 엔터티 자체가 아닙니다. 대신에, 기능은 관련 용어의 예제를 제공할 수 있습니다.  

## <a name="what-is-a-phrase-list-feature"></a>구문 목록 기능이란?
구문 목록에는 동일한 클래스에 속하고 비슷하게 처리되어야 하는 값(단어 또는 구문) 그룹이 포함됩니다(예: 도시 또는 제품의 이름). LUIS가 이러한 값 중 하나에 대해 학습하는 내용이 다른 값에도 자동으로 적용됩니다. 이 목록은 일치하는 단어의 닫힌 [목록 엔터티](luis-concept-entity-types.md#types-of-entities)(정확한 텍스트 일치)가 아닙니다.

구문 목록은 해당 단어와 관련된 LUIS에 대한 두 번째 신호로 앱 도메인의 어휘에 추가됩니다.

## <a name="how-to-use-phrase-lists"></a>구문 목록 사용 방법
Human Resource 앱의 [단순 엔터티 자습서](luis-quickstart-primary-and-secondary-data.md)에서 이 앱은 직업 형식(예: 프로그래머, 지붕 수리인 및 비서)의 **Job** 구 목록을 사용합니다. 이러한 값 중 하나에 기계 학습 엔터티로 레이블을 지정하면 LUIS는 다른 값을 인식하기 위해 학습합니다. 

구문 목록은 서로 교환 가능하거나 서로 교환 불가능할 수 있습니다. *서로 교환 가능한* 구 목록은 동의어에 해당하는 값에 사용하고, *서로 교환 불가능한* 구 목록은 동의어는 아니지만 앱에서 여전히 추가 신호가 필요한 값에 사용합니다. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>
## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>구 목록은 단순 Interchangeable 엔터티를 식별하는 데 도움이 됩니다.
서로 교환 가능한 구 목록은 LUIS 앱의 성능을 조정하는 좋은 방법입니다. 앱이 올바른 의도에 대한 발화를 예측하거나 엔터티를 인식하는 데 문제가 있는 경우에는 발화에 비정상적인 단어나 의미가 모호할 수 있는 단어가 포함되어 있는지 생각해 보세요. 이러한 단어는 구문 목록에 포함할 좋은 후보입니다.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>구문 목록은 컨텍스트를 보다 잘 이해하여 의도를 식별하는 데 도움이 됨
구문 목록은 엄격한 일치를 수행하거나 항상 구문 목록의 모든 용어에 똑같은 레이블을 지정하기 위한 LUIS에 대한 명령이 아닙니다. 힌트일 뿐입니다. 예를 들어, “Patti” 및 “Selma”가 이름임을 나타내는 구 목록이 있을 수 있지만, LUIS는 여전히 컨텍스트 정보를 사용하여 “Make a reservation for 2 at Patti's Diner for dinner”(Patti의 저녁 식사 손님으로 2명 예약) 및 “Find me driving directions to Selma, Georgia”(조지아주 셀마(Selma)로 향하는 주행 방향 찾기)에서 해당 이름이 서로 다른 것을 의미함을 인식할 수 있습니다. 

의도에 더 많은 예제 발화를 추가하는 대신 구문 목록을 추가할 수 있습니다. 

## <a name="an-interchangeable-phrase-list"></a>서로 교환 가능한 구 목록
단어 또는 구가 클래스 또는 그룹을 만들 때 서로 교환 가능한 구 목록을 사용합니다. 예를 들면 "January", "February", "March"와 같은 월 목록이나 "John", "Mary", "Frank"와 같은 이름 목록이 있습니다.  이러한 목록은 구 목록의 다른 단어가 사용될 경우 발언이 같은 의도나 엔터티로 레이블이 지정된다는 측면에서 서로 교환 가능합니다. 예를 들어, "show the calendar for January"가 "show the calendar for February"와 같은 의도를 갖는 경우 단어가 서로 교환 가능한 목록에 포함됩니다. 

## <a name="a-non-interchangeable-phrase-list"></a>서로 교환 불가능한 구 목록
도메인에서 그룹화될 수 있는 동의어가 아닌 단어나 구에는 서로 교환 불가능한 구 목록을 사용합니다. 

예를 들어, 희귀 단어, 독점 단어 및 외래 단어에는 서로 교환 불가능한 구 목록을 사용합니다. LUIS가 희귀 및 독점 단어뿐 아니라 앱 문화권 외부의 외래 단어를 인식하지 못할 수 있습니다. 서로 교환 불가능한 설정은 희귀 단어 집합이 LUIS가 인식하도록 학습되어야 하는 클래스를 구성하지만 동의어가 아니고 서로 교환 가능하지 않음을 나타냅니다.

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>구문 목록 및 목록 엔터티를 사용하는 경우
구문 목록과 목록 엔터티는 모든 의도의 발화에 서로 다른 방법으로 영향을 줄 수 있습니다. 구문 목록을 사용하면 의도 예측 점수에 영향을 줍니다. 목록 엔터티를 사용하면 정확한 텍스트 일치를 위해 엔터티 추출에 영향을 줍니다. 

### <a name="use-a-phrase-list"></a>구문 목록 사용
구문 목록을 기반으로 LUIS는 여전히 컨텍스트를 고려하고 비슷하지만 정확히 일치하지는 않는 항목을 목록의 항목으로 식별하도록 일반화될 수 있습니다. LUIS 앱이 범주의 새 항목을 일반화하고 식별할 수 있게 하려면 구문 목록을 사용합니다. 

새 연락처의 이름을 인식해야 하는 모임 스케줄러 또는 새 제품을 인식해야 하는 인벤토리 앱과 같이 엔터티의 새 인스턴스를 인식할 수 있게 하려면 단순 또는 계층 구조 엔터티와 같은 다른 유형의 기계 학습으로 학습된 엔터티를 사용합니다. 그런 다음, LUIS가 엔터티와 유사한 다른 단어를 찾는 데 도움이 되는 단어 및 구문의 구문 목록을 만듭니다. 이 목록은 해당 단어의 값에 중요도를 추가하여 엔터티 예제를 인식하도록 LUIS를 안내합니다. 

구문 목록은 의도 및 엔터티 모두의 이해 품질을 개선하도록 도와주는 도메인별 어휘와 비슷합니다. 일반적으로 구문 목록은 도시 이름과 같은 고유 명사에 사용됩니다. 도시 이름은 하이픈이나 아포스트로피를 포함한 여러 단어일 수 있습니다.
 
### <a name="dont-use-a-phrase-list"></a>구문 목록 사용 안 함 
목록 엔터티는 엔터티가 사용할 수 있는 모든 값을 명시적으로 정의하고 정확히 일치하는 값만 식별합니다. 엔터티의 모든 인스턴스가 알려지고 자주 변경되지 않는 앱의 경우 목록 엔터티가 적절할 수 있습니다. 자주 변경되지 않는 음식점 메뉴의 음식 항목을 예로 들 수 있습니다. 엔터티와 정확히 일치하는 텍스트가 필요한 경우, 구문 목록을 사용하지 마세요. 

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="next-steps"></a>다음 단계

LUIS 앱에 기능을 추가하는 방법에 대해 자세히 알아보려면 [기능 추가](luis-how-to-add-features.md)를 참조하세요.
