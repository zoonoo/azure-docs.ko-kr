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
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464751"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK의 릴리스 정보
다음 섹션에는 최신 릴리스의 변경 내용이 나와 있습니다.

## <a name="speech-devices-sdk-160"></a>음성 장치 SDK 1.6.0:

*   일반적인 [샘플 응용 프로그램](https://aka.ms/sdsdk-download) 을 사용 하 여 Windows 및 Linux에서 [Azure Kinect 진한](https://azure.microsoft.com/services/kinect-dk/) 지원
*   [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.6.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="speech-devices-sdk-151"></a>음성 장치 SDK 1.5.1:

*   샘플 앱에 [대화 기록을](conversation-transcription-service.md) 포함 합니다.
*   [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.5.1 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services 음성 장치 SDK 1.5.0:2019-릴리스 수

*   음성 장치 SDK는 이제 GA 이며 더 이상 제어 되는 미리 보기가 아닙니다.
*   [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.5.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
*   새로운 키워드 기술은 상당한 품질 향상을 제공 하며 주요 변경 내용을 참조 하세요.
*   새 오디오 처리 파이프라인.

**주요 변경 내용**

*   새로운 키워드 기술 덕분에 향상 된 키워드 포털에서 모든 키워드를 다시 만들어야 합니다. 장치에서 이전 키워드를 완전히 제거 하려면 이전 앱을 제거 합니다.
    - adb coginitiveservices. sdsdkstarterapp를 제거 합니다.

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services 음성 장치 SDK 1.4.0:2019-Apr 릴리스

* [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.4.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services 음성 장치 SDK 1.3.1:2019-3 월 릴리스

* [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.3.1 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
*   업데이트 된 키워드 처리, 주요 변경 내용을 참조 하세요.
*   샘플 응용 프로그램은 음성 인식과 번역을 위한 언어 선택 항목을 추가 합니다.

**주요 변경 내용**

*   [키워드를 설치](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) 하는 것은 간소화 되었으므로 이제 앱의 일부 이며 장치에 별도로 설치할 필요가 없습니다.
*   키워드 인식이 변경 되었으며 두 개의 이벤트가 지원 됩니다.
    - RecognizingKeyword는 음성 결과에 (확인 되지 않은) 키워드 텍스트가 포함 되어 있음을 나타냅니다.
    - RecognizedKeyword는 키워드 인식이 지정 된 키워드를 인식 하 여 완료 되었음을 나타냅니다.


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
* KWS 모델 파일은 Speech Devices SDK 1.0.1과 호환되지 않습니다. 새 키워드 파일이 장치에 기록 된 후 기존 키워드 파일이 삭제 됩니다.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services Speech Devices SDK 0.5.0: 2018년 8월 릴리스

* 오디오 처리 코드에서 버그를 수정하여 음성 인식 정확도를 향상했습니다.
* [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 0.5.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)를 참조하세요.

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services Speech Devices SDK 0.2.12733: 2018년 5월 릴리스

Cognitive Services Speech Devices SDK의 첫 번째 공개 미리 보기 릴리스입니다.
