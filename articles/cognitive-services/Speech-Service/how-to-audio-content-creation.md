---
title: 오디오 콘텐츠 제작 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 오디오 콘텐츠 만들기는 앱 및 제품에 대한 Microsoft의 텍스트 음성 변환 출력을 사용자 지정하고 미세 조정할 수 있는 온라인 도구입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 8184d30471b4d9171a23c8d03e17b2a54dca2ece
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397826"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>오디오 콘텐츠 제작으로 합성 개선

[오디오 콘텐츠 만들기는](https://aka.ms/audiocontentcreation) 앱 및 제품에 대한 Microsoft의 텍스트 음성 변환 출력을 사용자 지정하고 미세 조정할 수 있는 온라인 도구입니다. 이 도구를 사용하여 보다 정확한 자연스러운 표현을 위해 공용 및 사용자 지정 음성을 미세 조정하고 클라우드에서 출력을 관리할 수 있습니다.

오디오 콘텐츠 생성 도구는 [음성 합성 마크업 언어(SSML)를](speech-synthesis-markup.md)기반으로 합니다. 사용자 지정 및 튜닝을 간소화하기 위해 오디오 콘텐츠 생성을 사용하면 텍스트 음성 변환을 실시간으로 시각적으로 검사할 수 있습니다.

## <a name="how-does-it-work"></a>작동 원리

이 다이어그램은 사용자 지정된 음성-텍스트 출력을 조정하고 내보내는 데 걸리는 단계를 보여 주며 있습니다. 각 단계에 대해 자세히 알아보려면 아래 링크를 사용하십시오.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 첫 번째 단계는 [Azure 계정을 만들고, 음성 리소스를 등록하고, 구독 키를 얻는](#create-a-speech-resource)것입니다. 구독 키가 있으면 이 키를 사용하여 음성 서비스를 호출하고 [오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation)에 액세스할 수 있습니다.
2. 일반 텍스트 또는 SSML을 사용하여 [오디오 튜닝 파일을 만듭니다.](#create-an-audio-tuning-file)
3. 조정하려는 음성과 언어를 선택합니다. 오디오 콘텐츠 생성에는 모든 [Microsoft 텍스트 음성 변환 음성이](language-support.md#text-to-speech)포함됩니다. 표준, 신경 또는 사용자 지정 음성을 사용할 수 있습니다.
   >[!NOTE]
   > 사용자 지정 신경 음성에 대해 게이트 액세스가 가능하므로 자연스러운 음성과 유사한 고화질 음성을 만들 수 있습니다. 자세한 내용은 [게이팅 프로세스](https://aka.ms/ignite2019/speech/ethics)를 참조하십시오.

4. 기본 결과를 검토합니다. 그런 다음 튜닝 도구를 사용하여 발음, 피치, 속도, 억양, 음성 스타일 등을 조정합니다. 옵션의 전체 목록은 [음성 합성 마크업 언어를](speech-synthesis-markup.md)참조하십시오.
5. [조정된 오디오를](#export-tuned-audio)저장하고 내보내기. 튜닝 트랙을 시스템에 저장하면 계속 작업하고 출력을 반복할 수 있습니다. 출력이 만족스너리인 경우 내보내기 기능을 사용하여 오디오 생성 작업을 만들 수 있습니다. 내보내기 작업의 상태를 관찰하고 앱 및 제품에 사용할 출력을 다운로드할 수 있습니다.
6. 마지막 단계는 앱 및 제품에서 사용자 지정 조정 된 음성을 사용하는 것입니다.

## <a name="create-a-speech-resource"></a>음성 리소스 만들기

다음 단계에 따라 음성 리소스를 만들고 음성 스튜디오에 연결합니다.

1. 다음 지침에 따라 [Azure 계정에 등록하고](get-started.md#new-resource) [음성 리소스를 만듭니다.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) 가격 책정 계층이 **S0로**설정되어 있는지 확인합니다. Neural 음성 중 하나를 사용하는 경우 [지원되는 지역에서](regions.md#standard-and-neural-voices)리소스를 만들어야 합니다.
2. 오디오 [콘텐츠 만들기에 로그인합니다.](https://aka.ms/audiocontentcreation)
3. 기존 프로젝트를 선택하거나 **새 프로젝트 만들기를 클릭합니다.**
4. 상단 탐색에 있는 **설정** 옵션을 사용 하 고 언제든지 구독을 수정할 수 있습니다.

## <a name="create-an-audio-tuning-file"></a>오디오 튜닝 파일 만들기

오디오 콘텐츠 제작 도구에 콘텐츠를 넣는 방법에는 두 가지가 있습니다.

**옵션 1:**

1. 오디오 콘텐츠 [만들기에](https://aka.ms/audiocontentcreation)로그인한 후 **오디오 튜닝을** 클릭하여 새 오디오 튜닝 파일을 만듭니다.
2. 편집 창이 나타나면 최대 10,000자까지 입력할 수 있습니다.
3. 저장하는 것을 잊지 마십시오.

**옵션 2:**

1. 오디오 콘텐츠 [만들기에](https://aka.ms/audiocontentcreation)로그인한 후 **업로드를** 클릭하여 하나 이상의 텍스트 파일을 가져옵니다. 일반 텍스트와 SSML이 모두 지원됩니다.
2. 텍스트 파일을 업로드할 때 콘텐츠가 이러한 요구 사항을 충족하는지 확인합니다.

   | 속성 | 값 / 노트 |
   |----------|---------------|
   | 파일 형식 | 일반 텍스트(.txt)<br/> SSML 텍스트(.txt)<br/> Zip 파일이 지원되지 않음 |
   | 인코딩 형식 | UTF-8 |
   | 파일 이름 | 각 파일에는 고유한 이름이 있어야 합니다. 중복은 지원되지 않습니다. |
   | 텍스트 길이 | 텍스트 파일은 10,000자를 초과해서는 안 됩니다. |
   | SSML 제한 사항 | 각 SSML 파일에는 단일 SSML만 포함될 수 있습니다. |

### <a name="plain-text-example"></a>일반 텍스트 예제

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML 텍스트 예제

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>튜닝된 오디오 내보내기

오디오 출력을 검토하고 튜닝 및 조정에 만족하면 오디오를 내보낼 수 있습니다.

1. 오디오 [콘텐츠 만들기](https://aka.ms/audiocontentcreation) 도구에서 **내보내기를** 클릭하여 오디오 만들기 작업을 만듭니다.
2. 튜닝된 오디오의 출력 형식을 선택합니다. 지원되는 형식 및 샘플 요금 목록은 아래에서 확인할 수 있습니다.
3. **내보내기 작업** 탭에서 작업 상태를 볼 수 있습니다. 작업이 실패하면 전체 보고서의 자세한 정보 페이지를 참조하세요.
4. 작업이 완료되면 오디오 **라이브러리** 탭에서 오디오를 다운로드할 수 있습니다.
5. **다운로드**를 이제 앱 또는 제품에서 사용자 지정 오디오를 사용할 준비가 되었습니다.

### <a name="supported-audio-formats"></a>지원되는 오디오 형식

| 형식 | 16 kHz 샘플 속도 | 24 kHz 샘플 속도 |
|--------|--------------------|--------------------|
| wav | 리프-16khz-16비트 모노-pcm | 리프-24khz-16비트 모노-pcm |
| mp3 | 오디오 16khz-128kbitrate-모노-mp3 | 오디오 24khz-160kbitrate-모노-mp3 |

## <a name="see-also"></a>참조

* [긴 오디오 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
