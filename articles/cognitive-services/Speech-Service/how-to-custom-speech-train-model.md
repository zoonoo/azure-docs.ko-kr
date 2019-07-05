---
title: 사용자 지정 음성-음성 서비스에 대한 모델 학습
titlesuffix: Azure Cognitive Services
description: 음성-텍스트를 학습시키는 것은 Microsoft의 기본 모델 또는 만들려고 계획 중인 사용자 지정 모델 모두에 대한 인식 정확도를 향상하는 데 필요합니다. 사람 레이블 기록 및 관련된 텍스트를 사용하여 모델을 학습시킵니다. 이전에 업로드된 오디오 데이터와 함께 이러한 데이터 집합은 단어, 구, 약어, 이름 및 제품 관련 용어를 인식하는 음성-텍스트 모델을 학습시키고 다듬는 데 사용됩니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: e12cef052db6aabad94b47283eda11f60f3b2b13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063929"
---
# <a name="train-a-model-for-custom-speech"></a>사용자 지정 음성 모델 학습

음성-텍스트를 학습시키는 것은 Microsoft의 기본 모델 또는 만들려고 계획 중인 사용자 지정 모델 모두에 대한 인식 정확도를 향상하는 데 필요합니다. 사람 레이블 기록 및 관련된 텍스트를 사용하여 모델을 학습시킵니다. 이전에 업로드된 오디오 데이터와 함께 이러한 데이터 집합은 단어, 구, 약어, 이름 및 제품 관련 용어를 인식하는 음성-텍스트 모델을 학습시키고 다듬는 데 사용됩니다. 제공하는 도메인 내부 데이터 집합(사용자가 말할 내용과 인식될 것으로 기대하는 관련된 데이터)이 많을수록 모델이 더 정확해져, 인식 결과가 향상됩니다. 관련 없는 데이터를 학습에 추가하게 되면 모델의 정확도를 저하하거나 해칠 수 있다는 것을 명심해야 합니다.

## <a name="use-training-to-resolve-accuracy-issues"></a>정확도 문제를 해결하기 위해 학습 사용

모델에서 인식 문제가 발생할 경우, 추가 학습에 대한 사람 레이블 기록과 관련 데이터를 사용하면 정확도를 향상시키는 데 도움이 됩니다. 이 표를 사용하여 문제를 해결하는 데 사용할 데이터 집합을 결정합니다.

| 사용 사례 | 데이터 형식 | 데이터 수량 |
|----------|-----------|---------------|
| 적절 한 이름에서 됩니다. | 관련된 텍스트 (문장을/utterances) | 500mb 10MB |
| 액센트 때문에 단어가 잘못 인식됩니다. | 관련된 텍스트(발음) | 잘못 인식된 단어를 제공합니다. |
| 일반적인 단어가 삭제되거나 잘못 인식됩니다. | 오디오 + 사람 레이블 기록 | 10 ~ 1000 기록 시간 |

> [!IMPORTANT]
> 데이터 집합을 업로드하지 않은 경우, [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)를 참조하세요. 이 문서에서는 데이터를 업로드하는 것에 대한 지침 및 고품질 데이터 집합 만들기에 대한 지침을 제공합니다.

## <a name="train-and-evaluate-a-model"></a>학습 및 모델 평가

모델을 학습시키는 첫 번째 단계는 학습 데이터를 업로드하는 것입니다. 사람 레이블 기록과 관련된 테스트를 준비하기 위한 단계별 지침으로 [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)를 사용합니다. 학습 데이터를 업로드한 후, 모델을 학습시키려면 다음 지침을 따릅니다.

1. 이동할 **음성-텍스트 > 사용자 지정 음성 > 교육**합니다.
2. **모델 학습**을 클릭합니다.
3. 다음으로 학습 **이름** 및 **설명**을 제공합니다.
4. **시나리오 및 기준 모델** 드롭 다운 메뉴에서 도메인에 가장 잘 맞는 시나리오를 선택합니다. 선택할 시나리오를 잘 모를 경우, **일반**을 선택합니다. 기준 모델은 학습을 위한 시작 지점입니다. 선호하는 것이 없는 경우, 최신을 사용할 수 있습니다.
5. **학습 데이터 선택** 페이지에서, 학습에 사용하려는 하나 이상의 오디오 + 사람 레이블 기록 데이터 집합을 선택합니다.
6. 학습이 완료되면 새로 학습된 모델에서 정확도 테스트를 수행하는 것을 선택할 수 있습니다. 이 단계는 선택 사항입니다.
7. **만들기**를 선택하여 사용자 지정 모델을 작성합니다.

학습 테이블 모델을 새로 만든이에 해당 하는 새 항목을 표시 합니다. 테이블에는 상태가 표시 됩니다.  처리, 성공, 실패 합니다.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>학습된 모델의 정확도 평가

다음 문서를 사용하여 데이터를 검사하고 모델 정확도를 평가할 수 있습니다.

* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)


정확도를 테스트하려는 경우, 실제 모델의 성능을 파악할 수 있도록 모델에서 사용한 것과 다른 음향 데이터 집합을 선택하는 것이 중요합니다.

## <a name="next-steps"></a>다음 단계

* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
