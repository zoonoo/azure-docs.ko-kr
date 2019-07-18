---
title: 문제 해결-Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서의 문제 해결 Personalizer에 대 한 질문을 찾을 수 있습니다.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722257"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 문제 해결

이 문서에서는 Personalizer에 대 한 문제 해결 질문과 대답을 포함합니다.

## <a name="learning-loop"></a>Learning 루프

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Learning 루프에 알아봅니다 같지 않습니다. 이 수정 하는 방법

Learning 루프 순위 호출 효과적으로 우선 순위 지정 하기 전에 몇 천 Reward 호출 해야 합니다. 

Learning 루프 현재 작동 하는 방법에 대 한 확실 하지 않은, 경우 실행 하는 [오프 라인 평가](concepts-offline-evaluation.md), 수정 된 학습 정책을 적용 하 고 합니다. 

## <a name="next-steps"></a>다음 단계

[모델 업데이트 빈도 구성 합니다.](how-to-settings.md#model-update-frequency)