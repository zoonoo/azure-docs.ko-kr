---
title: 긴 오디오 API-음성 서비스
titleSuffix: Azure Cognitive Services
description: 긴 오디오 API가 긴 형식의 텍스트를 음성으로 통합 하기 위해 디자인 되는 방법에 대해 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618409"
---
# <a name="long-audio-api"></a>긴 오디오 API

긴 오디오 API는 긴 형식 텍스트를 음성으로 통합 (예: 오디오 서적, 뉴스 기사 및 문서) 하는 데 적합 합니다. 이 API는 합성 오디오를 실시간으로 반환 하지 않습니다. 대신 응답을 폴링하고 서비스에서 제공 되는 출력을 사용 하는 것이 예상 됩니다. 음성 SDK에서 사용 하는 텍스트 및 음성 API와는 달리 긴 오디오 API는 10 분 이상 합성 오디오를 만들 수 있으므로 게시자 및 오디오 콘텐츠 플랫폼에서 일괄 처리의 오디오 서적 같은 긴 오디오 콘텐츠를 만드는 데 적합 합니다.

긴 오디오 API의 추가 이점은 다음과 같습니다.

* 서비스에서 반환 되는 합성 된 음성은 가장 적합 한 신경망을 사용 합니다.
* 합성 음성 끝점은 실시간 일괄 처리 모드가 아닌 음성으로 배포할 필요가 없습니다.

> [!NOTE]
> 이제 긴 오디오 API는 [공용 신경망](./language-support.md#neural-voices) 및 [사용자 지정 신경망](./how-to-custom-voice.md#custom-neural-voices)을 모두 지원 합니다.

## <a name="workflow"></a>워크플로

일반적으로 긴 오디오 API를 사용 하는 경우 합성 될 텍스트 파일을 제출 하 고, 상태를 폴링하고, 상태가 성공 인 경우 오디오 출력을 다운로드할 수 있습니다.

이 다이어그램에서는 워크플로의 개략적인 개요를 제공 합니다.

![긴 오디오 API 워크플로 다이어그램](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비 하는 경우 다음을 확인 합니다.

* 일반 텍스트 (.txt) 또는 SSML 텍스트 (.txt) 중 하나입니다.
* 는 [BOM (바이트 순서 표시)을 사용 하 여 u t f-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) 로 인코딩됩니다.
* 는 zip이 아니라 단일 파일입니다.
* 일반 텍스트의 경우 400 자 보다 크거나, SSML 텍스트에 대 한 400 [청구 가능 문자](./text-to-speech.md#pricing-note) 를 포함 하며, 1만 단락 보다 작음
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** -View [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) 에 따라 구분 됩니다.
  * SSML 텍스트의 경우 각 SSML 조각이 단락으로 간주 됩니다. [Ssml 부분은](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) 다른 단락으로 구분 해야 합니다.

## <a name="sample-code"></a>샘플 코드
이 페이지의 나머지 부분에서는 Python에 집중 하지만 긴 오디오 API에 대 한 샘플 코드는 GitHub에서 다음과 같은 프로그래밍 언어에 사용할 수 있습니다.

* [샘플 코드: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [샘플 코드: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python 예제

이 섹션에는 긴 오디오 API의 기본 사용법을 보여 주는 Python 예제가 포함 되어 있습니다. 즐겨찾는 IDE 또는 편집기를 사용하여 새 Python 프로젝트를 만듭니다. 그런 다음이 코드 조각을 이라는 파일에 복사 `long_audio_synthesis_client.py` 합니다.

```python
import json
import ntpath
import requests
```

이러한 라이브러리는 HTTP 요청을 생성 하 고 텍스트 음성 변환 긴 오디오 합성 REST API 호출 하는 데 사용 됩니다.

### <a name="get-a-list-of-supported-voices"></a>지원 되는 음성 목록 가져오기

지원 되는 음성 목록을 가져오려면에 GET 요청을 보냅니다 `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


이 코드를 사용 하 여 특정 지역/끝점에 대 한 전체 음성 목록을 가져올 수 있습니다.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

다음 값을 바꿉니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.
* 를 `<region>` 음성 리소스가 생성 된 지역 (예: `eastus` 또는)으로 바꿉니다 `westus` . 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.

다음과 같은 출력이 표시 됩니다.

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

**속성. publicAvailable** 이 **true** 인 경우 음성은 공용 신경망입니다. 그렇지 않으면 사용자 지정 신경망입니다.

### <a name="convert-text-to-speech"></a>텍스트 음성 변환

일반 텍스트 또는 SSML 텍스트에서 입력 텍스트 파일을 준비한 후에 다음 코드를 추가 합니다 `long_audio_synthesis_client.py` .

> [!NOTE]
> `concatenateResult` 는 선택적 매개 변수입니다. 이 매개 변수를 설정 하지 않으면 오디오 출력이 단락 단위로 생성 됩니다. 매개 변수를 설정 하 여 오디오 된 os를 1 개의 출력으로 연결할 수도 있습니다. 
> `outputFormat` 도 선택 사항입니다. 기본적으로 오디오 출력은 riff-16khz-16 비트로 설정 됩니다. 지원 되는 오디오 출력 형식에 대 한 자세한 내용은 [오디오 출력 형식](#audio-output-formats)을 참조 하세요.

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

다음 값을 바꿉니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.
* 를 `<region>` 음성 리소스가 생성 된 지역 (예: `eastus` 또는)으로 바꿉니다 `westus` . 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.
* `<input_file_path>`텍스트 음성 변환에 대해 준비한 텍스트 파일의 경로로 대체 합니다.
* `<locale>`원하는 출력 로캘로 대체 합니다. 자세한 내용은 [언어 지원](language-support.md#neural-voices)을 참조 하세요.

끝점에 대 한 이전 호출에서 반환 된 음성 중 하나를 사용 `/voices` 합니다.

* 공용 신경망을 사용 하는 경우을 `<voice_name>` 원하는 출력 음성으로 바꿉니다.
* 사용자 지정 신경망을 사용 하려면 변수를 `voice_identities` 다음으로 바꾸고 `<voice_id>` `id` 사용자 지정 신경망의로 대체 합니다.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

다음과 같은 출력이 표시 됩니다.

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> 입력 파일이 2 개 이상 있는 경우 여러 요청을 제출 해야 합니다. 유의 해야 하는 몇 가지 제한 사항이 있습니다.
> * 클라이언트는 각 Azure 구독 계정에 대해 초당 최대 **5 개의** 요청을 서버에 제출할 수 있습니다. 이 제한이 초과 되 면 클라이언트는 429 오류 코드 (너무 많은 요청)를 가져옵니다. 초당 요청 크기를 줄이십시오.
> * 서버를 실행 하 고 각 Azure 구독 계정에 대해 최대 **120** 요청을 큐에 대기 시킬 수 있습니다. 이 제한이 초과 되 면 서버는 429 오류 코드 (너무 많은 요청)를 반환 합니다. 잠시 기다렸다가 일부 요청이 완료 될 때까지 새 요청을 제출 하지 마세요.

출력의 URL은 요청 상태를 가져오는 데 사용할 수 있습니다.

### <a name="get-information-of-a-submitted-request"></a>제출 된 요청에 대 한 정보 가져오기

제출 된 합성 요청의 상태를 가져오려면 이전 단계에서 반환 된 URL에 GET 요청을 보냅니다.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
출력은 다음과 같습니다.
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

`status`속성에서이 요청의 상태를 읽을 수 있습니다. 요청은 상태에서 시작 되 고 `NotStarted` ,로 변경 되 `Running` 고, 마지막으로 또는가 됩니다 `Succeeded` `Failed` . 상태가가 될 때까지 루프를 사용 하 여이 API를 폴링할 수 있습니다 `Succeeded` .

### <a name="download-audio-result"></a>오디오 결과 다운로드

합성 요청이 성공 하면 GET API를 호출 하 여 오디오 결과를 다운로드할 수 있습니다 `/files` .

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
`<request_id>`결과를 다운로드 하려는 요청 ID로 대체 합니다. 이전 단계의 응답에서 찾을 수 있습니다.

출력은 다음과 같습니다.
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
출력에는 2 개 파일의 정보가 포함 됩니다. 을 사용 하는 경우 `"kind": "LongAudioSynthesisScript"` 입력 스크립트가 전송 됩니다. 다른 하나는 `"kind": "LongAudioSynthesisResult"` 이 요청의 결과입니다.
결과는 입력 텍스트의 복사본과 함께 생성 된 오디오 출력 파일을 포함 하는 zip입니다.

두 파일은 모두 해당 속성의 URL에서 다운로드할 수 있습니다 `links.contentUrl` .

### <a name="get-all-synthesis-requests"></a>모든 합성 요청 가져오기

다음 코드를 사용 하 여 제출 된 모든 요청 목록을 가져올 수 있습니다.

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

출력은 다음과 같습니다.
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` 속성은 합성 요청 목록을 포함 합니다. 최대 페이지 크기가 100 인 목록의 페이지가 매겨집니다. 100 개가 넘는 요청이 있는 경우 `"@nextLink"` 페이지가 매겨진 목록의 다음 페이지를 가져오도록 속성이 제공 됩니다.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

`skip`URL 매개 변수를 제공 하 여 페이지 크기를 사용자 지정 하 고 번호를 건너뛸 수도 있습니다 `top` .

### <a name="remove-previous-requests"></a>이전 요청 제거

서비스는 각 Azure 구독 계정에 대해 최대 **2만** 개의 요청을 유지 합니다. 요청 금액이이 제한을 초과 하는 경우 새 요청을 만들기 전에 이전 요청을 제거 하세요. 기존 요청을 제거 하지 않으면 오류 알림이 수신 됩니다.

다음 코드에서는 특정 합성 요청을 제거 하는 방법을 보여 줍니다.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

요청이 성공적으로 제거 되 면 응답 상태 코드는 HTTP 204 (콘텐츠 없음)입니다.

```console
response.status_code: 204
```

> [!NOTE]
> 또는 상태의 요청은 `NotStarted` `Running` 제거 하거나 삭제할 수 없습니다.

완성 된는 `long_audio_synthesis_client.py` [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)에서 사용할 수 있습니다.

## <a name="http-status-codes"></a>HTTP 상태 코드

다음 표에서는 REST API의 HTTP 응답 코드 및 메시지에 대해 자세히 설명 합니다.

| API | HTTP 상태 코드 | Description | 솔루션 |
|-----|------------------|-------------|----------|
| 생성 | 400 | 음성 합성은이 지역에서 사용할 수 없습니다. | 음성 구독 키를 지원 되는 지역으로 변경 합니다. |
|        | 400 | 이 영역에 대 한 **표준** 음성 구독만 유효 합니다. | 음성 구독 키를 "표준" 가격 책정 계층으로 변경 합니다. |
|        | 400 | Azure 계정에 대 한 2만 요청 제한을 초과 합니다. 새 요청을 제출 하기 전에 일부 요청을 제거 하세요. | 서버는 각 Azure 계정에 대해 최대 2만 개의 요청을 유지 합니다. 새 요청을 제출 하기 전에 일부 요청을 삭제 합니다. |
|        | 400 | 이 모델은 음성 합성에서 사용할 수 없습니다. {modelID}. | {ModelID}의 상태가 올바른지 확인 하세요. |
|        | 400 | 요청 지역이 {modelID} 모델의 지역과 일치 하지 않습니다. | {ModelID}의 지역이 요청 지역과 일치 하는지 확인 합니다. |
|        | 400 | 음성 합성은 바이트 순서 표식을 사용 하 여 UTF-8 인코딩의 텍스트 파일만 지원 합니다. | 입력 파일이 바이트 순서 표식을 사용 하 여 UTF-8 인코딩으로 되어 있는지 확인 합니다. |
|        | 400 | 음성 합성 요청에서는 유효한 SSML 입력만 허용 됩니다. | 입력 SSML 식이 올바른지 확인 합니다. |
|        | 400 | 입력 파일에 음성 이름 {voiceName}이 (가) 없습니다. | 입력 SSML 음성 이름이 모델 ID와 맞지 않습니다. |
|        | 400 | 입력 파일의 단락 수는 1만 미만 이어야 합니다. | 파일의 단락 수가 1만 미만 인지 확인 합니다. |
|        | 400 | 입력 파일은 400 자이 하 여야 합니다. | 입력 파일이 400 자를 초과 하는지 확인 합니다. |
|        | 404 | 음성 합성 정의에 선언 된 모델을 찾을 수 없습니다. {modelID}. | {ModelID}가 올바른지 확인 하세요. |
|        | 429 | 활성 음성 합성 제한을 초과 합니다. 일부 요청이 완료 될 때까지 기다려 주세요. | 서버를 실행 하 고 각 Azure 계정에 대해 최대 120 개의 요청을 큐에 대기 시킬 수 있습니다. 잠시 기다렸다가 일부 요청이 완료 될 때까지 새 요청을 제출 하지 마세요. |
| 모두       | 429 | 요청이 너무 많습니다. | 클라이언트는 각 Azure 계정에 대해 초당 최대 5 개의 요청을 서버에 제출할 수 있습니다. 초당 요청 크기를 줄이십시오. |
| DELETE    | 400 | 음성 합성 작업을 계속 사용 하 고 있습니다. | **완료** 또는 **실패** 한 요청만 삭제할 수 있습니다. |
| GetByID   | 404 | 지정 된 엔터티를 찾을 수 없습니다. | 합성 ID가 올바른지 확인 합니다. |

## <a name="regions-and-endpoints"></a>지역 및 엔드포인트

긴 오디오 API는 고유한 끝점을 사용 하 여 여러 지역에서 사용할 수 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 미국 동부 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 인도 중부 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 동남아시아 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 영국 남부 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 서유럽 | `https://westeurope.customvoice.api.speech.microsoft.com` |

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
