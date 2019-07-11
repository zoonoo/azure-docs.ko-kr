---
title: 사용자 지정 음성-음성 서비스에 대한 정확도를 평가합니다.
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 Microsoft의 음성-텍스트 모델 또는 사용자 지정 모델의 품질을 정량적으로 측정하는 방법을 알아봅니다. 오디오 + 사람 레이블 기록 데이터가 정확도를 테스트하는 데 필요하며 30분에서 5시간의 대표적인 오디오를 제공해야 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2e9818fad9a0b5d04cc50a293b16d838c319dd86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606564"
---
# <a name="evaluate-custom-speech-accuracy"></a>사용자 지정 음성 정확도 평가

이 문서에서는 Microsoft의 음성-텍스트 모델 또는 사용자 지정 모델의 품질을 측정 하는 수량 적으로 하는 방법에 알아봅니다. 오디오 + 사람 레이블 기록 데이터가 정확도를 테스트하는 데 필요하며 30분에서 5시간의 대표적인 오디오를 제공해야 합니다.

## <a name="what-is-word-error-rate-wer"></a>WER(Word Error Rate, 단어 오류율)이란?

모델 정확도를 측정하는 업계 표준은 *Word Error Rate*(WER)입니다. WER은 인식하는 동안 확인된 잘못된 단어의 수를 센 다음, 사람 레이블 기록에서 제공된 단어의 총 수로 나눕니다. 마지막으로, 해당 수를 100%로 곱하여 WER를 계산합니다.

![WER 수식](./media/custom-speech/custom-speech-wer-formula.png)

잘못 식별된 단어는 세 가지 범주로 나뉩니다.

* (I) 삽입 합니다. 가설 대 본에 잘못 추가 되는 단어
* 삭제 (D): 가설 대 본에서 검색 되지 않는 단어
* 대체 (S): 참조 및 가설 간의 대체 된 단어

예를 들면 다음과 같습니다.

![잘못 식별 된 단어의 예](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>오류 해결 및 WER 개선

앱, 도구 또는 제품에서 사용하는 모델의 품질을 평가하기 위해 기계 인식 결과에서 WER을 사용할 수 있습니다. 5%~10%의 WER는 양호한 것으로 간주되고 사용할 준비가 된 것입니다. 20%의 WER는 허용되지만 추가 학습을 고려할 수도 있습니다. 30% 이상의 WER은 품질이 낮음을 나타내고 사용자 지정 및 학습이 필요합니다.

오류가 어떻게 분포되어 있는가는 중요합니다. 많은 삭제 오류가 발생하는 것은 일반적으로 약한 오디오 신호 강도 때문입니다. 이 문제를 해결하려면 소스와 더 가까운 곳에서 오디오 데이터를 수집해야 합니다. 삽입 오류는 시끄러운 환경에서 오디오를 녹음하여 혼선으로 인식 문제가 발생했을 수 있음을 의미합니다. 사람 레이블 기록이나 관련된 텍스트로 제공된 경우 모두, 도메인별 용어에 대한 샘플이 부족한 경우에는 대체 오류가 종종 발생합니다.

개별 파일을 분석하여, 어떤 유형의 오류가 존재하고 특정 파일에 고유한 오류는 무엇인가를 판별할 수 있습니다. 파일 수준에서 문제를 이해하는 것은 개선점을 정하는 데 도움이 됩니다.

## <a name="create-a-test"></a>테스트 만들기

학습된 사용자 지정 모델 또는 Microsoft의 음성-텍스트 기본 모델의 품질을 테스트하려는 경우, 정확도를 평가하기 위해 두 가지 모델을 나란히 비교할 수 있습니다. 해당 비교에는 WER 및 인식 결과가 포함됩니다. 일반적으로, 사용자 지정 모델은 Microsoft의 기준 모델과 비교됩니다.

모델을 나란히 평가하려면:

1. **음성-텍스트 > 사용자 지정 음성 > 테스트**로 이동합니다.
2. **테스트 추가**를 클릭합니다.
3. **정확도 평가**를 선택합니다. 테스트 이름, 설명을 제공하고 사용자의 오디오 + 사람 레이블 기록 데이터 집합을 선택합니다.
4. 테스트 하려고 하는 최대 두 개의 모델을 선택합니다.
5. **만들기**를 클릭합니다.

테스트가 성공적으로 생성된 후, 결과를 나란히 비교할 수 있습니다.

## <a name="side-by-side-comparison"></a>나란히 비교

테스트가 완료 되 면 상태 변경에 나타난 *Succeeded*, 테스트에 포함 된 두 모델에 대 한 WER 숫자를 찾을 수 있습니다. 테스트 세부 정보 페이지를 볼 테스트 이름을 클릭 합니다. 이 세부 정보 페이지는 데이터 집합의 모든 발언을 나열하며, 제출된 데이터 집합의 기록과 함께 두 모델의 인식 결과를 나타냅니다. 나란히 비교 검사를 위해 삽입, 삭제 및 대체를 비롯한 여러 가지 오류 형식 간 전환할 수 있습니다. 오디오 수신 대기 하 고 사람이 레이블이 기록을 보여 주는 각 열에서 인식 결과 및 두 가지 음성-텍스트 모델에 대 한 결과 비교 하 여 모델 요구 사항을 충족 하 고 추가 학습 및 향상 되 결정할 수 있습니다. 필수.

## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 자료

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
