---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422274"
---
압축 오디오 형식을 음성 서비스로 스트리밍하려면 `SPXPullAudioInputStream` 또는 `SPXPushAudioInputStream`를 만듭니다.

다음 코드 조각에서는 MP3를 스트림의 압축 `SPXAudioConfiguration` 형식으로 지정 하 여 `SPXPushAudioInputStream`의 인스턴스에서을 만드는 방법을 보여 줍니다.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

다음 코드 조각은 압축 된 오디오 데이터를 파일에서 읽고로 펌프 하는 방법을 보여 줍니다 `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
