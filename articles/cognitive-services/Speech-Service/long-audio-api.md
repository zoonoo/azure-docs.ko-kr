---
title: 긴 오디오 API (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: f03e32eb17c7c02d8969ea6f79663a5c99196d7b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931946"
---
# <a name="long-audio-api-preview"></a>긴 오디오 API (미리 보기)

긴 오디오 API는 긴 형식 텍스트를 음성으로 통합 (예: 오디오 서적) 하도록 설계 되었습니다. 이 API는 합성 오디오를 실시간으로 반환 하지 않습니다. 대신 응답을 폴링하고 서비스에서 제공 되는 출력을 사용 하는 것이 예상 됩니다. 음성 SDK에서 사용 하는 텍스트-음성 API와 달리 긴 오디오 API는 10 분 이상 합성 오디오를 만들 수 있으므로 게시자 및 오디오 콘텐츠 플랫폼에 이상적입니다.

긴 오디오 API의 추가 이점은 다음과 같습니다.

* 서비스에서 반환 되는 합성 음성은 고화질 오디오 출력을 보장 하는 신경망을 사용 합니다.
* 실시간 응답은 지원 되지 않으므로 음성 끝점을 배포할 필요가 없습니다.

> [!NOTE]
> 긴 오디오 API는 이제 [사용자 지정 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)만 지원 합니다.

## <a name="workflow"></a>워크플로

일반적으로 긴 오디오 API를 사용 하는 경우 합성 될 텍스트 파일을 제출 하 고, 상태를 폴링하고, 상태가 성공 인 경우 오디오 출력을 다운로드할 수 있습니다.

이 다이어그램에서는 워크플로의 개략적인 개요를 제공 합니다.

![긴 오디오 API 워크플로 다이어그램](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비 하는 경우 다음을 확인 합니다.

* 일반 텍스트 (.txt) 또는 SSML 텍스트 (.txt) 중 하나입니다.
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** -View [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) 에 따라 구분 됩니다.
  * SSML 텍스트의 경우 각 SSML 조각이 단락으로 간주 됩니다. SSML 부분은 다른 단락-뷰 [ssml 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)로 구분 해야 합니다. 언어 코드의 경우 [SSML (Speech 합성 Markup language)](speech-synthesis-markup.md) 을 참조 하세요.
* 는 [BOM (바이트 순서 표시)을 사용 하 여 u t f-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) 로 인코딩됩니다.
* 1만 자 이상 또는 50 단락 이상 포함
* 는 zip이 아니라 단일 파일입니다.

## <a name="audio-output-formats"></a>오디오 출력 형식

긴 오디오 API에서 지원 되는 오디오 출력 형식은 다음과 같습니다.

> [!NOTE]
> 기본 오디오 형식은 riff-16khz-16 비트입니다.

* riff-8khz-16 비트
* riff-16khz-16 비트
* riff-24khz-16 비트
* riff-48khz-16 비트
* 오디오-16khz-32kbitrate 전송률-mono-mp3
* 오디오-16khz-64kbitrate 전송률-mono-mp3
* 오디오-16khz-128kbitrate 전송률-mono-mp3
* 오디오-24khz-48kbitrate 전송률-mono-mp3
* 오디오-24khz-96kbitrate 전송률-mono-mp3
* 오디오-24khz-160kbitrate 전송률-mono-mp3

## <a name="quickstarts"></a>빠른 시작

긴 오디오 API를 성공적으로 실행 하는 데 도움이 되는 빠른 시작을 제공 합니다. 이 표에는 언어별 구성 된 긴 오디오 API 퀵 스타트 목록이 포함 되어 있습니다.

* [빠른 시작: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>샘플 코드
긴 오디오 API에 대 한 샘플 코드는 GitHub에서 사용할 수 있습니다.

* [샘플 코드: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [샘플 코드:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
