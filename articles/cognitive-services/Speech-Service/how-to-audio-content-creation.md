---
title: 오디오 콘텐츠 생성-음성 서비스
titleSuffix: Azure Cognitive Services
description: 오디오 콘텐츠 만들기는 앱 및 제품에 대 한 Microsoft의 텍스트 음성 변환 출력을 사용자 지정 하 고 미세 조정할 수 있는 온라인 도구입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8e3a76abe840a70ba7410855cde1d82ec96c28eb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500875"
---
# <a name="audio-content-creation"></a>오디오 콘텐츠 만들기

[오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation) 는 앱 및 제품에 대 한 Microsoft의 텍스트 음성 변환 출력을 사용자 지정 하 고 미세 조정할 수 있는 온라인 도구입니다. 이 도구를 사용 하 여 보다 정확한 자연 식에 대 한 공용 및 사용자 지정 음성을 미세 조정 하 고 클라우드에서 출력을 관리할 수 있습니다.

오디오 콘텐츠 생성 도구는 [SSML (음성 합성 마크업 언어)](speech-synthesis-markup.md)을 기반으로 합니다. 사용자 지정 및 조정을 간소화 하기 위해 오디오 콘텐츠 생성을 사용 하면 텍스트 음성 변환 출력을 실시간으로 시각적으로 검사할 수 있습니다.

## <a name="how-does-it-work"></a>작동 원리

이 다이어그램에서는 사용자 지정 된 음성-텍스트 출력을 조정 하 고 내보내는 데 필요한 단계를 보여 줍니다. 각 단계에 대 한 자세한 내용을 보려면 아래 링크를 사용 하세요.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 첫 번째 단계는 [Azure 계정을 만들고, 음성 리소스를 등록 하 고, 구독 키를 가져오는](#create-a-speech-resource)것입니다. 구독 키가 있으면이 키를 사용 하 여 음성 서비스를 호출 하 고 [오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation)에 액세스할 수 있습니다.
2. 일반 텍스트 또는 SSML를 사용 하 여 [오디오 튜닝 파일을 만듭니다](#create-an-audio-tuning-file) .
3. 조정 하고자 하는 음성 및 언어를 선택 합니다. 오디오 콘텐츠 생성에는 모든 [Microsoft 텍스트 음성 변환 음성이](language-support.md#text-to-speech)포함 됩니다. 표준, 신경망 또는 고유한 사용자 지정 음성을 사용할 수 있습니다.
   >[!NOTE]
   > 제어 된 액세스는 사용자 지정 신경망에 사용할 수 있으며,이를 통해 자연 스런 음성 처럼 고화질 음성을 만들 수 있습니다. 자세한 내용은 [제어 프로세스](https://aka.ms/ignite2019/speech/ethics)를 참조 하세요.

4. 기본 결과를 검토 합니다. 그런 다음, 조정 도구를 사용 하 여 발음, 피치, 요율, intonation, 음성 스타일 등을 조정 합니다. 전체 옵션 목록은 [음성 합성 마크업 언어](speech-synthesis-markup.md)를 참조 하세요.
5. [튜닝 오디오를](#export-tuned-audio)저장 하 고 내보냅니다. 시스템에서 튜닝 트랙을 저장할 때 계속 해 서 작업 하 고 출력을 반복할 수 있습니다. 출력에 만족 하는 경우 내보내기 기능을 사용 하 여 오디오 만들기 작업을 만들 수 있습니다. 내보내기 작업의 상태를 관찰 하 고 앱 및 제품에 사용할 출력을 다운로드할 수 있습니다.
6. 마지막 단계는 앱 및 제품에서 사용자 지정 조정 된 음성을 사용 하는 것입니다.

## <a name="create-a-speech-resource"></a>음성 리소스 만들기

음성 리소스를 만들고 Speech Studio와 연결 하려면 다음 단계를 수행 합니다.

1. 다음 지침에 따라 [Azure 계정에 등록 하 고 음성 리소스를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure). 가격 책정 계층이 **S0**로 설정 되었는지 확인 합니다. 신경망 중 하나를 사용 하는 경우 [지원 되는 지역](regions.md#standard-and-neural-voices)에서 리소스를 만들어야 합니다.
2. [오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation)에 로그인 합니다.
3. 기존 프로젝트를 선택 하거나 **새로 만들기**를 클릭 합니다.
4. 위쪽 탐색 창에 있는 **설정** 옵션을 사용 하 여 언제 든 지 구독을 수정할 수 있습니다.

## <a name="create-an-audio-tuning-file"></a>오디오 튜닝 파일 만들기

오디오 콘텐츠 생성 도구에 콘텐츠를 가져오는 방법에는 두 가지가 있습니다.

**옵션 1:**

1. [오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation)에 로그인 한 후 **오디오 튜닝** 을 클릭 하 여 새 오디오 튜닝 파일을 만듭니다.
2. 편집 창이 표시 되 면 최대 1만 자까지 입력할 수 있습니다.
3. 저장 하는 것을 잊지 마세요.

**옵션 2:**

1. [오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation)에 로그인 한 후 **업로드** 를 클릭 하 여 하나 이상의 텍스트 파일을 가져옵니다. 일반 텍스트와 SSML은 모두 지원 됩니다.
2. 텍스트 파일을 업로드할 때 콘텐츠가 이러한 요구 사항을 충족 하는지 확인 합니다.

   | 속성 | 값/메모 |
   |----------|---------------|
   | 파일 형식 | 일반 텍스트(.txt)<br/> SSML 텍스트 (.txt)<br/> Zip 파일은 지원 되지 않습니다. |
   | 인코딩 형식 | UTF-8 |
   | 파일 이름 | 각 파일의 이름은 고유 해야 합니다. 중복은 지원 되지 않습니다. |
   | 텍스트 길이 | 텍스트 파일은 1만 자를 초과할 수 없습니다. |
   | SSML 제한 사항 | 각 SSML 파일은 SSML의 단일 부분을 포함할 수 있습니다. |

### <a name="plain-text-example"></a>일반 텍스트 예

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML 텍스트 예

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>튜닝 오디오 내보내기

오디오 출력을 검토 하 고 조정 및 조정에 만족 하는 경우 오디오를 내보낼 수 있습니다.

1. [오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation) 도구에서 **내보내기** 를 클릭 하 여 오디오 만들기 작업을 만듭니다.
2. 튜닝 된 오디오의 출력 형식을 선택 합니다. 지원 되는 형식 및 샘플 속도 목록은 아래에서 볼 수 있습니다.
3. 작업 **내보내기** 탭에서 작업 상태를 볼 수 있습니다. 태스크가 실패 하는 경우 전체 보고서에 대 한 자세한 정보 페이지를 참조 하세요.
4. 작업이 완료 되 면 오디오 **라이브러리** 탭에서 오디오를 다운로드할 수 있습니다.
5. **다운로드**를 클릭합니다. 이제 앱 또는 제품에서 사용자 지정 조정 된 오디오를 사용할 준비가 되었습니다.

### <a name="supported-audio-formats"></a>지원되는 오디오 형식

| 형식 | 16Khz 샘플 주기 | 24khz 샘플 요금 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16 비트 | riff-24khz-16 비트 |
| mp3 | 오디오-16khz-128kbitrate 전송률-mono-mp3 | 오디오-24khz-160kbitrate 전송률-mono-mp3 |

## <a name="see-also"></a>참고 항목

* [긴 오디오 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
