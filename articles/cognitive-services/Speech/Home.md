---
title: Microsoft Bing Speech Service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Speech API를 사용하여 사용자와의 실시간 상호 작용을 포함하여 앱에 음성 기반 작업을 추가합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 960d2d402f223b306aa6ff05b567d13525e3d525
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340361"
---
# <a name="what-is-bing-speech"></a>Bing Speech란?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

클라우드 기반 Microsoft Bing Speech API를 사용하면 개발자가 음성 명령 제어, 자연스러운 음성 대화를 사용하는 사용자 대화 및 음성 전사/받아쓰기와 같이 애플리케이션에서 강력한 음성 사용 기능을 쉽게 만들 수 있습니다. Microsoft Speech API는 *Speech to Text* 및 *Text to Speech* 변환을 모두 지원합니다.

- **Speech to Text**(음성 - 텍스트 변환) API는 인간의 음성을 텍스트로 변환하여 응용 프로그램을 제어하기 위한 입력 또는 명령으로 사용할 수 있습니다.
- **Text to Speech**(텍스트 음성 변환) API는 텍스트를 오디오 스트림으로 변환하여 응용 프로그램 사용자에게 재생할 수 있습니다.

## <a name="speech-to-text-speech-recognition"></a>음성 - 텍스트 변환(음성 인식)

Microsoft Speech Recognition API는 오디오 스트림을 애플리케이션에서 사용자에게 표시하거나 명령 입력으로 작동할 수 있는 텍스트로 *전사*합니다. 개발자가 앱에 Speech를 추가하는 두 가지 방법, 즉 REST API **또는** Websocket 기반 클라이언트 라이브러리를 제공합니다.

- [REST API](GetStarted/GetStartedREST.md): 개발자가 음성 인식을 위해 앱에서 서비스로의 HTTP 호출을 사용할 수 있습니다.
- [클라이언트 라이브러리](GetStarted/GetStartedClientLibraries.md): 고급 기능의 경우 개발자가 Microsoft Speech 클라이언트 라이브러리를 다운로드하고 앱에 연결할 수 있습니다.  클라이언트 라이브러리는 다양한 언어(C#, Java, JavaScript, ObjectiveC)를 사용하여 다양한 플랫폼(Windows, Android, iOS)에서 사용할 수 있습니다. REST API와 달리, 클라이언트 라이브러리는 Websocket 기반 프로토콜을 사용합니다.

| 사용 사례 | [REST API](GetStarted/GetStartedREST.md) | [클라이언트 라이브러리](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| 중간 결과가 없는 짧은 음성 오디오(예: 오디오 길이가 15초 미만인 명령) 변환 | yes | yes |
| 긴 오디오(15초 초과) 변환 | 아니요 | yes |
| 중간 결과가 필요한 오디오 스트림 | 아니요 | yes |
| LUIS를 사용하여 오디오에서 변환된 텍스트 인식 | 아니요 | yes |

개발자가 REST API와 클라이언트 라이브러리 중에서 어느 것을 선택하든 Microsoft Speech Service에서 지원하는 항목은 다음과 같습니다.

- Cortana, Office Dictation, Office Translator 및 기타 Microsoft 제품에서 사용하는 Microsoft의 고급 음성 인식 기술
- 실시간 연속 인식. 음성 인식 API를 사용하면 오디오를 텍스트로 실시간으로 전사할 수 있으며, 지금까지 인식된 단어에 대한 중간 결과를 받을 수 있습니다. 음성 끝 감지도 음성 서비스에서 지원합니다. 또한 사용자는 대문자 표시/문장 부호, 불경한 언어 마스킹 및 텍스트 정규화와 같은 추가 형식 지정 기능을 선택할 수 있습니다.
- *대화형*, *대화* 및 *받아쓰기* 시나리오에 최적화된 음성 인식 결과를 지원합니다. 사용자 지정 언어 모델과 음향 모델이 필요한 사용자 시나리오의 경우 [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md)를 사용하면 애플리케이션과 사용자에 맞게 조정된 음성 모델을 만들 수 있습니다.
- 여러 방언으로 된 다양한 음성 언어를 지원합니다. 각 인식 모드에서 지원되는 언어의 전체 목록은 [인식 언어](api-reference-rest/supportedlanguages.md)를 참조하세요.
- 언어 인식과의 통합. *Speech to Text*는 입력 오디오를 텍스트로 변환하는 것 외에도 텍스트의 의미를 인식하는 추가 기능을 응용 프로그램에 제공합니다. [LUIS(Language Understanding Intelligent Service)](../LUIS/what-is-luis.md)를 사용하여 인식된 텍스트에서 의도와 엔터티를 추출합니다.

### <a name="next-steps"></a>다음 단계

- [REST API](GetStarted/GetStartedREST.md) 또는 [클라이언트 라이브러리](GetStarted/GetStartedClientLibraries.md)를 사용하여 Microsoft 음성 인식 서비스를 시작합니다.
- 기본 설정 프로그래밍 언어로 작성된 [샘플 애플리케이션](samples.md)을 확인합니다.
- [참조] 섹션에서 [Microsoft Speech 프로토콜](API-Reference-REST/websocketprotocol.md) 세부 정보 및 API 참조를 찾습니다.

## <a name="text-to-speech-speech-synthesis"></a>텍스트 음성 변환(음성 합성)

*Text to Speech* API는 REST를 사용하여 구조화된 텍스트를 오디오 스트림으로 변환합니다. API는 다양한 음성과 언어를 통해 빠른 텍스트 음성 변환 기능을 제공합니다. 또한 사용자는 SSML 태그를 사용하여 발음, 음량, 피치 등과 같은 오디오 특성을 변경할 수 있습니다.

### <a name="next-steps"></a>다음 단계

- Microsoft 텍스트 음성 변환 서비스([Text to Speech API 참조](api-reference-rest/bingvoiceoutput.md))를 사용합니다. Text to Speech에서 지원되는 언어와 음성에 대한 전체 목록은 [지원되는 로캘 및 음성 글꼴](api-reference-rest/bingvoiceoutput.md#SupLocales)을 참조하세요.
