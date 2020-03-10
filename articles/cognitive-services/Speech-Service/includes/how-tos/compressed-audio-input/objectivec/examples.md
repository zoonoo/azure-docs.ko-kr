---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943805"
---
압축 오디오 형식을 음성 서비스로 스트리밍하려면 `SPXPullAudioInputStream` 또는 `SPXPushAudioInputStream`를 만듭니다.

다음 코드 조각에서는 MP3를 스트림의 압축 형식으로 지정 하 여 `SPXPushAudioInputStream`인스턴스에서 `SPXAudioConfiguration`을 만드는 방법을 보여 줍니다.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

다음 코드 조각에서는 압축 된 오디오 데이터를 파일에서 읽고 `SPXPushAudioInputStream`펌프 하는 방법을 보여 줍니다.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
