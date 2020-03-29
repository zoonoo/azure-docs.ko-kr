---
title: Speech Devices SDK 설명서
titleSuffix: Azure Cognitive Services
description: 릴리스 노트는 음성 장치 SDK에 대한 업데이트, 개선 사항, 버그 수정 및 변경 사항에 대한 로그를 제공합니다. 이 문서는 음성 장치 SDK의 각 릴리스와 함께 업데이트됩니다.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371612"
---
# <a name="release-notes-speech-devices-sdk"></a>릴리스 정보: 음성 장치 SDK

다음 섹션에는 최신 릴리스의 변경 내용이 나와 있습니다.

## <a name="speech-devices-sdk-190"></a>음성 장치 SDK 1.9.0:

- [Urbetter DDK(Linux](https://aka.ms/sdsdk-download-urbetter) ARM64)에 대한 초기 바이너리가 제공됩니다.
- 루보 v1은 이제 음성 SDK에 메이븐을 사용
- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.9.0으로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="speech-devices-sdk-170"></a>음성 장치 SDK 1.7.0:

- 리눅스 ARM은 이제 지원됩니다.
- [루보 v2 DDK에](https://aka.ms/sdsdk-download-roobov2) 대한 초기 바이너리가 제공됩니다 (리눅스 ARM64).
- Windows 사용자는 `AudioConfig.fromDefaultMicrophoneInput()` 사용할 `AudioConfig.fromMicrophoneInput(deviceName)` 마이크를 사용하거나 지정할 수 있습니다.
- 라이브러리 크기가 최적화되었습니다.
- 동일한 음성/의도 인식기 개체를 사용하여 다중 회전 인식 지원.
- 인식을 중지하는 동안 발생하는 간헐적 중단을 수정합니다.
- 샘플 앱에는 이제 파일의 형식을 보여 주는 샘플 participants.properties 파일이 포함되어 있습니다.
- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.7.0으로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="speech-devices-sdk-160"></a>음성 장치 SDK 1.6.0:

- 일반적인 [샘플 응용 프로그램과](https://aka.ms/sdsdk-download) 함께 Windows 및 Linux에서 Azure [Kinect DK](https://azure.microsoft.com/services/kinect-dk/) 지원
- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.6.0으로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="speech-devices-sdk-151"></a>음성 장치 SDK 1.5.1:

- 샘플 앱에 [대화 기록을](conversation-transcription-service.md) 포함합니다.
- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.5.1로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="speech-devices-sdk-150-2019-may-release"></a>음성 장치 SDK 1.5.0: 2019-5월 릴리스

- 음성 장치 SDK는 이제 GA이며 더 이상 게이트 미리보기가 아닙니다.
- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.5.0으로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
- 새로운 키워드 기술은 주요 품질 향상을 가져온다, 주요 변경 사항을 참조하십시오.
- 원거리 인식 개선을 위한 새로운 오디오 처리 파이프라인.

**호환성이 손상되는 변경**

- 새로운 키워드 기술로 인해 향상된 키워드 포털에서 모든 키워드를 다시 만들어야 합니다. 장치에서 이전 키워드를 완전히 제거하려면 이전 앱을 제거합니다.
  - adb 제거 com.microsoft.cognitiveservices.speech.samples.sddkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>음성 장치 SDK 1.4.0: 2019-4월 릴리스

- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.4.0으로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>음성 장치 SDK 1.3.1: 2019-3 월 릴리스

- 음성 [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소를 버전 1.3.1로 업데이트했습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
- 업데이트된 키워드 처리는 주요 변경 사항을 참조하십시오.
- 샘플 응용 프로그램은 음성 인식 및 번역 모두에 대한 언어의 선택을 추가합니다.

**호환성이 손상되는 변경**

- [키워드 설치가](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) 단순화되었으며 이제 앱의 일부이며 장치에 별도의 설치가 필요하지 않습니다.
- 키워드 인식이 변경되었으며 두 개의 이벤트가 지원됩니다.
  - `RecognizingKeyword,`는 음성 결과가 확인되지 않은 키워드 텍스트를 포함하고 있음을 나타냅니다.
  - `RecognizedKeyword`을 나타내면 지정된 키워드를 인식하여 키워드 인식이 완료됨을 나타냅니다.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>음성 장치 SDK 1.1.0: 2018-11월 릴리스

- [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.1.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
- 향상된 오디오 처리 알고리즘으로 원거리 음성 인식 정확도가 향상되었습니다.
- 샘플 애플리케이션에 중국어 음성 인식 지원이 추가되었습니다.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>음성 장치 SDK 1.0.1: 2018-10월 릴리스

- [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 1.0.1 버전으로 업데이트되었습니다. 해당 [릴리스 정보](https://aka.ms/csspeech/whatsnew)를 참조하세요.
- 향상된 오디오 처리 알고리즘을 사용하여 음성 인식 정확도가 향상됩니다.
- 연속 인식 오디오 세션 버그 하나가 해결되었습니다.

**호환성이 손상되는 변경**

- 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다. API와 관련된 자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요.
- KWS 모델 파일은 Speech Devices SDK 1.0.1과 호환되지 않습니다. 새 키워드 파일이 장치에 기록된 후 기존 키워드 파일이 삭제됩니다.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>음성 장치 SDK 0.5.0: 2018-8월 릴리스

- 오디오 처리 코드에서 버그를 수정하여 음성 인식 정확도를 향상했습니다.
- [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 구성 요소가 0.5.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)를 참조하세요.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>음성 장치 SDK 0.2.12733: 2018-5월 릴리스

Cognitive Services Speech Devices SDK의 첫 번째 공개 미리 보기 릴리스입니다.
