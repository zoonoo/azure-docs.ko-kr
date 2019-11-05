---
title: Custom Speech-Speech Service에 대 한 테스트 데이터 준비
titleSuffix: Azure Cognitive Services
description: Microsoft 음성 인식이 정확한 지 확인 하거나 모델을 학습 하는 것을 테스트 하는 경우에는 데이터가 필요 합니다 (오디오 및/또는 텍스트 형식). 이 페이지에서는 데이터 유형, 사용 방법 및 관리 하는 방법을 다룹니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 577a76b628e40b7651345698a46cba255b16a828
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464551"
---
# <a name="prepare-data-for-custom-speech"></a>Custom Speech에 대 한 데이터 준비

Microsoft 음성 인식이 정확한 지 확인 하거나 모델을 학습 하는 것을 테스트 하는 경우에는 오디오와 텍스트 형식의 데이터가 필요 합니다. 이 페이지에서는 데이터 유형, 사용 방법 및 관리 하는 방법을 다룹니다.

## <a name="data-types"></a>데이터 형식

다음 표에서는 허용 되는 데이터 형식, 각 데이터 형식을 사용 해야 하는 경우 및 권장 수량을 나열 합니다. 모델을 만드는 데 모든 데이터 형식이 필요 하지는 않습니다. 데이터 요구 사항은 테스트를 만들거나 모델을 학습 하는지에 따라 달라 집니다.

| 데이터 형식 | 테스트 사용 | 수량 | 학습에 사용 됨 | 수량 |
|-----------|-----------------|----------|-------------------|----------|
| [오디오](#audio-data-for-testing) | 예<br>시각적 검사에 사용 됨 | 5 + 오디오 파일 | 아니요 | 해당 없음 |
| [오디오 + 사람이 레이블 지정 된 성적 증명서](#audio--human-labeled-transcript-data-for-testingtraining) | 예<br>정확도를 평가 하는 데 사용 됩니다. | 0.5 ~ 5 시간 오디오 | 예 | 1-1000 시간 (오디오) |
| [관련 텍스트](#related-text-data-for-training) | 아니요 | 해당 없음 | 예 | 1-200 MB의 관련 텍스트 |

파일은 형식에 따라 데이터 집합으로 그룹화 되 고 zip 파일로 업로드 되어야 합니다. 각 데이터 집합은 단일 데이터 형식만 포함할 수 있습니다.

## <a name="upload-data"></a>데이터 업로드

데이터를 업로드할 준비가 되 면 [Custom Speech 포털로](https://speech.microsoft.com/customspeech)이동한 후 **데이터 업로드** 를 클릭 하 여 마법사를 시작 하 고 첫 번째 데이터 집합을 만듭니다. 데이터를 업로드 하기 전에 데이터 집합에 대 한 음성 데이터 형식을 선택 하 라는 메시지가 표시 됩니다.

![음성 포털에서 오디오를 선택 합니다.](./media/custom-speech/custom-speech-select-audio.png)

업로드 하는 각 데이터 집합은 사용자가 선택 하는 데이터 형식에 대 한 요구 사항을 충족 해야 합니다. 업로드 하기 전에 데이터의 서식을 올바르게 지정 하는 것이 중요 합니다. 이렇게 하면 Custom Speech 서비스가 데이터를 정확 하 게 처리할 수 있습니다. 요구 사항은 다음 섹션에 나열 되어 있습니다.

데이터 집합을 업로드 한 후에는 몇 가지 옵션을 사용할 수 있습니다.

* **테스트** 탭으로 이동 하 여 오디오 전용 또는 오디오 + 사람 레이블이 지정 된 기록 데이터를 시각적으로 검사할 수 있습니다.
* **학습** 탭으로 이동 하 여 오디오 + 인간 기록 데이터 또는 관련 텍스트 데이터를 사용 하 여 사용자 지정 모델을 학습 시킬 수 있습니다.

## <a name="audio-data-for-testing"></a>테스트용 오디오 데이터

오디오 데이터는 Microsoft의 기본 음성 텍스트 모델 또는 사용자 지정 모델의 정확도를 테스트 하는 데 가장 적합 합니다. 오디오 데이터는 특정 모델의 성능과 관련 하 여 음성의 정확도를 검사 하는 데 사용 됩니다. 모델의 정확도를 수량화 하려는 경우 [오디오 + 사람 레이블이 지정](#audio--human-labeled-transcript-data-for-testingtraining)된 기록 데이터를 사용 합니다.

이 표를 사용 하 여 Custom Speech에서 사용 하기 위해 오디오 파일의 형식이 올바르게 지정 되었는지 확인 합니다.

| 속성 | 값 |
|----------|-------|
| 파일 형식 | RIFF(WAV) |
| 샘플링 주기 | 8000 hz 또는 16000 Hz |
| 채널 | 1(mono) |
| 오디오 당 최대 길이 | 2시간 |
| 샘플 형식 | PCM, 16비트 |
| 보관 형식 | .zip |
| 최대 보관 크기 | 2 GB |

오디오가 이러한 속성을 충족 하지 않거나 해당 속성을 확인 하려는 경우 [sox](http://sox.sourceforge.net) 를 다운로드 하 여 오디오를 확인 하거나 변환 하는 것이 좋습니다. 다음은 명령줄을 통해 이러한 각 작업을 수행할 수 있는 방법에 대 한 몇 가지 예입니다.

| 작업 | 설명 | Sox 명령 |
|----------|-------------|-------------|
| 오디오 형식 확인 | 이 명령을 사용 하 여 오디오 파일 형식을 확인 합니다. | `sox --i <filename>` |
| 오디오 형식 변환 | 이 명령을 사용 하 여 오디오 파일을 단일 채널 16 비트, 16Khz로 변환 합니다. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>테스트/학습을 위해 오디오 + 사람이 레이블 지정 된 성적 증명서 데이터

오디오 파일을 처리할 때 Microsoft의 음성-텍스트 정확도 정확도를 측정 하려면 비교를 위해 사용자에 게 레이블이 지정 된 음성-텍스트 (word)를 제공 해야 합니다. 사람이 레이블 지정 된 기록을 사용 하는 경우가 종종 있지만 정확성을 평가 하 고 사용 사례에 맞게 모델을 학습 해야 합니다. 인식의 향상 된 기능은 제공 된 데이터 만큼만 적절 합니다. 따라서 고품질의 증명서만 업로드 하는 것이 중요 합니다.  

| 속성 | 값 |
|----------|-------|
| 파일 형식 | RIFF(WAV) |
| 샘플링 주기 | 8000 hz 또는 16000 Hz |
| 채널 | 1(mono) |
| 오디오 당 최대 길이 | 60 s |
| 샘플 형식 | PCM, 16비트 |
| 보관 형식 | .zip |
| 최대 zip 크기 | 2 GB |

단어 삭제 또는 대체와 같은 문제를 해결 하기 위해 많은 양의 데이터가 필요 하므로 인식 기능을 향상 시킬 수 있습니다. 일반적으로 약 10 ~ 1000 시간의 오디오에 대해 word를 통해 word를 제공 하는 것이 좋습니다. 모든 WAV 파일에 대한 전사는 단일 일반 텍스트 파일에 포함되어야 합니다. 전사 파일의 각 줄은 오디오 파일 중 하나의 이름을 포함하고 그 뒤에 해당 전사가 와야 합니다. 파일 이름과 전사는 탭(\t)으로 구분 해야 합니다.

  예:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> 전사는 UTF-8 BOM으로 인코딩해야 합니다.

전사는 시스템에서 처리할 수 있도록 텍스트로 정규화됩니다. 그러나 Speech Studio에 데이터를 업로드 하기 _전에_ 사용자가 수행 해야 하는 몇 가지 중요 한 normalizations 있습니다. 사용자를 준비할 때 사용할 적절 한 언어는 [사람이 레이블 지정 된 기록을 만드는 방법](how-to-custom-speech-human-labeled-transcriptions.md) 을 참조 하세요.

오디오 파일 및 해당 하는 소리를 수집한 후에는 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 업로드 하기 전에 단일 .zip 파일로 패키지 해야 합니다. 3 개의 오디오 파일과 사람이 레이블이 지정 된 기록 파일이 있는 예제 데이터 집합입니다.

![음성 포털에서 오디오를 선택 합니다.](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>학습에 대 한 관련 텍스트 데이터

제품 이름 또는 기능이 고유 하 고 올바르게 인식 되는지 확인 하려는 경우 학습을 위해 관련 텍스트 데이터를 포함 하는 것이 중요 합니다. 인식 기능을 향상 시키기 위해 다음과 같은 두 가지 유형의 관련 텍스트 데이터를 제공할 수 있습니다.

| 데이터 형식 | 이 데이터가 인식 기능을 향상 시키는 방법 |
|-----------|------------------------------------|
| 길이 발언 및/또는 문장 | 이는 제품 이름 또는 문장의 컨텍스트 내에서 산업별 어휘를 인식할 때 정확성을 향상 시킬 수 있습니다. |
| 발음 | 이렇게 하면 특수 하지 않은 용어, 머리글자어 또는 기타 발음 정의 되지 않은 단어의 발음을 향상할 수 있습니다. |

길이 발언는 단일 또는 여러 텍스트 파일로 제공 될 수 있습니다. 텍스트 데이터에 대 한 자세한 내용은 음성의 데이터에 가까울수록 정확성이 향상 될 가능성이 높아집니다. 발음는 단일 텍스트 파일로 제공 되어야 합니다. 모든 항목을 단일 zip 파일로 패키지 하 고 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 업로드할 수 있습니다.

### <a name="guidelines-to-create-an-utterances-file"></a>길이 발언 파일을 만들기 위한 지침

관련 텍스트를 사용 하 여 사용자 지정 모델을 만들려면 샘플 길이 발언 목록을 제공 해야 합니다. 이러한 길이 발언는 완전 한 문장이 나 문법적으로 올바른 것은 아니지만 프로덕션에 필요한 음성 입력을 정확 하 게 반영 해야 합니다. 특정 조건의 가중치가 증가 하도록 하려면 관련 데이터 파일에 이러한 특정 조건을 포함 하는 여러 문장을 추가 하면 됩니다.

이 표를 사용 하 여 길이 발언에 대 한 관련 데이터 파일의 형식이 올바르게 지정 되었는지 확인 합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM |
| 줄당 발언의 # | 1 |
| 최대 파일 크기 | 200MB |

또한 다음과 같은 제한 사항을 고려해 야 합니다.

* 반복 문자를 4 번 이상 사용 하지 마십시오. 예를 들면 "aaaa" 또는 "uuuu"입니다.
* U + 00A1 위의 특수 문자나 UTF-8 문자를 사용 하지 마세요.
* Uri는 거부 됩니다.

### <a name="guidelines-to-create-a-pronunciation-file"></a>발음 파일을 만들기 위한 지침

사용자가 발생 하거나 사용할 표준 발음 없는 자주 사용 되는 용어가 없는 경우 사용자 지정 음성 파일을 제공 하 여 인식 기능을 향상 시킬 수 있습니다.

> [!IMPORTANT]
> 일반적인 단어의 발음을 변경 하는 데이 기능을 사용 하지 않는 것이 좋습니다.

여기에는 음성 utterance의 예제와 각각에 대 한 사용자 지정 발음이 포함 됩니다.

| 인식/표시 양식 | 발성 형식 |
|--------------|--------------------------|
| 3CPO | 3 개의 c p o |  
| CNTK | c n t k |
| IEEE | i 삼중 e |

음성 형식은 철자를 확인 하는 음성입니다. 문자, 단어, 음절 또는 3의 조합으로 구성 될 수 있습니다.

사용자 지정 된 발음은 영어 (en-us) 및 독일어 (de-de)로 제공 됩니다. 다음 표에서는 언어에 따라 지원 되는 문자를 보여 줍니다.

| language | Locale | 문자 |
|----------|--------|------------|
| 영어 | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| 독일어 | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

이 표를 사용 하 여 발음에 대 한 관련 데이터 파일의 형식이 올바르게 지정 되었는지 확인 합니다. 발음 파일은 작으며 몇 Kb을 초과 해서는 안 됩니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM (ANSI도 영어에 대해 지원 됨) |
| 줄 당 발음 수 | 1 |
| 최대 파일 크기 | 1mb (무료 계층의 경우 1kb) |

## <a name="next-steps"></a>다음 단계

* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
