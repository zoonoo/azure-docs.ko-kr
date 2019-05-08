---
title: 사용자 지정 음성-음성 서비스에 대 한 모델을 학습 합니다.
titlesuffix: Azure Cognitive Services
description: 음성-텍스트를 학습 하는 것은 Microsoft의 기본 모델 또는 만들려면 하려고 계획 중인 사용자 지정 모델 모두에 대 한 인식 정확도 향상 하는 데 필요한 합니다. 사람이 레이블이 지정 된 기록 및 관련된 텍스트를 사용 하 여 모델을 학습 합니다. 이전에 업로드 된 오디오 데이터와 함께 이러한 데이터 집합은 구체화 하 고 단어, 구, 약어, 이름 및 제품 관련 용어를 인식 하는 음성-텍스트 모델을 학습에 사용 됩니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 194ae477bb3cba4ac7e3350da6b793c6fea6ecdb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026719"
---
# <a name="train-a-model-for-custom-speech"></a>사용자 지정 음성 모델을 학습 합니다.

음성-텍스트를 학습 하는 것은 Microsoft의 기본 모델 또는 만들려면 하려고 계획 중인 사용자 지정 모델 모두에 대 한 인식 정확도 향상 하는 데 필요한 합니다. 사람이 레이블이 지정 된 기록 및 관련된 텍스트를 사용 하 여 모델을 학습 합니다. 이전에 업로드 된 오디오 데이터와 함께 이러한 데이터 집합은 구체화 하 고 단어, 구, 약어, 이름 및 제품 관련 용어를 인식 하는 음성-텍스트 모델을 학습에 사용 됩니다. 더 도메인에서 데이터 집합 (하겠습니다 사용자를 인식 하도록 예상 대로 관련 된 데이터)를 제공 하는 보다 정확한 모델 수는 향상 된 인식 결과입니다. 제공 하 여 교육에 관련 없는 데이터를 염두에서에 둡니다, 그리고 줄이거나 모델의 정확도 저하 될 수 있습니다.

## <a name="use-training-to-resolve-accuracy-issues"></a>학습을 사용 하 여 정확성 문제를 해결 하려면

사람이 레이블을 사용 하 여 모델을 인식 문제가 발생할 경우 기록 및 추가 학습에 대 한 관련된 데이터 정확도를 향상 시키는 데 도움이 됩니다. 이 표를 사용 하 여 사용자 문제를 해결 하는 데 사용할 데이터 집합을 결정 합니다.

| 사용 사례 | 데이터 형식 | 데이터 수량 |
|----------|-----------|---------------|
| 적절 한 이름에서 됩니다. | 관련 텍스트 (문장을/utterances) | 500mb 10MB |
| 단어 강조 인해에서 됩니다. | 관련된 텍스트 (발음) | Misrecognized 단어를 제공 합니다. |
| 일반적인 단어 삭제 되거나에서 | 오디오 + 사람이 레이블이 지정 된 내용 | 10 ~ 1000 대 인 기록 시간 |

> [!IMPORTANT]
> 데이터 집합을 업로드 하지 않은 경우를 참조 하세요 [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)입니다. 이 문서에서는 고품질 데이터 집합 만들기에 대 한 지침 및 데이터를 업로드 하는 것에 대 한 지침을 제공 합니다.

## <a name="train-and-evaluate-a-model"></a>학습 및 모델 평가

모델을 학습 하는 첫 번째 단계 학습 데이터를 업로드 하는 것입니다. 사용 하 여 [준비 및 테스트 데이터](how-to-custom-speech-test-data.md) 사람이 레이블이 지정 된 기록 및 관련된 텍스트 (길이 발언 및 발음)를 준비 하기 위한 단계별 지침에 대 한 합니다. 학습 데이터를 업로드 한 후 모델을 학습 하려면 다음이 지침을 따릅니다.

1. 이동할 **음성-텍스트 > 사용자 지정 음성 > 교육**합니다.
2. 클릭 **모델 학습**합니다.
3. 다음으로 교육을 제공 된 **이름** 및 **설명**합니다.
4. **시나리오 및 기준 모델** 드롭 다운 메뉴에서 도메인에 가장 잘 맞는 시나리오를 선택 합니다. 선택 하는 시나리오를 잘 모를 경우 선택할 **일반**합니다. 기준 모델에 학습을 위한 시작 지점입니다. 선호도 없는 경우에 최신을 사용할 수 있습니다.
5. **학습 데이터를 선택** 페이지, 학습에 사용 하려는 하는 하나 또는 여러 개의 오디오 + 사람이 레이블이 지정 된 기록 데이터 집합을 선택 합니다.
6. 학습 완료 되 면 정확도 새로 학습된 된 모델에서 테스트 하는 데 선택할 수 있습니다. 이 단계는 선택 사항입니다.
7. 선택 **만들기** 를 사용자 지정 모델을 작성 합니다.

학습 테이블 모델을 새로 만든이에 해당 하는 새 항목을 표시 합니다. 테이블에는 상태가 표시 됩니다.  처리, 성공, 실패 합니다.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>학습 된 모델의 정확도 평가 합니다.

데이터를 검사 하 고 이러한 문서를 사용 하 여 모델 정확도 평가할 수 있습니다.

* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)


정확도 테스트 하려는 경우 것이 중요 하는 실제 모델의 성능을 파악 하 여 모델을 사용 하 여 사용 된 계정이 다른 어쿠스틱 데이터 집합을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
