---
title: Custom Speech 음성 서비스에 대 한 정확도 평가
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 음성 텍스트 모델 또는 사용자 지정 모델의 품질을 quantitatively 측정 하는 방법에 대해 알아봅니다. 정확도를 테스트 하려면 오디오 + 사람 레이블이 지정 된 기록 데이터가 필요 하며, 대표 오디오는 30 분에서 5 시간으로 제공 되어야 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806099"
---
# <a name="evaluate-custom-speech-accuracy"></a>Custom Speech 정확도 평가

이 문서에서는 Microsoft의 음성 텍스트 모델 또는 사용자 지정 모델의 품질을 quantitatively 측정 하는 방법에 대해 알아봅니다. 정확도를 테스트 하려면 오디오 + 사람 레이블이 지정 된 기록 데이터가 필요 하며, 대표 오디오는 30 분에서 5 시간으로 제공 되어야 합니다.

## <a name="what-is-word-error-rate-wer"></a>WER (단어 오류 요금) 란?

모델 정확도를 측정 하는 산업 표준은 WER ( *단어 오류 요금* )입니다. WER은 인식 중에 식별 된 잘못 된 단어 수를 계산 하 고, 사용자 레이블 성적 증명서에 제공 된 단어의 총 수로 나눕니다. 마지막으로,이 숫자에는 WER를 계산 하기 위해 100%가 곱해집니다.

![WER 수식](./media/custom-speech/custom-speech-wer-formula.png)

잘못 식별 된 단어는 다음과 같은 세 가지 범주로 분류 됩니다.

* 삽입 (I): 가설 성적 증명서에 잘못 추가 된 단어
* 삭제 (D): 가설 성적 증명서에서 감지 되지 않은 단어
* 대체 (S): 참조와 가설 사이에서 대체 된 단어입니다.

아래 예를 살펴보세요.

![잘못 식별 된 단어의 예](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>오류 해결 및 WER 향상

컴퓨터 인식 결과에서 WER를 사용 하 여 앱, 도구 또는 제품에서 사용 하는 모델의 품질을 평가할 수 있습니다. 5%-10%의 WER은 좋은 품질로 간주 되 고 사용할 준비가 된 것으로 간주 됩니다. WER의 20%가 허용 되지만 추가 교육을 고려해 야 할 수 있습니다. WER이 30% 이상인 경우 품질이 저하 되며 사용자 지정 및 교육이 필요 합니다.

오류가 어떻게 배포 되는지는 중요 합니다. 많은 삭제 오류가 발생 한 경우 일반적으로 약한 오디오 신호 강도로 인 한 것입니다. 이 문제를 해결 하려면 오디오 데이터를 원본에 더 가깝게 수집 해야 합니다. 삽입 오류는 오디오가 소음 환경에 기록 되 고 crosstalk 있을 수 있으므로 인식 문제가 발생 했음을 의미 합니다. 대체 오류는 도메인별 용어에 대 한 충분 한 샘플이 사람이 레이블이 지정 된 텍스트 또는 관련 텍스트로 제공 되지 않은 경우에 종종 발생 합니다.

개별 파일을 분석 하 여 존재 하는 오류 유형과 특정 파일에 고유한 오류를 확인할 수 있습니다. 파일 수준에서 문제를 이해 하면 향상 된 기능을 대상으로 할 수 있습니다.

## <a name="create-a-test"></a>테스트 만들기

Microsoft의 음성 텍스트 기준선 모델 또는 학습 된 사용자 지정 모델의 품질을 테스트 하려는 경우 두 모델을 나란히 비교 하 여 정확도를 평가할 수 있습니다. 이 비교에는 WER 및 인식 결과가 포함 됩니다. 일반적으로 사용자 지정 모델은 Microsoft의 기본 모델과 비교 됩니다.

모델을 나란히 평가 하려면:

1. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인 합니다.
2. **음성 텍스트 > Custom Speech > 테스트**로 이동 합니다.
3. **테스트 추가**를 클릭 합니다.
4. **정확도 평가**를 선택 합니다. 테스트 이름, 설명을 지정 하 고 오디오 + 사람 레이블이 지정 된 기록 데이터 집합을 선택 합니다.
5. 테스트 하려는 최대 2 개의 모델을 선택 합니다.
6. **만들기**를 클릭합니다.

테스트를 성공적으로 만든 후 결과를 나란히 비교할 수 있습니다.

## <a name="side-by-side-comparison"></a>병렬 비교

테스트가 완료 되 면 상태가 *성공*으로 변경 되 면 테스트에 포함 된 두 모델 모두에 대해 WER 숫자가 표시 됩니다. 테스트 이름을 클릭 하 여 테스트 세부 정보 페이지를 표시 합니다. 이 세부 정보 페이지에는 데이터 집합의 모든 길이 발언 나열 되며 제출 된 데이터 집합의 기록을 함께 두 모델의 인식 결과를 나타냅니다. 병렬 비교를 검사 하기 위해 삽입, 삭제 및 대체를 비롯 한 다양 한 오류 유형을 전환할 수 있습니다. 오디오를 수신 하 고 두 개의 음성-텍스트 모델에 대 한 결과와 사람이 레이블이 지정 된 기록을 보여 주는 각 열에서 인식 결과를 비교 하 여 요구 사항을 충족 하는 모델과 추가 학습 및 향상이 필요한 모델을 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
