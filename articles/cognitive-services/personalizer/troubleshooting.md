---
title: 문제 해결-Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer에 대 한 문제 해결 질문은이 문서에서 찾을 수 있습니다.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663675"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 문제 해결

이 문서에는 Personalizer에 대 한 자주 묻는 질문에 대 한 답변이 포함 되어 있습니다.

## <a name="learning-loop"></a>학습 루프

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>학습 루프가 학습 되지 않는 것 같습니다. 어떻게 해결할 수 있나요?

학습 루프는 순위 호출에 효과적으로 우선 순위를 지정 하기 전에 몇 천 개의 보상 호출이 필요 합니다. 

현재 학습 루프가 어떻게 동작 하는지 확실 하지 않은 경우 [오프 라인 평가](concepts-offline-evaluation.md)를 실행 하 고 수정 된 학습 정책을 적용 합니다. 

## <a name="next-steps"></a>다음 단계

[모델 업데이트 빈도 구성](how-to-settings.md#model-update-frequency)