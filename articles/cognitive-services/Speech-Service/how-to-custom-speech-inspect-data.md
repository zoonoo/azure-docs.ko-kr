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
ms.date: 09/23/2020
ms.author: erhopf
ms.openlocfilehash: c55d69e99715a0c646c4e836df06cf105f9770bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319128"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech 데이터 검사

> [!NOTE]
> 이 페이지에서는 [Custom Speech에 대 한 준비 테스트 데이터](how-to-custom-speech-test-data.md) 를 읽고 검사를 위해 데이터 집합을 업로드 했다고 가정 합니다.

Custom Speech는 오디오 데이터를 해당 하는 인식 결과와 비교 하 여 모델의 인식 품질을 시각적으로 검사 하는 데 사용할 수 있는 도구를 제공 합니다. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에서 업로드 된 오디오를 재생 하 고 제공 된 인식 결과가 올바른지 확인할 수 있습니다. 이 도구를 사용 하면 Microsoft의 기준선 음성 텍스트 모델의 품질을 검사 하거나 학습 된 사용자 지정 모델을 검사 하거나 두 모델을 기준으로 기록을 비교할 수 있습니다.

이 문서에서는 Microsoft의 기본 음성 텍스트 모델 및/또는 학습 한 사용자 지정 모델의 품질을 시각적으로 검사 하는 방법에 대해 알아봅니다. 온라인 기록 편집기를 사용 하 여 레이블이 지정 된 오디오 데이터 집합을 만들고 구체화 하는 방법에 대해서도 알아봅니다.

## <a name="create-a-test"></a>테스트 만들기

테스트를 만들려면 다음 지침을 따르세요.

1. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인 합니다.
2. **음성 텍스트 > Custom Speech > [프로젝트 이름] > 테스트**로 이동 합니다.
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

## <a name="online-transcription-editor"></a>온라인 기록 편집기

온라인 기록 편집기를 사용 하면 Custom Speech에서 오디오를 쉽게 사용할 수 있습니다. 편집기의 주요 사용 사례는 다음과 같습니다. 

* 오디오 데이터만 있지만 모델 학습에 사용 하기 위해 처음부터 정확한 오디오 + 사람 레이블 데이터 집합을 빌드 하려고 합니다.
* 오디오 + 사람이 레이블 지정 된 데이터 집합이 이미 있지만 기록에 오류 또는 결함이 있습니다. 편집기를 사용 하면 신속 하 게 학습 정확도를 얻기 위해 신속 하 게 신속 하 게 수정할 수 있습니다.

기록 편집기를 사용 하는 유일한 요구 사항은 오디오 데이터 (오디오 전용 또는 오디오 + 기록)를 업로드 하는 것입니다.

### <a name="import-datasets-to-editor"></a>편집기로 데이터 집합 가져오기

편집기로 데이터를 가져오려면 먼저 **Custom Speech > [사용자 프로젝트] > 편집기**로 이동 합니다.

![편집기 탭](media/custom-speech/custom-speech-editor-detail.png)

다음 단계를 사용 하 여 데이터를 가져옵니다.

1. **데이터 가져오기** 클릭
1. 새 데이터 집합을 만들고 설명을 제공 합니다.
1. 데이터 집합을 선택 합니다. 여러 항목을 선택할 수 있으며 오디오 데이터는 물론 오디오 + 사람 레이블이 지정 된 데이터를 선택할 수 있습니다.
1. 오디오 전용 데이터의 경우 필요에 따라 기본 모델을 사용 하 여 편집기로 가져온 후 자동으로 컴퓨터 기록을 생성할 수 있습니다.
1. **가져오기** 클릭

데이터를 가져온 후에는 데이터 집합을 클릭 하 여 편집을 시작할 수 있습니다.

> [!TIP]
> 데이터 집합을 선택 하 고 **편집기로 내보내기를** 클릭 하 여 데이터 집합을 편집기로 직접 가져올 수도 있습니다.

### <a name="edit-transcription-by-listening-to-audio"></a>오디오를 수신 하 여 기록을 편집 합니다.

데이터 업로드가 성공한 후 각 항목 이름을 클릭 하 여 데이터의 세부 정보를 확인 합니다. **이전** 및 **다음** 을 사용 하 여 각 파일 사이를 이동할 수도 있습니다.

세부 정보 페이지에는 각 오디오 파일의 모든 세그먼트가 나열 되며 원하는 utterance을 클릭할 수 있습니다. 각 utterance에 대해 오디오를 재생 하 고, 기록을 검사 하 고, 삽입, 삭제 또는 대체 오류가 발생 하는 경우에는 해당 내용을 편집할 수 있습니다. 오류 유형에 대 한 자세한 내용은 [데이터 평가 방법을](how-to-custom-speech-evaluate-data.md) 참조 하세요.

![편집기 페이지](media/custom-speech/custom-speech-editor.png)

편집을 수행한 후 **저장** 단추를 클릭 합니다.

### <a name="export-datasets-from-the-editor"></a>편집기에서 데이터 집합 내보내기

데이터 집합을 **데이터** 탭으로 다시 내보내려면 데이터 세부 정보 페이지로 이동 하 고 **내보내기** 단추를 클릭 하 여 모든 파일을 새 데이터 집합으로 내보냅니다. 마지막 편집 시간, 오디오 기간 등을 기준으로 파일을 필터링 하 여 원하는 파일을 부분적으로 선택할 수도 있습니다. 

![데이터 내보내기](media/custom-speech/custom-speech-editor-export.png)

데이터로 내보낸 파일은 새 데이터 집합으로 사용 되 고 기존 데이터/학습/테스트 엔터티에는 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
- [모델 개선](how-to-custom-speech-improve-accuracy.md)
- [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

- [Custom Speech에 대 한 테스트 데이터 준비](how-to-custom-speech-test-data.md)
