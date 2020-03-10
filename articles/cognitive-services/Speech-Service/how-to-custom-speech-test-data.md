---
title: Custom Speech-Speech service에 대 한 테스트 데이터 준비
titleSuffix: Azure Cognitive Services
description: Microsoft 음성 인식의 정확도를 테스트 하거나 사용자 지정 모델을 학습 하는 경우 오디오 및 텍스트 데이터가 필요 합니다. 이 페이지에서는 데이터 형식, 사용 방법 및 관리 방법에 대해 다룹니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 969c1450966d2754e6e8f00126da52a1e88181fc
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942705"
---
# <a name="prepare-data-for-custom-speech"></a>사용자 지정 음성에 대한 데이터 준비

Microsoft 음성 인식의 정확도를 테스트 하거나 사용자 지정 모델을 학습 하는 경우 오디오 및 텍스트 데이터가 필요 합니다. 이 페이지에서는 데이터 형식, 사용 방법 및 관리 방법에 대해 다룹니다.

## <a name="data-types"></a>데이터 형식

이 표는 각 데이터 형식을 사용해야 하는 경우, 허용되는 데이터 형식 및 권장되는 수량을 나열합니다. 모델을 만들기 위해 모든 데이터 형식이 필요한 것은 아닙니다. 데이터 요구 사항은 테스트를 만드는지 또는 모델을 학습시키는지 여부에 따라 달라집니다.

| 데이터 형식 | 테스트에 사용 됨 | 권장 수량 | 학습에 사용 됨 | 권장 수량 |
|-----------|-----------------|----------|-------------------|----------|
| [오디오](#audio-data-for-testing) | yes<br>시각적 조사에 사용됨 | 5 + 오디오 파일 | 예 | 해당 사항 없음 |
| [오디오 + 사람이 레이블 지정 된 성적 증명서](#audio--human-labeled-transcript-data-for-testingtraining) | yes<br>정확도 평가에 사용됨 | 0.5-오디오의 5 시간 | yes | 1 ~ 1000 시간 (오디오) |
| [관련 텍스트](#related-text-data-for-training) | 예 | 해당 사항 없음 | yes | 1-200 MB의 관련 텍스트 |

파일은 형식에 따라 데이터 집합으로 그룹화 되 고 .zip 파일로 업로드 되어야 합니다. 각 데이터 집합은 단일 데이터 형식만 포함할 수 있습니다.

> [!TIP]
> 빠르게 시작 하려면 샘플 데이터를 사용 하는 것이 좋습니다. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">샘플 Custom Speech 데이터 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 는이 GitHub 리포지토리를 참조 하세요.

## <a name="upload-data"></a>데이터 업로드

데이터를 업로드 하려면 <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech 포털로 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>이동 합니다. 포털에서 **데이터 업로드** 를 클릭 하 여 마법사를 시작 하 고 첫 번째 데이터 집합을 만듭니다. 데이터 업로드를 허용하기 전에 데이터 집합에 대한 음성 데이터 형식을 선택하라는 메시지가 표시됩니다.

![음성 포털에서 오디오를 선택 합니다.](./media/custom-speech/custom-speech-select-audio.png)

업로드하는 각 데이터 집합은 선택한 데이터 형식에 대한 요구 사항을 충족해야 합니다. 데이터를 업로드 하기 전에 올바른 형식으로 지정 해야 합니다. 올바른 형식의 데이터를 사용 하면 Custom Speech 서비스에서 정확 하 게 처리 됩니다. 요구 사항은 다음 섹션에 나열됩니다.

데이터 집합을 업로드한 후, 몇 가지 옵션이 있습니다.

* **테스트** 탭으로 이동 하 여 오디오 전용 또는 오디오 + 사람 레이블이 지정 된 기록 데이터를 시각적으로 검사할 수 있습니다.
* **학습** 탭으로 이동 하 여 오디오 + 인간 기록 데이터 또는 관련 텍스트 데이터를 사용 하 여 사용자 지정 모델을 학습 시킬 수 있습니다.

## <a name="audio-data-for-testing"></a>테스트를 위한 오디오 데이터

오디오 데이터는 Microsoft의 기본 음성-텍스트 모델 또는 사용자 지정 모델의 정확도를 테스트하기에 적합합니다. 오디오 데이터는 특정 모델의 성능 향상을 위해 음성의 정확성을 검사하는 데 사용되는 것임을 명심합니다. 모델의 정확도를 수량화 하려는 경우 [오디오 + 사람 레이블이 지정](#audio--human-labeled-transcript-data-for-testingtraining)된 기록 데이터를 사용 합니다.

이 표를 사용하여 사용자 지정 음성에서 사용할 수 있도록 오디오 파일의 형식이 올바른지 확인합니다.

| 속성                 | 값                 |
|--------------------------|-----------------------|
| 파일 형식              | RIFF(WAV)            |
| 샘플링 주기              | 8000 hz 또는 16000 Hz |
| 채널                 | 1(mono)              |
| 오디오 당 최대 길이 | 2시간               |
| 샘플 형식            | PCM, 16비트           |
| 보관 형식           | .zip                  |
| 최대 보관 크기     | 2GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> 학습 및 테스트 데이터를 업로드할 때 .zip 파일 크기는 2gb를 초과할 수 없습니다. 학습에 더 많은 데이터를 요구 하는 경우 여러 개의 .zip 파일로 나누고 별도로 업로드 합니다. 나중에 *여러* 데이터 집합에서 학습 하도록 선택할 수 있습니다. 그러나 *단일* 데이터 집합 에서만 테스트할 수 있습니다.

<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 를 사용 하 여 오디오 속성을 확인 하거나 기존 오디오를 적절 한 형식으로 변환 합니다. 다음은 이러한 각 활동을 SoX 명령줄을 통해 수행할 수 있는 방법에 대 한 몇 가지 예입니다.

| 작업 | Description | SoX 명령 |
|----------|-------------|-------------|
| 오디오 형식 확인 | 다음 명령을 사용 하 여 확인 합니다.<br>오디오 파일 형식입니다. | `sox --i <filename>` |
| 오디오 형식 변환 | 다음 명령을 사용 하 여 변환 합니다.<br>단일 채널에 대 한 오디오 파일 (16 비트, 16 KHz)입니다. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>테스트/학습을 위한 오디오 + 사람 레이블 기록 데이터

오디오 파일을 처리하는 동안 Microsoft의 음성-텍스트 정확도를 측정하려면, 비교를 위한 사람 레이블 기록(단어 단위)을 제공해야 합니다. 사람 레이블 기록은 시간이 오래 걸리는 반면, 정확도를 평가하고 사용 사례에 대해 모델을 학습시키는 데 필요합니다. 인식의 향상은 제공된 데이터에 달려 있다는 점을 기억하세요. 따라서 고품질 기록을 업로드하는 것이 중요합니다.

| 속성                 | 값                               |
|--------------------------|-------------------------------------|
| 파일 형식              | RIFF(WAV)                          |
| 샘플링 주기              | 8000 hz 또는 16000 Hz               |
| 채널                 | 1(mono)                            |
| 오디오 당 최대 길이 | 2 시간 (테스트)/60 s (교육) |
| 샘플 형식            | PCM, 16비트                         |
| 보관 형식           | .zip                                |
| 최대 zip 크기         | 2GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 학습 및 테스트 데이터를 업로드할 때 .zip 파일 크기는 2gb를 초과할 수 없습니다. *단일* 데이터 집합 에서만 테스트할 수 있으며, 적절 한 파일 크기 내에서 유지 해야 합니다. 또한 각 학습 파일은 60 초를 초과할 수 없습니다. 그렇지 않으면 오류가 발생 합니다.

단어 삭제 또는 대체와 같은 이슈를 해결하기 위해, 인식 기능을 향상하는 데는 많은 양의 데이터가 필요합니다. 일반적으로, 약 10~ 1000시간의 오디오의 단어 단위 기록을 제공하는 것이 좋습니다. 모든 WAV 파일에 대한 전사는 단일 일반 텍스트 파일에 포함되어야 합니다. 전사 파일의 각 줄은 오디오 파일 중 하나의 이름을 포함하고 그 뒤에 해당 전사가 와야 합니다. 파일 이름과 전사는 탭(\t)으로 구분 해야 합니다.

  다음은 그 예입니다.
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> 전사는 UTF-8 BOM으로 인코딩해야 합니다.

전사는 시스템에서 처리할 수 있도록 텍스트로 정규화됩니다. 그러나 데이터를 Speech Studio로 업로드 하기 전에 수행 해야 하는 몇 가지 중요 한 normalizations 있습니다. 사용자를 준비할 때 사용할 적절 한 언어는 [사람이 레이블 지정 된 기록을 만드는 방법](how-to-custom-speech-human-labeled-transcriptions.md) 을 참조 하세요.

오디오 파일 및 해당 하는 해당 하는 항목을 수집한 후에는 <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech 포털 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>에 업로드 하기 전에 단일 .zip 파일로 패키지 합니다. 다음은 3 개의 오디오 파일과 사람이 레이블이 지정 된 기록 파일이 있는 예제 데이터 집합입니다.

> [!div class="mx-imgBorder"]
> 음성 포털에서 오디오 ![선택](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>학습을 위한 관련된 텍스트 데이터

고유 하 게 제공 되는 제품 이름 또는 기능에는 학습을 위해 관련 텍스트 데이터를 포함 해야 합니다. 관련 텍스트를 통해 올바른 인식을 보장할 수 있습니다. 인식 기능을 향상하기 위해  두 가지 유형의 관련된 텍스트 데이터를 제공할 수 있습니다.

| 데이터 형식 | 이 데이터가 인식 기능을 향상 시키는 방법 |
|-----------|------------------------------------|
| 문장 (길이 발언) | 문장의 컨텍스트 내에서 제품 이름 또는 산업별 어휘를 인식할 때 정확성을 향상 시킵니다. |
| 발음 | 특수 하지 않은 용어, 머리글자어 또는 기타 단어 (정의 되지 않은 발음)의 발음을 개선 합니다. |

문장은 단일 텍스트 파일 또는 여러 텍스트 파일로 제공 될 수 있습니다. 정확도를 높이기 위해 예상 되는 음성 길이 발언 더 가까운 텍스트 데이터를 사용 합니다. 발음은 단일 텍스트 파일로 제공되어야 합니다. 모든 항목을 단일 zip 파일로 패키지 하 고 <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech 포털 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>에 업로드할 수 있습니다.

### <a name="guidelines-to-create-a-sentences-file"></a>문장 파일을 만들기 위한 지침

문장을 사용 하 여 사용자 지정 모델을 만들려면 샘플 길이 발언 목록을 제공 해야 합니다. 길이 발언는 완전 하거나 문법적으로 정확 하 게 지정할 필요는 없지만 프로덕션 _에서 필요한 음성_ 입력을 정확 하 게 반영 해야 합니다. 특정 조건의 가중치가 증가 하도록 하려면 이러한 특정 용어를 포함 하는 여러 문장을 추가 합니다.

일반적으로 모델 적응은 학습 텍스트가 프로덕션 환경에서 예상 되는 실제 텍스트와 최대한 가까운 경우에 가장 효과적입니다. 개선을 위해 대상으로 지정 하려는 도메인별 용어 및 구가 학습 텍스트에 포함 되어야 합니다. 가능 하면 하나의 문장이 나 키워드를 별도의 줄에 제어 해 보세요. 사용자에 게 중요 한 키워드 및 구 (예: 제품 이름)의 경우 여러 번 복사할 수 있습니다. 그러나 너무 많이 복사 하지 마세요. 전체 인식 요금에 영향을 줄 수 있습니다.

이 표를 사용하여 발언에 대한 관련 데이터 파일의 형식이 올바른지 확인합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM |
| 줄당 발언의 # | 1 |
| 최대 파일 크기 | 200MB |

또한 다음과 같은 제한 사항을 고려해 야 합니다.

* 반복 문자를 4 번 이상 사용 하지 마십시오. 예를 들면 "aaaa" 또는 "uuuu"입니다.
* `U+00A1`위의 특수 문자 또는 UTF-8 문자를 사용 하지 마세요.
* Uri는 거부 됩니다.

### <a name="guidelines-to-create-a-pronunciation-file"></a>발음 파일을 만들기 위한 지침

사용자가 접하거나 사용하는 용어 중 표준 발음이 없는 비일반적인 용어가 있는 경우, 인식 기능을 향상하기 위해 사용자 지정 발음 파일을 제공할 수 있습니다.

> [!IMPORTANT]
> 사용자 지정 발음 파일을 사용 하 여 일반적인 단어의 발음을 변경 하는 것은 권장 되지 않습니다.

각 음성 발언 및 사용자 정의 발음 예가 포함됩니다.

| 인식/표시 양식 | 발성 형식 |
|--------------|--------------------------|
| 3CPO | three c p o |
| CNTK | c n t k |
| IEEE | i triple e |

음성 형식은 철자를 확인 하는 음성입니다. 문자, 단어, 음절 또는 3의 조합으로 구성 될 수 있습니다.

사용자 지정 된 발음은 영어 (`en-US`)와 독일어 (`de-DE`)로 제공 됩니다. 다음 표는 각 언어에서 지원되는 문자를 보여줍니다.

| 언어 | Locale | 문자 |
|----------|--------|------------|
| 영어 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 독일어 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

다음 표를 사용 하 여 발음에 대 한 관련 데이터 파일의 형식이 올바르게 지정 되었는지 확인 합니다. 발음 파일은 작으며 크기가 몇 킬로바이트 여야 합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | Utf-8 BOM(ANSI 또한 영어에서 지원됩니다.) |
| 줄 당 발음 수 | 1 |
| 최대 파일 크기 | 1MB(무료 계층은 1KB) |

## <a name="next-steps"></a>다음 단계

* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
