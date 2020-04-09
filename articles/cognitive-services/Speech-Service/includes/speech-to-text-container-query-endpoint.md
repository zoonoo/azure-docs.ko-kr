---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879027"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>음성-텍스트 또는 사용자 지정 음성-텍스트

컨테이너는 [Speech SDK](../index.yml)를 통해 액세스되는 Websocket 기반 쿼리 끝점 API를 제공합니다. 기본적으로 Speech SDK는 온라인 음성 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다.

> [!TIP]
> 컨테이너와 함께 음성 SDK를 사용하는 경우 Azure Speech 리소스 [구독 키 또는 인증 베어러 토큰을](../rest-speech-to-text.md#authentication)제공할 필요가 없습니다.

아래 예제를 참조하세요.

# <a name="c"></a>[C#](#tab/csharp)

다음 Azure 클라우드 초기화 호출을

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

컨테이너 [호스트를](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)사용하여 이 호출에:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

다음 Azure 클라우드 초기화 호출을

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

컨테이너 [호스트를](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)사용하여 이 호출에:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
