---
title: 활성 및 비활성 이벤트-Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Personalizer 서비스 내에서 활성 및 비활성 이벤트, 학습 설정 및 학습 정책의 사용 방법을 설명 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 1641a1020193395d7d2ddb9c4893bd7bc89cdcd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681869"
---
# <a name="active-and-inactive-events"></a>활성 및 비활성 이벤트

응용 프로그램이 Rank API를 호출 하면 응용 프로그램이 **rewardActionId** 필드에 표시 해야 하는 동작이 표시 됩니다.  이 순간부터 Personalizer는 동일한 eventId를 가진 보상 호출을 기대 합니다. 보상 점수는 이후 순위 호출을 위해 모델을 학습 하는 데 사용 됩니다. EventId에 대해 보상 호출이 수신 되지 않으면 기본 보상을 적용 합니다. 기본 보상은 Azure Portal에서 설정 됩니다.

일부 시나리오에서는 응용 프로그램이 결과를 사용 하거나 사용자에 게 표시할지 여부를 파악 하기 전에 Rank를 호출 해야 할 수 있습니다. 이는 예를 들어 마케팅 캠페인에서 승격 된 콘텐츠의 페이지 렌더링을 덮어쓰는 경우에 발생할 수 있습니다. Rank 호출의 결과가 사용 되지 않은 경우 사용자가이를 확인 하지 않으면 해당 보상 통화를 보내지 않습니다.

일반적으로 이러한 시나리오는 다음과 같은 경우에 발생 합니다.

* 사용자가 표시 하거나 볼 수 없는 렌더링 하지 않는 UI입니다. 
* 응용 프로그램이 거의 실시간 컨텍스트로 순위를 설정 하 고 응용 프로그램에서 출력을 사용 하거나 사용 하지 않을 때 예측 개인 설정을 수행 하 고 있습니다. 

이러한 경우 Personalizer를 사용 하 여 Rank를 호출 하 고 이벤트를 _비활성화_하도록 요청 합니다. Personalizer는이 이벤트에 대 한 보상을 받을 수 없으며 기본 보상을 적용 하지 않습니다. 비즈니스 논리에서 나중에 응용 프로그램에서 순위 호출의 정보를 사용 하는 경우에는 이벤트를 _활성화_ 하기만 하면 됩니다. 이벤트가 활성화 되는 즉시 Personalizer는 이벤트 보상을 기대 합니다. 보상 API에 대 한 명시적 호출이 없으면 Personalizer는 기본 보상을 적용 합니다.

## <a name="inactive-events"></a>비활성 이벤트

이벤트에 대 한 학습을 사용 하지 않도록 설정 하려면 `learningEnabled = False`을 사용 하 여 순위를 호출 합니다. 비활성 이벤트의 경우 이벤트 Id에 대 한 보상을 보내거나 해당 eventId에 대해 `activate` API를 호출 하면 학습이 암시적으로 활성화 됩니다.

## <a name="learning-settings"></a>학습 설정

학습 설정은 모델 학습의 하이퍼 *매개 변수* 를 결정 합니다. 서로 다른 학습 설정에서 학습 된 동일한 데이터의 두 모델은 서로 다르게 작동 합니다.

### <a name="import-and-export-learning-policies"></a>학습 정책 가져오기 및 내보내기

Azure Portal에서 학습 정책 파일을 가져오고 내보낼 수 있습니다. 이 메서드를 사용 하 여 기존 정책을 저장 하 고 테스트 하 여 바꾸고 나중에 참조 및 감사 하기 위한 아티팩트로 소스 코드 제어에 보관 합니다.

### <a name="understand-learning-policy-settings"></a>학습 정책 설정 이해

학습 정책의 설정은 변경할 수 없습니다. 설정이 Personalizer에 미치는 영향을 이해 하는 경우에만 설정을 변경 하십시오. 이 지식이 없으면 Personalizer 모델 무효화를 비롯 한 문제가 발생할 수 있습니다.

### <a name="compare-learning-policies"></a>학습 정책 비교

[오프 라인 평가](concepts-offline-evaluation.md)를 수행 하 여 Personalizer 로그의 이전 데이터에 대해 다양 한 학습 정책을 수행 하는 방법을 비교할 수 있습니다.

[사용자 고유의 학습 정책을 업로드](how-to-offline-evaluation.md) 하 여 현재 학습 정책과 비교 합니다.

### <a name="optimize-learning-policies"></a>학습 정책 최적화

Personalizer는 [오프 라인 평가](how-to-offline-evaluation.md)에서 최적화 된 학습 정책을 만들 수 있습니다. Personalizer에서 온라인으로 사용 하는 경우 오프 라인 평가에 더 나은 보상을 주는 최적화 된 학습 정책을 통해 더 나은 결과를 얻을 수 있습니다.

학습 정책을 최적화 한 후에는이 정책을 Personalizer에 직접 적용 하 여 현재 정책을 즉시 바꿀 수 있습니다. 또는 추가 평가를 위해 최적화 된 정책을 저장 하 고 나중에 삭제, 저장 또는 적용할지 여부를 결정할 수 있습니다.
