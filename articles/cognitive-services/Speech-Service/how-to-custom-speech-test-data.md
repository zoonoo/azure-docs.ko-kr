---
title: 사용자 지정 음성 - 음성 서비스에 대한 테스트 데이터 준비
titleSuffix: Azure Cognitive Services
description: Microsoft 음성 인식의 정확성을 테스트하거나 사용자 지정 모델을 교육할 때 오디오 및 텍스트 데이터가 필요합니다. 이 페이지에서는 데이터 유형, 사용 방법 및 관리 방법을 다룹니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 969c1450966d2754e6e8f00126da52a1e88181fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942705"
---
# <a name="prepare-data-for-custom-speech"></a>사용자 지정 음성에 대한 데이터 준비

Microsoft 음성 인식의 정확성을 테스트하거나 사용자 지정 모델을 교육할 때 오디오 및 텍스트 데이터가 필요합니다. 이 페이지에서는 데이터 유형, 사용 방법 및 관리 방법을 다룹니다.

## <a name="data-types"></a>데이터 형식

이 표에는 허용된 데이터 형식, 각 데이터 형식을 사용해야 하는 시기 및 권장 수량이 나열됩니다. 모델을 만드는 데 모든 데이터 형식이 필요한 것은 아닙니다. 데이터 요구 사항은 테스트를 만들거나 모델을 학습하는지 여부에 따라 달라집니다.

| 데이터 형식 | 테스트에 사용 | 권장 수량 | 교육에 사용 | 권장 수량 |
|-----------|-----------------|----------|-------------------|----------|
| [오디오](#audio-data-for-testing) | yes<br>육안으로 검사하는 데 사용됩니다. | 5+ 오디오 파일 | 예 | 해당 사항 없음 |
| [오디오 + 사람이 표시한 성적 증명서](#audio--human-labeled-transcript-data-for-testingtraining) | yes<br>정확도 를 평가하는 데 사용됩니다. | 0.5-5시간 의 오디오 | yes | 1-1,000시간의 오디오 |
| [관련 텍스트](#related-text-data-for-training) | 예 | 해당 사항 없음 | yes | 관련 텍스트 1-200MB |

파일은 데이터 집합에 유형별로 그룹화하고 .zip 파일로 업로드해야 합니다. 각 데이터 집합에는 단일 데이터 형식만 포함될 수 있습니다.

> [!TIP]
> 빠르게 시작하려면 샘플 데이터를 사용하는 것이 좋습니다. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">샘플 사용자 지정 음성 데이터에 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 대 한이 GitHub 리포지토리를 참조

## <a name="upload-data"></a>데이터 업로드

데이터를 업로드하려면 <a href="https://speech.microsoft.com/customspeech" target="_blank">사용자 지정 음성 <span class="docon docon-navigate-external x-hidden-focus"> </span>포털로 </a>이동합니다. 포털에서 데이터 **업로드를** 클릭하여 마법사를 실행하고 첫 번째 데이터 집합을 만듭니다. 데이터를 업로드하기 전에 데이터 집합에 대한 음성 데이터 유형을 선택하라는 메시지가 표시됩니다.

![음성 포털에서 오디오 선택](./media/custom-speech/custom-speech-select-audio.png)

업로드하는 각 데이터 집합은 선택한 데이터 형식에 대한 요구 사항을 충족해야 합니다. 데이터가 업로드되기 전에 올바르게 포맷되어야 합니다. 올바르게 포맷된 데이터는 사용자 지정 음성 서비스에서 정확하게 처리되도록 합니다. 요구 사항은 다음 섹션에 나열됩니다.

데이터 집합을 업로드한 후 몇 가지 옵션이 있습니다.

* **테스트** 탭으로 이동하여 오디오 만 또는 오디오 + 사람이 표시한 전사 데이터를 시각적으로 검사할 수 있습니다.
* **교육** 탭으로 이동하여 오디오 + 인간 전사 데이터 또는 관련 텍스트 데이터를 사용하여 사용자 지정 모델을 학습할 수 있습니다.

## <a name="audio-data-for-testing"></a>테스트를 위한 오디오 데이터

오디오 데이터는 Microsoft의 기본 음성-텍스트 모델 또는 사용자 지정 모델의 정확도를 테스트하는 데 최적입니다. 오디오 데이터는 특정 모델의 성능과 관련하여 음성의 정확성을 검사하는 데 사용됩니다. 모델의 정확도를 정량화하려는 경우 [오디오 + 사람이 레이블이 지정한 전사 데이터를](#audio--human-labeled-transcript-data-for-testingtraining)사용합니다.

이 표를 사용하여 오디오 파일의 서식이 올바르게 지정되어 사용자 지정 음성과 함께 사용할 수 있는지 확인합니다.

| 속성                 | 값                 |
|--------------------------|-----------------------|
| 파일 형식              | RIFF(WAV)            |
| 샘플 속도              | 8,000Hz 또는 16,000Hz |
| 채널                 | 1(mono)              |
| 오디오당 최대 길이 | 2시간               |
| 샘플 형식            | PCM, 16비트           |
| 보관 형식           | .zip                  |
| 최대 보관 크기     | 2GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> 학습 및 테스트 데이터를 업로드할 때 .zip 파일 크기는 2GB를 초과할 수 없습니다. 교육에 더 많은 데이터가 필요한 경우 여러 .zip 파일로 나누고 별도로 업로드합니다. 나중에 *여러* 데이터 집합에서 학습하도록 선택할 수 있습니다. 그러나 *단일* 데이터 집합에서만 테스트할 수 있습니다.

<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX를 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 사용하여 오디오 속성을 확인하거나 기존 오디오를 적절한 형식으로 변환합니다. 다음은 SoX 명령줄을 통해 이러한 각 작업을 수행하는 방법에 대한 몇 가지 예입니다.

| 활동 | 설명 | SoX 명령 |
|----------|-------------|-------------|
| 오디오 형식 확인 | 이 명령을 사용하여<br>오디오 파일 형식입니다. | `sox --i <filename>` |
| 오디오 형식 변환 | 이 명령을 사용하여 변환<br>오디오 파일을 단일 채널, 16비트, 16KHz로 이동합니다. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>테스트/교육을 위한 오디오 + 사람이 표시한 성적 증명서 데이터

오디오 파일을 처리할 때 Microsoft의 음성-텍스트 정확도의 정확도를 측정하려면 비교를 위해 사람이 레이블이 지정한 전사(단어별)를 제공해야 합니다. 사람이 표지한 전사는 종종 시간이 많이 걸리지만 정확도를 평가하고 사용 사례에 맞게 모델을 학습해야 합니다. 인식 개선은 제공된 데이터만큼이나 양호합니다. 따라서 고품질 의 성적증명서만 업로드하는 것이 중요합니다.

| 속성                 | 값                               |
|--------------------------|-------------------------------------|
| 파일 형식              | RIFF(WAV)                          |
| 샘플 속도              | 8,000Hz 또는 16,000Hz               |
| 채널                 | 1(mono)                            |
| 오디오당 최대 길이 | 2시간(시험) / 60s(트레이닝) |
| 샘플 형식            | PCM, 16비트                         |
| 보관 형식           | .zip                                |
| 최대 지퍼 크기         | 2GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 학습 및 테스트 데이터를 업로드할 때 .zip 파일 크기는 2GB를 초과할 수 없습니다. *단일* 데이터 집합에서만 테스트할 수 있으므로 적절한 파일 크기 내에 보관해야 합니다. 또한 각 교육 파일은 60초를 초과할 수 없으며 그렇지 않으면 오류가 발생합니다.

단어 삭제 또는 대체와 같은 문제를 해결하려면 인식을 개선하기 위해 상당한 양의 데이터가 필요합니다. 일반적으로 약 10~1,000시간의 오디오에 대해 단어별 기록을 제공하는 것이 좋습니다. 모든 WAV 파일에 대한 전사는 단일 일반 텍스트 파일에 포함되어야 합니다. 전사 파일의 각 줄은 오디오 파일 중 하나의 이름을 포함하고 그 뒤에 해당 전사가 와야 합니다. 파일 이름과 전사는 탭(\t)으로 구분 해야 합니다.

  예를 들어:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> 전사는 UTF-8 BOM으로 인코딩해야 합니다.

전사는 시스템에서 처리할 수 있도록 텍스트로 정규화됩니다. 그러나 스피치 스튜디오에 데이터를 업로드하기 전에 수행해야 하는 몇 가지 중요한 정규화가 있습니다. 전사를 준비할 때 사용할 적절한 언어는 [사람이 표시한 전사를 만드는 방법을](how-to-custom-speech-human-labeled-transcriptions.md) 참조하십시오.

오디오 파일과 해당 기록을 수집한 후 <a href="https://speech.microsoft.com/customspeech" target="_blank">사용자 지정 음성 포털에 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>업로드하기 전에 단일 .zip 파일로 패키징합니다. 다음은 세 개의 오디오 파일과 사람이 레이블이 지정한 전사 파일이 있는 예제 데이터 집합입니다.

> [!div class="mx-imgBorder"]
> ![음성 포털에서 오디오 선택](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>교육을 위한 관련 텍스트 데이터

고유한 제품 이름이나 기능에는 교육을 위한 관련 텍스트 데이터가 포함되어야 합니다. 관련 텍스트는 올바른 인식을 보장하는 데 도움이 됩니다. 인식을 개선하기 위해 두 가지 유형의 관련 텍스트 데이터를 제공할 수 있습니다.

| 데이터 형식 | 이 데이터가 인식을 향상시키는 방법 |
|-----------|------------------------------------|
| 문장(발언) | 문장의 맥락에서 제품 이름 또는 산업별 어휘를 인식할 때 정확도를 향상시킵니다. |
| 발음 | 정의되지 않은 발음으로 흔하지 않은 용어, 약어 또는 다른 단어의 발음을 향상시킵니다. |

문장은 단일 텍스트 파일 또는 여러 텍스트 파일로 제공 될 수 있습니다. 정확도를 향상하려면 예상되는 음성 발언에 더 가까운 텍스트 데이터를 사용합니다. 발음은 단일 텍스트 파일로 제공되어야 합니다. 모든 것을 단일 zip 파일로 패키징하고 <a href="https://speech.microsoft.com/customspeech" target="_blank">사용자 <span class="docon docon-navigate-external x-hidden-focus"> </span>지정 음성 포털에 </a>업로드할 수 있습니다.

### <a name="guidelines-to-create-a-sentences-file"></a>문장 파일을 만드는 지침

문장을 사용하여 사용자 지정 모델을 만들려면 샘플 발언 목록을 제공해야 합니다. 발언은 _do not_ 완전하거나 문법적으로 정확할 필요는 없지만 프로덕션환경에서 기대하는 음성 입력을 정확하게 반영해야 합니다. 특정 용어의 가중치를 늘리려면 이러한 특정 용어를 포함하는 여러 문장을 추가합니다.

일반적인 지침으로, 모델 적응은 교육 텍스트가 프로덕션환경에서 예상되는 실제 텍스트에 최대한 근접할 때 가장 효과적입니다. 도메인별 전문 용어와 강화를 목표로 하는 구는 학습 텍스트에 포함되어야 합니다. 가능하면 한 문장이나 키워드를 별도의 줄에 제어하도록 합니다. 중요한 키워드와 문구(예: 제품 이름)의 경우 몇 번 복사할 수 있습니다. 그러나 너무 많이 복사하지 마십시오 - 그것은 전반적인 인식 속도에 영향을 미칠 수 있습니다 명심하십시오.

이 표를 사용하여 발언에 대한 관련 데이터 파일의 서식이 올바르게 지정되었는지 확인합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM |
| 줄당 발언의 # | 1 |
| 최대 파일 크기 | 200MB |

또한 다음과 같은 제한 사항을 고려해야 합니다.

* 문자를 네 번 이상 반복하지 마십시오. 예를 들어 " aaaa" 또는 "uuuu".
* 위의 `U+00A1`특수 문자 또는 UTF-8 문자를 사용하지 마십시오.
* URI는 거부됩니다.

### <a name="guidelines-to-create-a-pronunciation-file"></a>발음 파일을 만드는 지침

사용자가 만나거나 사용하는 표준 발음이 없는 드문 용어가 있는 경우 사용자 지정 발음 파일을 제공하여 인식을 개선할 수 있습니다.

> [!IMPORTANT]
> 일반적인 단어의 발음을 변경하려면 사용자 지정 발음 파일을 사용하지 않는 것이 좋습니다.

여기에는 음성 발언의 예와 각 발언에 대한 사용자 지정 발음이 포함됩니다.

| 인식/표시 양식 | 발성 형식 |
|--------------|--------------------------|
| 3CPO | 3 c p o |
| CNTK | c n t k |
| Ieee | 나는 트리플 전자 |

음성 양식은 철자된 음성 시퀀스입니다. 문자, 단어, 음절 또는 세 가지 모두의 조합으로 구성될 수 있습니다.

사용자 정의 발음은 영어 ()`en-US`및`de-DE`독일어 ()로 제공됩니다. 이 표에는 지원되는 문자가 언어별로 표시됩니다.

| 언어 | Locale | 문자 |
|----------|--------|------------|
| 영어 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 독일어 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

다음 표를 사용하여 발음에 대한 관련 데이터 파일의 서식이 올바르게 지정되었는지 확인합니다. 발음 파일은 작으며 크기가 몇 킬로바이트여야 합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM (ANSI는 영어로도 지원) |
| # 라인당 발음 | 1 |
| 최대 파일 크기 | 1 MB(프리 티어의 경우 1KB) |

## <a name="next-steps"></a>다음 단계

* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
