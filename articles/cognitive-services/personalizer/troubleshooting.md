---
title: 문제 해결-Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Personalizer에 대 한 자주 묻는 질문에 대 한 답변이 포함 되어 있습니다.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953171"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 문제 해결

이 문서에는 Personalizer에 대 한 자주 묻는 질문에 대 한 답변이 포함 되어 있습니다.

## <a name="learning-loop"></a>학습 루프

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>학습 루프가 학습 되지 않는 것 같습니다. 어떻게 할까요? 문제를 해결 하 시겠습니까?

학습 루프는 순위 호출에 효과적으로 우선 순위를 지정 하기 전에 몇 천 개의 보상 호출이 필요 합니다. 

현재 학습 루프가 어떻게 동작 하는지 확실 하지 않은 경우 [오프 라인 평가](concepts-offline-evaluation.md)를 실행 하 고 수정 된 학습 정책을 적용 합니다. 

## <a name="next-steps"></a>다음 단계

[모델 업데이트 빈도 구성](how-to-settings.md#model-update-frequency)