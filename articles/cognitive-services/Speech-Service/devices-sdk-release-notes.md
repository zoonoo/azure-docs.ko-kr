---
title: Speech Devices SDK 설명서
titleSuffix: Azure Cognitive Services
description: 릴리스 정보 - 가장 최근 릴리스에서 변경된 내용
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: fab23c86ee2e8f654ac4a9619c39d05ee4e9e4c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237928"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK의 릴리스 정보
다음 섹션에는 최신 릴리스의 변경 내용이 나와 있습니다.

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

*   포함 [대화 기록](conversation-transcription-service.md) 샘플 앱에서입니다.
*   업데이트를 [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 1.5.1을 구성 합니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services Speech Devices SDK 1.5.0: 2019 월 릴리스

*   음성 장치 SDK는 이제 GA 및 제어 된 체크 인된 미리 보기가 더 이상.
*   업데이트를 [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 1.5.0 버전 구성 요소입니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
*   절전 모드 해제 단어 신기술 상당한 품질 향상을 제공 하 고 주요 변경 내용을 참조 하십시오.
*   향상 된 끝 필드 인식에 대 한 새 오디오 처리 파이프라인.

**주요 변경 내용**

*   새 인해 wake word 기술 향상 된 절전 모드 해제 단어 포털에서 절전 모드 해제 하는 모든 단어를 다시 만들어야 합니다. 완전히 제거 하려면 장치에서 이전 키워드는 기존 앱을 제거 합니다.
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services Speech Devices SDK 1.4.0: 2019 년-4 월 릴리스 

* 업데이트를 [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 버전 1.4.0 구성 요소입니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요. 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services Speech Devices SDK 1.3.1: 2019-3 월 릴리스 

* 업데이트를 [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 버전 1.3.1 구성 요소입니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요. 
*   처리 하는 업데이트 된 절전 모드 해제 단어의 주요 변경 내용을 참조 하세요.
*   샘플 응용 프로그램 모두 음성 인식 및 변환에 대 한 언어를 추가합니다.

**주요 변경 내용** 

*   [절전 모드 해제 단어 설치](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) 되었습니다 간체, 이제 앱의 일부 이며 별도 장치에 설치할 필요가 없습니다.
*   절전 모드 해제 단어 인식 변경 하 고 두 이벤트는 지원 합니다.
    - RecognizingKeyword 합니다 (확인 되지 않은) 키워드 텍스트를 포함 하는 음성 결과 나타냅니다.
    - RecognizedKeyword, 지정 된 키워드가 인식 완료 하는 키워드 인식을 나타냅니다.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Cognitive Services Speech Devices SDK 1.1.0: 2018년 11월 릴리스 

* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.1.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요. 
* 향상된 오디오 처리 알고리즘으로 원거리 음성 인식 정확도가 향상되었습니다.
* 샘플 애플리케이션에 중국어 음성 인식 지원이 추가되었습니다.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services Speech Devices SDK 1.0.1: 2018년 10월 릴리스 

* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.0.1 버전으로 업데이트되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요. 
* 향상된 오디오 처리 알고리즘을 사용하여 음성 인식 정확도가 향상됩니다.  
* 연속 인식 오디오 세션 버그 하나가 해결되었습니다.

**주요 변경 내용** 

* 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다. API와 관련된 자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요. 
* KWS 모델 파일은 Speech Devices SDK 1.0.1과 호환되지 않습니다. 새로운 절전 모드 해제 단어 파일이 디바이스에 기록되면 기존의 절전 모드 해제 단어 파일이 삭제됩니다. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services Speech Devices SDK 0.5.0: 2018년 8월 릴리스

* 오디오 처리 코드에서 버그를 수정하여 음성 인식 정확도를 향상했습니다.
* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 0.5.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)를 참조하세요.

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services Speech Devices SDK 0.2.12733: 2018년 5월 릴리스

Cognitive Services Speech Devices SDK의 첫 번째 공개 미리 보기 릴리스입니다.
