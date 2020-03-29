---
title: 사용자 지정 음성 - 음성 서비스에 대한 데이터 품질 검사
titleSuffix: Azure Cognitive Services
description: 사용자 지정 음성은 오디오 데이터를 해당 인식 결과와 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 업로드된 오디오를 재생하고 제공된 인식 결과가 올바른지 확인할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806082"
---
# <a name="inspect-custom-speech-data"></a>사용자 지정 음성 데이터 검사

> [!NOTE]
> 이 [페이지에서는 사용자 지정 음성에 대한 테스트 데이터 준비를](how-to-custom-speech-test-data.md) 읽고 검사를 위해 데이터 집합을 업로드했다고 가정합니다.

사용자 지정 음성은 오디오 데이터를 해당 인식 결과와 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 사용자 [지정 음성 포털에서](https://speech.microsoft.com/customspeech)업로드된 오디오를 재생하고 제공된 인식 결과가 올바른지 확인할 수 있습니다. 이 도구를 사용하면 오디오 데이터를 전사할 필요 없이 Microsoft의 기본 음성 변환 모델 또는 학습된 사용자 지정 모델의 품질을 빠르게 검사할 수 있습니다.

이 문서에서는 이전에 업로드한 교육 데이터를 사용하여 모델의 품질을 시각적으로 검사하는 방법을 배웁니다.

이 페이지에서는 Microsoft의 기본 음성 변환 모델 및/또는 학습한 사용자 지정 모델의 품질을 시각적으로 검사하는 방법을 알아봅니다. 데이터 **탭에** 업로드한 데이터를 테스트에 사용합니다.

## <a name="create-a-test"></a>테스트 만들기

다음 지침을 따라 테스트를 만듭니다.

1. 사용자 지정 [음성 포털에 로그인합니다.](https://speech.microsoft.com/customspeech)
2. 사용자 **지정 음성 > 테스트에 > 음성-텍스트로**이동합니다.
3. **테스트 추가를 클릭합니다.**
4. **품질 검사(오디오 전용 데이터)를**선택합니다. 테스트에 이름, 설명을 지정하고 오디오 데이터 집합을 선택합니다.
5. 테스트할 최대 두 개의 모델을 선택합니다.
6. **만들기**를 클릭합니다.

테스트를 성공적으로 만든 후에는 모델을 나란히 비교할 수 있습니다.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>나란히 모델 비교

테스트 상태가 _성공하면_테스트 항목 이름을 클릭하여 테스트의 세부 정보를 확인합니다. 이 세부 정보 페이지에는 데이터 집합의 모든 발언이 나열되어 제출된 데이터 집합의 전사와 함께 두 모델의 인식 결과를 나타냅니다.

나란히 비교를 검사하는 데 도움이 있으므로 삽입, 삭제 및 대체를 비롯한 다양한 오류 유형을 전환할 수 있습니다. 오디오를 듣고 각 열의 인식 결과를 비교하여(사람이 레이블이 붙은 전사와 두 개의 음성-텍스트 모델의 결과를 표시함) 사용자의 요구를 충족하는 모델과 개선이 필요한 위치를 결정할 수 있습니다.

품질 테스트를 검사하는 것은 음성 인식 끝점의 품질이 응용 프로그램에 충분한지 확인하는 데 유용합니다. 전사된 오디오가 필요한 정확도의 객관적인 측정을 위해 [정확도 평가에](how-to-custom-speech-evaluate-data.md)있는 지침을 따르십시오.

## <a name="next-steps"></a>다음 단계

- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
- [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

- [사용자 지정 음성에 대한 테스트 데이터 준비](how-to-custom-speech-test-data.md)
