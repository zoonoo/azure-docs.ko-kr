---
title: 긴 오디오 API (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 긴 오디오 API가 긴 형식의 텍스트를 음성으로 통합 하기 위해 디자인 되는 방법에 대해 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: 0e18fd0c52fd4090477599f53cd0ef0bc05855f2
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587343"
---
# <a name="long-audio-api-preview"></a>긴 오디오 API (미리 보기)

긴 오디오 API는 긴 형식 텍스트를 음성으로 통합 (예: 오디오 서적, 뉴스 기사 및 문서) 하는 데 적합 합니다. 이 API는 합성 오디오를 실시간으로 반환 하지 않습니다. 대신 응답을 폴링하고 서비스에서 제공 되는 출력을 사용 하는 것이 예상 됩니다. 음성 SDK에서 사용 하는 텍스트-음성 API와 달리 긴 오디오 API는 10 분 이상 합성 오디오를 만들 수 있으므로 게시자 및 오디오 콘텐츠 플랫폼에 이상적입니다.

긴 오디오 API의 추가 이점은 다음과 같습니다.

* 서비스에서 반환 되는 합성 된 음성은 가장 적합 한 신경망을 사용 합니다.
* 합성 음성 끝점은 실시간 일괄 처리 모드가 아닌 음성으로 배포할 필요가 없습니다.

> [!NOTE]
> 이제 긴 오디오 API는 [공용 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) 및 [사용자 지정 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)을 모두 지원 합니다.

## <a name="workflow"></a>워크플로

일반적으로 긴 오디오 API를 사용 하는 경우 합성 될 텍스트 파일을 제출 하 고, 상태를 폴링하고, 상태가 성공 인 경우 오디오 출력을 다운로드할 수 있습니다.

이 다이어그램에서는 워크플로의 개략적인 개요를 제공 합니다.

![긴 오디오 API 워크플로 다이어그램](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비 하는 경우 다음을 확인 합니다.

* 일반 텍스트 (.txt) 또는 SSML 텍스트 (.txt) 중 하나입니다.
* 는 [BOM (바이트 순서 표시)을 사용 하 여 u t f-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) 로 인코딩됩니다.
* 는 zip이 아니라 단일 파일입니다.
* 일반 텍스트의 경우 400 자 보다 크거나, SSML 텍스트에 대 한 400 [청구 가능 문자](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) 를 포함 하며, 1만 단락 보다 작음
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** -View [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) 에 따라 구분 됩니다.
  * SSML 텍스트의 경우 각 SSML 조각이 단락으로 간주 됩니다. [Ssml 부분은](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) 다른 단락으로 구분 해야 합니다.
> [!NOTE]
> 중국어 (중국), 중국어 (홍콩), 중국어 (대만), 일본어 및 한국어의 경우 한 단어는 두 문자로 계산 됩니다. 

## <a name="submit-synthesis-requests"></a>합성 요청 제출

입력 콘텐츠를 준비한 후에는 [긴 형식의 오디오 합성 퀵 스타트](https://aka.ms/long-audio-python) 를 따라 요청을 제출 합니다. 둘 이상의 입력 파일이 있는 경우 여러 요청을 제출 해야 합니다. 

**HTTP 상태 코드** 는 일반적인 오류를 표시 합니다.

| API | HTTP 상태 코드 | 설명 | 제안 |
|-----|------------------|-------------|----------|
| 만들기 | 400 | 음성 합성은이 지역에서 사용할 수 없습니다. | 음성 구독 키를 지원 되는 지역으로 변경 합니다. |
|        | 400 | 이 영역에 대 한 **표준** 음성 구독만 유효 합니다. | 음성 구독 키를 "표준" 가격 책정 계층으로 변경 합니다. |
|        | 400 | Azure 계정에 대 한 2만 요청 제한을 초과 합니다. 새 요청을 제출 하기 전에 일부 요청을 제거 하세요. | 서버는 각 Azure 계정에 대해 최대 2만 개의 요청을 유지 합니다. 새 요청을 제출 하기 전에 일부 요청을 삭제 합니다. |
|        | 400 | 이 모델은 음성 합성에서 사용할 수 없습니다. {modelID}. | {ModelID}의 상태가 올바른지 확인 하세요. |
|        | 400 | 요청 지역이 {modelID} 모델의 지역과 일치 하지 않습니다. | {ModelID}의 지역이 요청 지역과 일치 하는지 확인 합니다. |
|        | 400 | 음성 합성은 바이트 순서 표식을 사용 하 여 UTF-8 인코딩의 텍스트 파일만 지원 합니다. | 입력 파일이 바이트 순서 표식을 사용 하 여 UTF-8 인코딩으로 되어 있는지 확인 합니다. |
|        | 400 | 음성 합성 요청에서는 유효한 SSML 입력만 허용 됩니다. | 입력 SSML 식이 올바른지 확인 합니다. |
|        | 400 | 입력 파일에 음성 이름 {voiceName}이 (가) 없습니다. | 입력 SSML 음성 이름이 모델 ID와 맞지 않습니다. |
|        | 400 | 입력 파일의 단락 크기는 1만 미만 이어야 합니다. | 파일의 단락이 1만 미만 인지 확인 합니다. |
|        | 400 | 입력 파일은 400 자이 하 여야 합니다. | 입력 파일이 400 자를 초과 하는지 확인 합니다. |
|        | 404 | 음성 합성 정의에 선언 된 모델을 찾을 수 없습니다. {modelID}. | {ModelID}가 올바른지 확인 하세요. |
|        | 429 | 활성 음성 합성 제한을 초과 합니다. 일부 요청이 완료 될 때까지 기다려 주세요. | 서버를 실행 하 고 각 Azure 계정에 대해 최대 120 개의 요청을 큐에 대기 시킬 수 있습니다. 잠시 기다렸다가 일부 요청이 완료 될 때까지 새 요청을 제출 하지 마세요. |
| 모두       | 429 | 요청이 너무 많습니다. | 클라이언트는 각 Azure 계정에 대해 초당 최대 5 개의 요청을 서버에 제출할 수 있습니다. 초당 요청 크기를 줄이십시오. |
| 삭제    | 400 | 음성 합성 작업을 계속 사용 하 고 있습니다. | **완료** 또는 **실패**한 요청만 삭제할 수 있습니다. |
| GetByID   | 404 | 지정 된 엔터티를 찾을 수 없습니다. | 합성 ID가 올바른지 확인 합니다. |

## <a name="regions-and-endpoints"></a>지역 및 엔드포인트

긴 오디오 API는 고유한 끝점을 사용 하 여 여러 지역에서 사용할 수 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 오스트레일리아 동부 | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| 캐나다 중부 | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| 미국 동부 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 인도 중부 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 미국 중남부 | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| 동남아시아 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 영국 남부 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 서유럽 | `https://westeurope.customvoice.api.speech.microsoft.com` |
| 미국 서부 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>오디오 출력 형식

유연한 오디오 출력 형식을 지원 합니다. 단락 당 오디오 출력을 생성 하거나 ' concatenateResult ' 매개 변수를 설정 하 여 오디오 출력을 단일 출력에 연결할 수 있습니다. 긴 오디오 API에서 지원 되는 오디오 출력 형식은 다음과 같습니다.

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
* [샘플 코드: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
