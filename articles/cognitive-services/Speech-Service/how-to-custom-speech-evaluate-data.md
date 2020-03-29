---
title: 사용자 지정 음성 - 음성 서비스에 대한 정확도 평가
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 음성-텍스트 모델 또는 사용자 지정 모델의 품질을 정량적으로 측정하는 방법을 배웁니다. 정확도를 테스트하려면 오디오+ 사람이 표시한 전사 데이터가 필요하며, 30분에서 5시간 동안 대표 오디오가 제공되어야 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806099"
---
# <a name="evaluate-custom-speech-accuracy"></a>사용자 지정 음성 정확도 평가

이 문서에서는 Microsoft의 음성 변환 모델 또는 사용자 지정 모델의 품질을 정량적으로 측정하는 방법을 알아봅니다. 정확도를 테스트하려면 오디오+ 사람이 표시한 전사 데이터가 필요하며, 30분에서 5시간 동안 대표 오디오가 제공되어야 합니다.

## <a name="what-is-word-error-rate-wer"></a>워드 오류율(WER)이란 무엇입니까?

모델 정확도를 측정하는 업계 표준은 *WER(워드 오류율)입니다.* WER은 인식 중에 식별된 잘못된 단어의 수를 계산한 다음 인간 레이블이 지정된 성적 증명서에 제공된 총 단어 수로 나눕니다. 마지막으로 이 숫자에 100%를 곱하여 WER을 계산합니다.

![WER 포뮬러](./media/custom-speech/custom-speech-wer-formula.png)

잘못 식별된 단어는 다음 세 가지 범주로 나뉩니다.

* 삽입 (I): 가설 성적 증명서에 잘못 추가된 단어
* 삭제(D): 가설 성적증명서에서 발견되지 않은 단어
* 대체(S): 참조와 가설 간에 대체된 단어

예를 들면 다음과 같습니다.

![잘못 식별된 단어의 예](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>오류 해결 및 WER 개선

기계 인식 결과의 WER을 사용하여 앱, 도구 또는 제품과 함께 사용 중인 모델의 품질을 평가할 수 있습니다. 5%-10%의 WER은 좋은 품질로 간주되며 사용할 준비가 되었습니다. 20%의 WER은 허용되지만 추가 교육을 고려할 수 있습니다. 30% 이상의 WER은 품질이 좋지 않으며 사용자 지정 및 교육이 필요합니다.

오류가 배포되는 방식이 중요합니다. 많은 삭제 오류가 발생하면 일반적으로 오디오 신호 강도가 약하기 때문입니다. 이 문제를 해결하려면 소스에 가까운 오디오 데이터를 수집해야 합니다. 삽입 오류는 오디오가 시끄러운 환경에서 녹음되고 누화가 있을 수 있음을 의미하므로 인식 문제가 발생할 수 있습니다. 대체 오류는 도메인별 용어의 샘플이 부족하여 사람이 레이블이 지정된 전사 또는 관련 텍스트로 제공된 경우가 많습니다.

개별 파일을 분석하여 존재하는 오류 유형과 특정 파일에 고유한 오류를 확인할 수 있습니다. 파일 수준에서 문제를 이해하면 개선 점을 목표로 하는 데 도움이 됩니다.

## <a name="create-a-test"></a>테스트 만들기

Microsoft의 음성-텍스트 기준 모델 또는 학습한 사용자 지정 모델의 품질을 테스트하려면 두 모델을 나란히 비교하여 정확도를 평가할 수 있습니다. 비교에는 WER 및 인식 결과가 포함됩니다. 일반적으로 사용자 지정 모델은 Microsoft의 기준 모델과 비교됩니다.

모델을 나란히 평가하려면 다음을 수행하십시오.

1. 사용자 지정 [음성 포털에 로그인합니다.](https://speech.microsoft.com/customspeech)
2. 사용자 **지정 음성 > 테스트에 > 음성-텍스트로**이동합니다.
3. **테스트 추가를 클릭합니다.**
4. **정확도 평가를**선택합니다. 테스트에 이름, 설명을 지정하고 오디오 + 사람이 레이블이 지정한 전사 데이터 집합을 선택합니다.
5. 테스트할 최대 두 개의 모델을 선택합니다.
6. **만들기**를 클릭합니다.

테스트를 성공적으로 만든 후에는 결과를 나란히 비교할 수 있습니다.

## <a name="side-by-side-comparison"></a>나란히 비교

테스트가 완료되면 *성공으로*상태 변경으로 표시되며 테스트에 포함된 두 모델 모두에 대한 WER 번호를 찾을 수 있습니다. 테스트 세부 정보 페이지를 보려면 테스트 이름을 클릭합니다. 이 세부 정보 페이지에는 데이터 집합의 모든 발언이 나열되어 제출된 데이터 집합의 전사와 함께 두 모델의 인식 결과를 나타냅니다. 나란히 비교를 검사하는 데 도움이 있으므로 삽입, 삭제 및 대체를 비롯한 다양한 오류 유형을 전환할 수 있습니다. 음성을 듣고 각 열의 인식 결과를 비교하여 두 개의 음성-텍스트 모델에 대한 음성 표시 전사 및 결과를 보여 주므로 요구 사항을 충족하는 모델과 추가 교육 및 개선 사항을 결정할 수 있습니다. 필수.

## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
