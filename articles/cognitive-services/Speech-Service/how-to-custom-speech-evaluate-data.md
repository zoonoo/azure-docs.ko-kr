---
title: 사용자 지정 음성-음성 서비스에 대 한 정확도 평가 합니다.
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 Microsoft의 음성-텍스트 모델 또는 사용자 지정 모델의 품질을 측정 하는 수량 적으로 하는 방법을 배웁니다. 오디오 + 사람이 레이블이 지정 된 기록 데이터 정확도 테스트 하는 데 필요 하 고 대표적인 오디오 5 시간 30 분을 제공 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026727"
---
# <a name="evaluate-custom-speech-accuracy"></a>사용자 지정 음성 정확도 평가 합니다.

이 문서에서는 Microsoft의 음성-텍스트 모델 또는 사용자 지정 모델의 품질을 측정 하는 수량 적으로 하는 방법에 알아봅니다. 오디오 + 사람이 레이블이 지정 된 기록 데이터 정확도 테스트 하는 데 필요 하 고 대표적인 오디오 5 시간 30 분을 제공 합니다.

## <a name="what-is-word-error-rate-wer"></a>Word 오류 속도 (WER) 란?

업계 표준 모델 정확도 측정 하는 *Word 오류율* (WER). WER 인식 하는 동안 확인 된 잘못 된 단어의 개수를 사람이 레이블이 지정 된 대 본에 제공 된 단어의 총 수로 나눕니다. 마지막으로, 해당 수를 100%는 WER을 계산 하려면 곱합니다.

![WER 수식](./media/custom-speech/custom-speech-wer-formula.png)

세 가지 범주로 단어 fall 잘못 식별 합니다.

* (I) 삽입 합니다. 가설 대 본에 잘못 추가 되는 단어
* 삭제 (D): 가설 대 본에서 검색 되지 않는 단어
* 대체 (S): 참조 및 가설 간의 대체 된 단어

예를 들면 다음과 같습니다.

![잘못 식별 된 단어의 예](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>오류를 해결 하 고 WER 개선

앱, 도구 또는 제품을 사용 하 여 사용 하는 모델의 품질을 평가 하려면 machine 인식 결과에서 WER을 사용할 수 있습니다. 5% ~ 10%는 WER 양호한 것으로 간주 됩니다 하 고 사용할 준비가 되었습니다. 20%의 WER 허용 인데 추가 교육 시킬 수도 있습니다. 30% 이상의 WER 품질이 신호를 보내고 사용자 지정 및 학습에 필요 합니다.

오류를 어떻게 분산 하는 것이 중요 합니다. 많은 삭제 오류가 발생 하는 경우에 일반적으로 약한 오디오 신호 강도 때문에 있습니다. 이 문제를 해결 하려면 원본에 더 가깝게 오디오 데이터를 수집 해야 합니다. 삽입 오류 시끄러운 환경에서 오디오를 녹음 하 혼선 있을 수를 인식 문제를 의미 합니다. 도메인별 용어의 부족 하 여 예제를 사람이 레이블이 지정 된 기록으로 제공 되거나 관련 된 경우 대체 오류가 종종 발생 텍스트입니다.

개별 파일을 분석 하 여 어떤 유형의 오류가 존재 하며 오류는 특정 파일에 고유한 값을 확인할 수 있습니다. 파일 수준에서 문제를 이해 하는 데 도움이 됩니다 향상 대상으로 합니다.

## <a name="create-a-test"></a>테스트 만들기

학습 된 사용자 지정 모델 또는 Microsoft의 음성-텍스트 기준선 모델의 품질을 테스트 하려는 경우 두 가지 모델 정확도 평가를 나란히 비교할 수 있습니다. WER 및 인식 결과 포함 하는 비교 합니다. 일반적으로 사용자 지정 모델은 Microsoft의 기준 모델을 사용 하 여 비교 됩니다.

병렬 모델 평가:

1. 이동할 **음성-텍스트 > 사용자 지정 음성 > 테스트**합니다.
2. 클릭 **테스트 추가**합니다.
3. 선택 **정확도 평가**합니다. 테스트 이름, 설명, 제공 및 오디오 + 사람이 레이블이 지정 된 기록 데이터 집합을 선택 합니다.
4. 테스트 하려는 하는 최대 두 개의 모델을 선택 합니다.
5. **만들기**를 클릭합니다.

테스트에 성공적으로 생성 된 후 결과 나란히 비교할 수 있습니다.

## <a name="side-by-side-comparison"></a>Side by side 비교

테스트가 완료 되 면 상태 변경에 나타난 *Succeeded*, 테스트에 포함 된 두 모델에 대 한 WER 숫자를 찾을 수 있습니다. 테스트 세부 정보 페이지를 볼 테스트 이름을 클릭 합니다. 이 세부 정보 페이지에 제출 된 데이터 집합에서 기록 함께 두 모델의 인식 결과 나타내는 데이터 집합에 모든 길이 발언을 나열 합니다. Side-by-side-비교 검사를 위해 삽입, 삭제 및 대체를 비롯 한 여러 가지 오류 형식을 전환할 수 있습니다. 오디오 수신 대기 하 고 사람이 레이블이 기록을 보여 주는 각 열에서 인식 결과 및 두 가지 음성-텍스트 모델에 대 한 결과 비교 하 여 모델 요구 사항을 충족 하 고 추가 학습 및 향상 되 결정할 수 있습니다. 필수.

## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
