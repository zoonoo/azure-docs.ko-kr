---
title: 끝점 발화를 검토하여 LUIS(Language Understanding)에서 활성 학습 사용 - Azure| Microsoft Docs
description: ‘끝점 발화 검토’라는 활성 학습 기능을 사용하여 성능 예측을 더 빠르게 개선합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: 05b3404d318359c6966df44bfab9baff3ded980f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222616"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>끝점 발화를 검토하여 활성 학습을 사용하도록 설정합니다.
활성 학습은 예측 정확도를 향상시키는 세 가지 전략 중 하나이며 구현하기에 가장 쉽습니다. 

## <a name="what-is-active-learning"></a>활성 학습의 개념
활성 학습은 2단계 프로세스입니다. 먼저 LUIS는 유효성 검사가 필요한 앱의 끝점에서 수신하는 발화를 선택합니다. 두 번째 단계는 앱 소유자 또는 협력자가 올바른 의도 및 의도 내의 모든 엔터티를 포함하여 [검토](luis-how-to-review-endoint-utt.md)를 위해 선택한 발화의 유효성을 검사하는 것입니다. 발화를 검토한 후 다시 앱을 학습시키고 게시합니다. 

## <a name="which-utterances-are-on-the-review-list"></a>검토 목록에 있는 발화
상위 실행 의도의 점수가 낮거나 상위 두 개 의도의 점수가 너무 근접하면 LUIS는 검토 목록에 발화를 추가합니다. 

## <a name="single-pool-for-utterances-per-app"></a>앱당 발언을 위한 단일 풀
**끝점 발언 검토** 목록은 버전에 따라 달라지지 않습니다. 적극적으로 편집 중인 발언의 버전이나 엔드포인트에서 게시된 앱 버전에 관계없이 검토할 단일 발언 풀이 있습니다. 

## <a name="where-are-the-utterances-from"></a>발화를 가져오는 위치
끝점 발화는 응용 프로그램 HTTP 끝점의 최종 사용자 쿼리에서 가져옵니다. 앱이 게시되지 않았거나 아직 적중을 받지 않은 경우에는 검토할 발화가 없습니다. 특정 의도 또는 엔터티에 대한 끝점 적중이 수신되지 않은 경우에는 검토할 해당 적중이 포함된 발화가 없습니다. 

## <a name="schedule-review-periodically"></a>주기적으로 검토 예약
제안된 발화를 매일 검토할 필요는 없지만 정기적인 LUIS 유지 관리에 포함해야 합니다. 

## <a name="delete-review-items-programmatically"></a>프로그래밍 방식으로 검토 항목 삭제
앱이 큰 경우 일부 발화를 검토하고 목록에서 나머지 부분을 프로그래밍 방식으로 삭제하도록 선택할 수 있습니다. 이 작업을 수행하려면 먼저 목록을 [가져온](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) 다음, ID별로 발화를 [삭제](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)합니다.

## <a name="next-steps"></a>다음 단계

* 끝점 발화를 [검토](luis-how-to-review-endoint-utt.md)하는 방법 알아보기
