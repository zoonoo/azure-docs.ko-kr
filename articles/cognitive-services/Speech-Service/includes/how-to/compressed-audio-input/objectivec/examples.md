---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409699"
---
압축된 오디오 형식으로 음성 서비스로 스트리밍하려면 `SPXPullAudioInputStream` 또는 `SPXPushAudioInputStream`을 만듭니다.

다음 코드 조각은 MP3를 `SPXAudioConfiguration` 스트림의 압축 `SPXPushAudioInputStream`형식으로 지정하는 "의 인스턴스에서 a를 만드는 방법을 보여 주다.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

다음 코드 조각은 압축된 오디오 데이터를 파일에서 읽고 `SPXPushAudioInputStream`로 펌핑하는 방법을 보여 주며.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
