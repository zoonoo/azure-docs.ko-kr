---
title: 음성-텍스트 컨테이너 끝점 쿼리
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491189"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>음성 텍스트 또는 Custom Speech 텍스트

컨테이너는 [SPEECH SDK](../index.md)를 통해 액세스 되는 websocket 기반 쿼리 끝점 api를 제공 합니다. 기본적으로 Speech SDK는 온라인 음성 서비스를 사용 합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다.

> [!TIP]
> 컨테이너와 함께 Speech SDK를 사용 하는 경우 Azure 음성 리소스 [구독 키 또는 인증 전달자 토큰](../rest-speech-to-text.md#authentication)을 제공 하지 않아도 됩니다.

아래 예제를 참조하세요.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 Azure 클라우드 초기화 호출을

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

컨테이너 [끝점](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet)을 사용 하 여이 호출을 수행 합니다.

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

다음 Azure 클라우드 초기화 호출을

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

컨테이너 [끝점](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)을 사용 하 여이 호출을 수행 합니다.

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
