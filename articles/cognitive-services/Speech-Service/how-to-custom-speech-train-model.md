---
title: 사용자 지정 음성 - 음성 서비스에 대한 모델 교육
titleSuffix: Azure Cognitive Services
description: 음성-텍스트 모델을 교육하면 Microsoft의 기준 모델 또는 사용자 지정 모델에 대한 인식 정확도를 향상시킬 수 있습니다. 모델은 인간 레이블이 지정된 전사 및 관련 텍스트를 사용하여 학습됩니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137764"
---
# <a name="train-a-model-for-custom-speech"></a>Custom Speech 모델 학습

음성-텍스트 모델을 교육하면 Microsoft의 기준 모델에 대한 인식 정확도를 향상시킬 수 있습니다. 모델은 인간 레이블이 지정된 전사 및 관련 텍스트를 사용하여 학습됩니다. 이러한 데이터 집합은 이전에 업로드된 오디오 데이터와 함께 단어, 구, 머리글자어, 이름 및 기타 제품별 용어를 인식하도록 음성-텍스트 모델을 구체화하고 학습하는 데 사용됩니다. 제공하는 도메인 내 데이터 집합(사용자가 말하는 내용과 인식할 것으로 예상되는 데이터와 관련된 데이터)이 많을수록 모델이 더 정확해인식이 향상됩니다. 관련없는 데이터를 교육에 공급하면 모델의 정확도를 줄이거나 손상시킬 수 있습니다.

## <a name="use-training-to-resolve-accuracy-issues"></a>교육을 사용하여 정확도 문제 해결

모델에 인식 문제가 발생하는 경우 추가 교육을 위해 사람이 표시한 성적 증명서 및 관련 데이터를 사용하면 정확도를 향상시킬 수 있습니다. 이 테이블을 사용하여 문제를 해결하는 데 사용할 데이터 집합을 결정합니다.

| 사용 사례 | 데이터 형식 |
| -------- | --------- |
| 의료 용어 또는 IT 전문 용어와 같은 산업별 어휘 및 문법에 대한 인식 정확도를 향상시킵니다. | 관련 텍스트(문장/발언) |
| 제품 이름 또는 머리글자와 같이 비표준 발음이 있는 단어 또는 용어의 발음 및 표시된 형식을 정의합니다. | 관련 텍스트(발음) |
| 말하기 스타일, 악센트 또는 특정 배경 소음에 대한 인식 정확도를 향상시킵니다. | 오디오 + 사람이 표시한 성적 증명서 |

> [!IMPORTANT]
> 데이터 집합을 업로드하지 않은 경우 [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)를 참조하십시오. 이 문서에서는 데이터 업로드에 대한 지침과 고품질 데이터 집합을 만들기 위한 지침을 제공합니다.

## <a name="train-and-evaluate-a-model"></a>모델 학습 및 평가

모델을 학습하는 첫 번째 단계는 학습 데이터를 업로드하는 것입니다. 준비 및 단계별 지침에 대 한 [데이터를 테스트](how-to-custom-speech-test-data.md) 하 고 인간 레이블이 지정 된 전사 및 관련 텍스트 (발언 및 발음)를 준비 합니다. 교육 데이터를 업로드한 후 다음 지침을 따라 모델 학습을 시작합니다.

1. 사용자 지정 [음성 포털에 로그인합니다.](https://speech.microsoft.com/customspeech)
2. 사용자 **지정 음성 > 교육> 음성-텍스트로**이동합니다.
3. **기차 모형을 클릭합니다.**
4. 다음으로, 교육에 **이름과** **설명을**지정합니다.
5. 시나리오 **및 기준 모델** 드롭다운 메뉴에서 도메인에 가장 적합한 시나리오를 선택합니다. 선택할 시나리오를 잘 모르는 경우 **일반**을 선택합니다. 기준 선은 학습의 시작점입니다. 기본 설정이 없는 경우 최신 을 사용할 수 있습니다.
6. 학습 **데이터 선택** 페이지에서 교육에 사용할 오디오 + 사람이 레이블이 지정한 전사 데이터 집합 중 하나 또는 여러 개를 선택합니다.
7. 교육이 완료되면 새로 학습된 모델에서 정확도 테스트를 수행하도록 선택할 수 있습니다. 이 단계는 선택 사항입니다.
8. 사용자 지정 모델을 빌드하려면 **만들기를** 선택합니다.

교육 테이블에는 새로 만든 모델에 해당하는 새 항목이 표시됩니다. 또한 테이블에는 처리, 성공, 실패 상태도 표시됩니다.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>숙련된 모델의 정확도 평가

다음 문서를 사용하여 데이터를 검사하고 모델 정확도를 평가할 수 있습니다.

- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)

정확도를 테스트하도록 선택한 경우 모델의 성능을 사실적으로 인식하기 위해 모델과 다른 음향 데이터 집합을 선택하는 것이 중요합니다.

## <a name="next-steps"></a>다음 단계

- [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

- [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
