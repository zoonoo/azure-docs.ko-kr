---
title: JavaScript에서 Bing Speech Recognition API 시작 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Cognitive Services의 Bing Speech Recognition API를 사용하여 음성 오디오를 텍스트로 계속 변환하는 애플리케이션을 개발합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17901ad40a48e9ee8d1a8b872b04ad52b75b3a52
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672409"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>JavaScript에서 Speech Recognition API 시작

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Speech Recognition API를 사용하여 음성 오디오를 텍스트로 변환하는 애플리케이션을 개발할 수 있습니다. JavaScript 클라이언트 라이브러리는 [Speech Service WebSocket 프로토콜](../API-Reference-REST/websocketprotocol.md)을 사용하여 전사된 텍스트를 동시에 말하고 받을 수 있습니다. 이 문서는 JavaScript에서 Speech Recognition API를 시작하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 조건

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API 구독 및 평가판 구독 키 가져오기

Speech API는 Cognitive Services의 일부입니다. [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지에서 평가판 구독 키를 가져올 수 있습니다. Speech API를 선택한 후에 **API 키 가져오기**를 선택하여 키를 가져옵니다. 기본 및 보조 키를 반환합니다. 두 키는 모두 동일한 할당량에 연결되므로 두 키 중 하나를 사용할 수 있습니다.

> [!IMPORTANT]
> 구독 키를 가져오세요. Speech 클라이언트 라이브러리를 사용하려면 먼저 [구독 키](https://azure.microsoft.com/try/cognitive-services/)가 있어야 합니다.

## <a name="get-started"></a>시작하기

이 섹션에서는 샘플 HTML 페이지를 로드하는 데 필요한 단계를 안내합니다. 샘플은 [GitHub 리포지토리](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)에 있습니다. 리포지토리에서 **직접 샘플을 열거나** 리포지토리의 **로컬 복사본에서 샘플을 열 수 있습니다**.

> [!NOTE]
> 일부 브라우저는 보안되지 않은 원본에서 마이크를 액세스하지 못하도록 차단합니다. 따라서 https에서 '샘플'/'앱'을 호스팅하여 지원되는 모든 브라우저에서 작동하도록 하는 것이 좋습니다.

### <a name="open-the-sample-directly"></a>샘플 직접 열기

위에서 설명한 대로 구독 키를 얻습니다. 그런 다음, [샘플에 대한 링크](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html)를 엽니다. 그러면 기본 브라우저에 페이지가 로드됩니다([htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)를 사용하여 렌더링됨).

### <a name="open-the-sample-from-a-local-copy"></a>로컬 복사본에서 샘플 열기

샘플을 로컬에서 사용하려면 이 리포지토리를 복제합니다.

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

TypeScript 소스를 컴파일하고, 단일 JavaScript 파일에 번들로 포함시킵니다([npm](https://www.npmjs.com/)이 컴퓨터에 설치되어 있어야 함). 복제된 리포지토리의 루트로 변경하고, 다음 명령을 실행합니다.

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

즐겨찾는 브라우저에서 `samples\browser\Sample.html`을 엽니다.

## <a name="next-steps"></a>다음 단계

사용자 고유의 웹 페이지에 SDK를 포함하는 방법에 대한 자세한 내용은 [여기](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)서 사용할 수 있습니다.

## <a name="remarks"></a>설명

- Speech Recognition API는 세 가지 [인식 모드](../concepts.md#recognition-modes)를 지원합니다. Sample.html 파일에 있는 **Setup()** 함수를 업데이트하여 모드를 전환할 수 있습니다. 이 샘플에서는 기본적으로 모드를 `Interactive`로 설정합니다. 모드를 변경하려면 `SR.RecognitionMode.Interactive` 매개 변수를 다른 모드로 업데이트합니다. 예를 들어 매개 변수를 `SR.RecognitionMode.Conversation`으로 변경합니다.
- 지원되는 언어에 대한 전체 목록은 [지원되는 언어](../API-Reference-REST/supportedlanguages.md)를 참조하세요.

## <a name="related-topics"></a>관련된 항목

- [JavaScript Speech Recognition API 샘플 리포지토리](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [REST API 시작](GetStartedREST.md)
