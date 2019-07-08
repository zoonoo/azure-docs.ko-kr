---
title: 사용자 지정 음성에 대한 데이터 품질 검사 - 음성 서비스
titlesuffix: Azure Cognitive Services
description: 사용자 지정 음성은 오디오 데이터와 해당 인식 결과를 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 사용자 지정 음성 포털에서 업로드된 오디오를 재생하고 제공된 인식 결과가 올바른지 결정할 수 있습니다.  이 도구를 사용하면 모든 오디오 데이터를 기록하지 않고도 학습된 사용자 지정 모델이나 Microsoft의 기본 음성-텍스트 모델의 품질을 신속하게 검사할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 132e355abbdbe147a843c51efd018cc759c06836
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064032"
---
# <a name="inspect-custom-speech-data"></a>사용자 지정 음성 데이터 검사

> [!NOTE]
> [사용자 지정 음성에 대한 테스트 데이터 준비](how-to-custom-speech-test-data.md)를 읽고 검사에 대한 데이터 집합을 업로드했다고 가정합니다.

사용자 지정 음성은 오디오 데이터와 해당 인식 결과를 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 사용자 지정 음성 포털에서 업로드된 오디오를 재생하고 제공된 인식 결과가 올바른지 결정할 수 있습니다. 이 도구를 사용하면 모든 오디오 데이터를 기록하지 않고도 학습된 사용자 지정 모델이나 Microsoft의 기본 음성-텍스트 모델의 품질을 신속하게 검사할 수 있습니다.

이 문서에서는 이전에 업로드한 학습 데이터를 사용하여 모델의 품질을 시각적으로 검사하는 방법을 배웁니다.

이 페이지에서는 Microsoft의 기본 음성-텍스트 모델 및/또는 학습된 사용자 지정 모델의 품질을 시각적으로 검사하는 방법을 배웁니다. 테스트를 위해 **데이터** 탭에 업로드한 데이터를 사용합니다.

## <a name="create-a-test"></a>테스트 만들기

테스트를 만들려면 이러한 지침을 따릅니다.

1. **음성-텍스트 > 사용자 지정 음성 > 테스트**로 이동합니다.
2. **테스트 추가**를 클릭합니다.
3. **품질 검사(오디오 전용 데이터)** 를 선택 합니다. 테스트 이름, 설명을 제공하고 오디오 데이터 집합을 선택합니다.
4. 테스트하려고 하는 최대 두 개의 모델을 선택합니다.
5. **만들기**를 클릭합니다.

테스트를 성공적으로 만든 후에, 모델을 나란히 비교할 수 있습니다.

## <a name="side-by-side-model-comparisons"></a>나란히 모델 비교

테스트 상태가 *Succeeded*가 되면, 테스트의 세부 정보를 보려면 테스트 항목 이름을 클릭합니다. 이 세부 정보 페이지는 데이터 집합의 모든 발언을 나열하며, 제출된 데이터 집합의 기록과 함께 두 모델의 인식 결과를 나타냅니다.

나란히 비교 검사를 위해 삽입, 삭제 및 대체를 비롯한 여러 가지 오류 형식 간 전환할 수 있습니다. 오디오 듣고 각 열의 인식 결과를 비교함으로써(사람 레이블 기록 및 두 가지 음성-텍스트 모델의 결과를 표시), 어떤 모델이 요구를 충족하고는지, 개선이 필요한 점은 무엇인지를 결정할 수 있습니다.

품질 테스트 검사는 음성 인식 끝점의 품질이 응용 프로그램에 충분한지 검증하는 데 유용합니다.  기록된 오디오를 필요로 하는, 정확도의 객관적 측정을 위해서는 [정확도 평가](how-to-custom-speech-evaluate-data.md)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [사용자 지정 음성에 대한 테스트 데이터를 준비합니다.](how-to-custom-speech-test-data.md)
