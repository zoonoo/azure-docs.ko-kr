---
title: Custom Speech에 대 한 데이터 품질 검사-음성 서비스
titleSuffix: Azure Cognitive Services
description: Custom Speech는 오디오 데이터를 해당 하는 인식 결과와 비교 하 여 모델의 인식 품질을 시각적으로 검사 하는 데 사용할 수 있는 도구를 제공 합니다. 업로드 한 오디오를 재생 하 고 제공 된 인식 결과가 올바른지 확인할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 7a8fec876556d943d29756a38ffc27ae8095e3c4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466241"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech 데이터 검사

> [!NOTE]
> 이 페이지에서는 [Custom Speech에 대 한 준비 테스트 데이터](how-to-custom-speech-test-data.md) 를 읽고 검사를 위해 데이터 집합을 업로드 했다고 가정 합니다.

Custom Speech는 오디오 데이터를 해당 하는 인식 결과와 비교 하 여 모델의 인식 품질을 시각적으로 검사 하는 데 사용할 수 있는 도구를 제공 합니다. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에서 업로드 된 오디오를 재생 하 고 제공 된 인식 결과가 올바른지 확인할 수 있습니다. 이 도구를 사용 하면 Microsoft의 기준선 음성 텍스트 모델의 품질을 검사 하거나 학습 된 사용자 지정 모델을 검사 하거나 두 모델을 기준으로 기록을 비교할 수 있습니다.

이 문서에서는 이전에 업로드 한 학습 데이터를 사용 하 여 모델의 품질을 시각적으로 검사 하는 방법을 배웁니다.

이 페이지에서는 Microsoft의 기본 음성 텍스트 모델 및/또는 학습 한 사용자 지정 모델의 품질을 시각적으로 검사 하는 방법을 알아봅니다. 테스트를 위해 **데이터** 탭에 업로드 한 데이터를 사용 합니다.

## <a name="create-a-test"></a>테스트 만들기

테스트를 만들려면 다음 지침을 따르세요.

1. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인 합니다.
2. **음성 텍스트 > Custom Speech > 테스트**로 이동 합니다.
3. **테스트 추가**를 클릭 합니다.
4. **품질 검사 (오디오 전용 데이터)** 를 선택 합니다. 테스트에 이름, 설명을 지정 하 고 오디오 데이터 집합을 선택 합니다.
5. 테스트 하려는 최대 2 개의 모델을 선택 합니다.
6. **만들기**를 클릭합니다.

테스트를 성공적으로 만든 후 모델에서 지정한 오디오 데이터 집합을 speech 두 모델의 결과를 나란히 비교 하는 방법을 확인할 수 있습니다.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>병렬 모델 비교

테스트 상태가 _성공_이면 테스트 항목 이름을 클릭 하 여 테스트에 대 한 세부 정보를 확인 합니다. 이 세부 정보 페이지에는 데이터 집합의 모든 길이 발언 나열 되며 비교 하는 두 모델의 인식 결과가 표시 됩니다.

병렬 비교를 검사 하기 위해 삽입, 삭제 및 대체를 비롯 한 다양 한 오류 유형을 전환할 수 있습니다. 오디오를 수신 하 고 각 열에서 인식 결과를 비교 하 여 (사람의 레이블이 지정 된 기록 및 두 개의 음성-텍스트 모델의 결과를 표시) 요구 사항을 충족 하는 모델과 향상 된 기능을 결정 하는 데 도움이 됩니다.

병렬 모델 테스트는 응용 프로그램에 가장 적합 한 음성 인식 모델의 유효성을 검사 하는 데 유용 합니다. Transcribed audio를 필요로 하는 정확도의 목표 측정값을 확인 하려면 [정확도 평가](how-to-custom-speech-evaluate-data.md)에 있는 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
- [모델 개선](how-to-custom-speech-improve-accuracy.md)
- [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

- [Custom Speech에 대 한 테스트 데이터 준비](how-to-custom-speech-test-data.md)
