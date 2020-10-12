---
title: Custom Speech 음성 서비스를 위한 모델 학습
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 모델을 학습 하면 Microsoft의 기본 모델이 나 사용자 지정 모델에 대 한 인식 정확도를 향상 시킬 수 있습니다. 사용자 레이블이 지정 된 텍스트와 관련 텍스트를 사용 하 여 모델을 학습 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: bf9209e0c256412ccb06ea62a197046a7b012e00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84629015"
---
# <a name="train-a-model-for-custom-speech"></a>Custom Speech 모델 학습

음성 텍스트 모델을 학습 하면 Microsoft의 기본 모델에 대 한 인식 정확도를 향상 시킬 수 있습니다. 사용자 레이블이 지정 된 텍스트와 관련 텍스트를 사용 하 여 모델을 학습 합니다. 이러한 데이터 집합은 이전에 업로드 된 오디오 데이터와 함께 음성 텍스트 모델을 구체화 하 고 학습 하는 데 사용 됩니다.

## <a name="use-training-to-resolve-accuracy-issues"></a>학습을 사용 하 여 정확도 문제 해결

모델에 대 한 인식 문제가 발생 하는 경우 추가 교육을 위해 사람이 레이블 지정 된 성적 증명서 및 관련 데이터를 사용 하면 정확도를 향상 시킬 수 있습니다. 이 표를 사용 하 여 문제를 해결 하는 데 사용할 데이터 집합을 결정 합니다.

| 사용 사례 | 데이터 형식 |
| -------- | --------- |
| 의료 용어 또는 IT 전문 용어와 같은 업계 별 어휘 및 문법에 대 한 인식 정확도를 향상 시킵니다. | 관련 텍스트 (문장/길이 발언) |
| 제품 이름 또는 머리글자어와 같이 비표준 발음을 포함 하는 단어 또는 용어의 윗주와 표시 된 형태를 정의 합니다. | 관련 텍스트 (발음) |
| 말하는 스타일, 강조 또는 특정 배경 소음에 대 한 인식 정확도를 향상 시킵니다. | 오디오 + 사람이 레이블 지정 된 성적 증명서 |

> [!IMPORTANT]
> 데이터 집합을 업로드 하지 않은 경우 [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)를 참조 하세요. 이 문서에서는 데이터 업로드에 대 한 지침과 고품질 데이터 집합을 만들기 위한 지침을 제공 합니다.

## <a name="train-and-evaluate-a-model"></a>모델 학습 및 평가

모델을 학습 하는 첫 번째 단계는 학습 데이터를 업로드 하는 것입니다. [데이터 준비 및 테스트](how-to-custom-speech-test-data.md) 를 사용 하 여 사용자에 게 레이블이 지정 된 텍스트 (길이 발언 및 발음)를 준비 하는 단계별 지침을 확인 합니다. 학습 데이터를 업로드 한 후 다음 지침에 따라 모델에 대 한 학습을 시작 합니다.

1. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인 합니다.
2. **음성 텍스트 > Custom Speech > [프로젝트 이름] > 학습**으로 이동 합니다.
3. **모델 학습**을 클릭 합니다.
4. 다음으로, 교육에 **이름** 및 **설명을**제공 합니다.
5. **시나리오 및 기준 모델** 드롭다운 메뉴에서 도메인에 가장 적합 한 시나리오를 선택 합니다. 선택할 시나리오를 잘 모르겠으면 **일반**을 선택 합니다. 기준선 모델은 학습을 위한 시작점입니다. 일반적으로 최신 모델을 선택 하는 것이 가장 좋습니다.
6. 학습 **데이터 선택** 페이지에서 학습에 사용할 오디오 + 사람이 레이블 지정 된 기록 데이터 집합을 하나 또는 여러 개 선택 합니다.
7. 교육이 완료 되 면 새로 학습 된 모델에 대 한 정확도 테스트를 수행 하도록 선택할 수 있습니다. 이 단계는 선택 사항입니다.
8. 만들기를 선택 하 여 사용자 지정 모델을 **만듭니다** .

학습 테이블은 새로 만든이 모델에 해당 하는 새 항목을 표시 합니다. 또한 테이블에는 처리 중, 성공, 실패 됨 상태가 표시 됩니다.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>학습 된 모델의 정확도 평가

이러한 문서를 사용 하 여 데이터를 검사 하 고 모델 정확도를 평가할 수 있습니다.

- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)

정확도를 테스트 하도록 선택한 경우 모델에서 사용 하는 것과 다른 음향 데이터 집합을 선택 하 여 모델의 성능을 사실적으로 파악 하는 것이 중요 합니다.

## <a name="next-steps"></a>다음 단계

- [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 자료

- [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
