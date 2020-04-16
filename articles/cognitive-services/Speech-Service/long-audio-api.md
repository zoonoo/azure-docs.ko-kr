---
title: 긴 오디오 API (미리 보기) - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 긴 오디오 API가 음성으로 긴 형식 텍스트의 비동기 합성을 위해 어떻게 설계되었는지 알아보십시오.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401066"
---
# <a name="long-audio-api-preview"></a>긴 오디오 API(미리 보기)

Long Audio API는 음성으로 긴 형식의 텍스트(예: 오디오 북)의 비동기 합성을 위해 설계되었습니다. 이 API는 합성된 오디오를 실시간으로 반환하지 않고 응답에 대해 폴링하고 서비스에서 사용할 수 있는 출력을 소비할 것으로 예상됩니다. 음성 SDK에서 사용하는 텍스트 음성 변환 API와 달리 Long Audio API는 10분 이상 합성된 오디오를 생성할 수 있으므로 게시자 및 오디오 콘텐츠 플랫폼에 이상적입니다.

긴 오디오 API의 추가 혜택:

* 서비스에서 반환되는 합성 음성은 고충실도 오디오 출력을 보장하는 신경 음성을 사용합니다.
* 실시간 응답은 지원되지 않으므로 음성 끝점을 배포할 필요가 없습니다.

> [!NOTE]
> 이제 긴 오디오 API는 [사용자 지정 신경음성만](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)지원합니다.

## <a name="workflow"></a>워크플로

일반적으로 Long Audio API를 사용하는 경우 합성할 텍스트 파일이나 파일을 제출하고 상태를 폴링한 다음 상태가 성공하면 오디오 출력을 다운로드할 수 있습니다.

이 다이어그램은 워크플로에 대한 개량된 개요를 제공합니다.

![긴 오디오 API 워크플로다](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비할 때 다음을 확인하십시오.

* 일반 텍스트(.txt) 또는 SSML 텍스트(.txt)
* [바이트 순서 표시 (BOM)와 UTF-8로](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) 인코딩 됩니다.
* 지퍼가 아닌 단일 파일입니다.
* 일반 텍스트의 경우 400자 이상, SSML 텍스트의 [경우 청구 가능한 문자](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) 400자, 단락 10,000자 미만 포함
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** - [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) 보기를 입력하여 구분됩니다.
  * SSML 텍스트의 경우 각 SSML 조각은 단락으로 간주됩니다. SSML 조각은 다른 단락으로 구분되어야한다 - 보기 [SSML 텍스트 입력 예](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 중국어(본토), 중국어(홍콩), 중국어(대만), 일본어 및 한국어의 경우 한 단어는 두 문자로 계산됩니다. 

## <a name="submit-synthesis-requests"></a>합성 요청 제출

입력 컨텐션을 준비한 후, [긴 형태의 오디오 합성 퀵스타트를](https://aka.ms/long-audio-python) 따라 요청을 제출한다. 입력 파일이 두 개 이상인 경우 여러 요청을 제출해야 합니다. 주의해야 할 몇 가지 제한 사항이 있습니다. 
* 클라이언트는 각 Azure 구독 계정에 대해 초당 최대 5개의 요청을 서버에 제출할 수 있습니다. 제한을 초과하면 클라이언트는 429 오류 코드(요청이 너무 많음)를 받게 됩니다. 초당 요청 금액을 줄여주세요.
* 서버는 각 Azure 구독 계정에 대해 최대 120개의 요청을 실행하고 큐에 대기할 수 있습니다. 제한을 초과하면 서버는 429 오류 코드(요청이 너무 많음)를 반환합니다. 일부 요청이 완료될 때까지 기다렸다가 새 요청을 제출하지 마십시오.
* 서버는 각 Azure 구독 계정에 대해 최대 20,000개의 요청을 유지합니다. 제한을 초과하는 경우 새 요청을 제출하기 전에 일부 요청을 삭제하십시오.

## <a name="audio-output-formats"></a>오디오 출력 형식

유연한 오디오 출력 형식을 지원합니다. 단락당 오디오 출력을 생성하거나 'connateresult' 매개 변수를 설정하여 오디오를 하나의 출력으로 연결할 수 있습니다. 다음 오디오 출력 형식은 Long Audio API에서 지원됩니다.

> [!NOTE]
> 기본 오디오 형식은 리프-16khz-16비트 모노-pcm입니다.

* 리프-8khz-16비트 모노-pcm
* 리프-16khz-16비트 모노-pcm
* 리프-24khz-16비트 모노-pcm
* 리프-48khz-16비트 모노-pcm
* 오디오 16khz-32kbitrate-모노-mp3
* 오디오 16khz-64kbitrate-모노-mp3
* 오디오 16khz-128kbitrate-모노-mp3
* 오디오 24khz-48kbitrate-모노-mp3
* 오디오 24khz-96kbitrate-모노-mp3
* 오디오 24khz-160kbitrate-모노-mp3

## <a name="quickstarts"></a>빠른 시작

우리는 당신이 성공적으로 긴 오디오 API를 실행할 수 있도록 설계 빠른 시작을 제공합니다. 이 표에는 언어별로 구성된 Long Audio API 빠른 시작 목록이 포함되어 있습니다.

* [빠른 시작: 파이썬](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>예제 코드
긴 오디오 API에 대한 샘플 코드는 GitHub에서 사용할 수 있습니다.

* [샘플 코드: 파이썬](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [샘플 코드: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: 자바](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
