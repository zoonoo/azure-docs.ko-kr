---
title: '빠른 시작: 긴 형식 오디오를 위한 비동기 합성(미리 보기) - 음성 서비스'
titleSuffix: Azure Cognitive Services
description: Long Audio API를 사용하여 텍스트를 비동기적으로 음성으로 변환하고 서비스에서 제공하는 URI에서 오디오 출력을 검색합니다. 이 REST API는 10,000자 또는 50개 단락을 초과하는 텍스트 파일을 합성 된 음성으로 변환해야하는 콘텐츠 공급자에게 이상적입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331079"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>빠른 시작: 파이썬에서 긴 형태의 오디오를 위한 비동기 합성(미리 보기)

이 빠른 시작에서는 Long Audio API를 사용하여 텍스트를 비동기적으로 음성으로 변환하고 서비스에서 제공하는 URI에서 오디오 출력을 검색합니다. 이 REST API는 5,000자(또는 길이가 10분 이상)를 초과하는 텍스트에서 오디오를 합성해야 하는 콘텐츠 공급자에게 이상적입니다. 자세한 내용은 [긴 오디오 API](../../long-audio-api.md)를 참조하십시오.

> [!NOTE]
> 장망의 오디오에 대한 비동기 합성은 [사용자 지정 신경 음부에서만](../../how-to-custom-voice.md#custom-neural-voices)사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에는 다음이 필요합니다.

* 파이썬 2.7.x 또는 3.x.
* [비주얼 스튜디오,](https://visualstudio.microsoft.com/downloads/) [비주얼 스튜디오 코드,](https://code.visualstudio.com/download)또는 좋아하는 텍스트 편집기.
* Azure 구독 및 음성 서비스 구독 키입니다. [Azure 계정을 만들고](../../get-started.md#new-resource) [음성 리소스를 만들어](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) 키를 가져옵니다. Speech 리소스를 만들 때 가격 책정 계층이 **S0으로**설정되어 있고 위치가 [지원되는 지역으로](../../regions.md#standard-and-neural-voices)설정되어 있는지 확인합니다.

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

즐겨찾는 IDE 또는 편집기를 사용하여 새 Python 프로젝트를 만듭니다. 그런 다음 이 코드 조각을 .라는 `voice_synthesis_client.py`파일에 복사합니다.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> 이러한 모듈을 사용하지 않은 경우 프로그램을 실행하기 전에 모듈을 설치해야 합니다. 이러한 패키지를 설치하려면 `pip install requests urllib3`를 실행합니다.

이러한 모듈은 인수를 구문 분석하고, HTTP 요청을 구성하고, 텍스트 음성 변환 긴 오디오 REST API를 호출하는 데 사용됩니다.

## <a name="get-a-list-of-supported-voices"></a>지원되는 음성 목록 받기

이 코드는 텍스트 음성 변환에 사용할 수 있는 사용 가능한 음성 목록을 가져옵니다. 다음 에 `voice_synthesis_client.py`코드를 추가합니다.

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>코드 테스트

지금까지 수행한 작업을 테스트해 보겠습니다. 아래 요청에서 몇 가지 사항을 업데이트해야 합니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.
* Speech `<region>` 리소스가 만들어진 지역(예: `eastus` 또는)으로 `westus`바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.

다음 명령을 실행합니다.

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

다음과 같은 출력이 표시됩니다.

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>입력 파일 준비

입력 텍스트 파일을 준비합니다. 일반 텍스트 또는 SSML 텍스트일 수 있습니다. 입력 파일 요구 사항에 대 한 [합성에 대 한 콘텐츠를 준비](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)하는 방법을 참조 하십시오.

## <a name="convert-text-to-speech"></a>텍스트를 음성으로 변환

입력 텍스트 파일을 준비한 후 음성 합성을 위해 이 코드를 다음으로 `voice_synthesis_client.py`추가합니다.

> [!NOTE]
> '연결결과'는 선택적 매개 변수입니다. 이 매개 변수가 설정되지 않으면 단락당 오디오 출력이 생성됩니다. 매개 변수를 설정하여 오디오를 1출력으로 연결할 수도 있습니다. 기본적으로 오디오 출력은 리프-16khz-16bit-모노-pcm으로 설정됩니다. 지원되는 오디오 출력에 대한 자세한 내용은 [오디오 출력 형식을](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)참조하십시오.

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>코드 테스트

입력 파일을 소스로 사용하여 텍스트를 합성하도록 요청해 보겠습니다. 아래 요청에서 몇 가지 사항을 업데이트해야 합니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.
* Speech `<region>` 리소스가 만들어진 지역(예: `eastus` 또는)으로 `westus`바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.
* 텍스트 `<input>` 음성 변환을 위해 준비한 텍스트 파일의 경로로 바꿉습니다.
* 원하는 `<locale>` 출력 로캘로 바꿉습니다. 자세한 내용은 [언어 지원](../../language-support.md#neural-voices)을 참조하십시오.
* 원하는 `<voice_guid>` 출력 음성으로 바꿉습니다. [지원되는 음성 목록을 가져옵니다.](#get-a-list-of-supported-voices)

이 명령으로 텍스트를 음성으로 변환합니다.

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> 입력 파일이 1개 이상인 경우 여러 요청을 제출해야 합니다. 알고 있어야 할 몇 가지 제한 사항이 있습니다. 
> * 클라이언트는 각 Azure 구독 계정에 대해 초당 최대 **5개의** 요청을 서버에 제출할 수 있습니다. 제한을 초과하면 클라이언트는 429 오류 코드(요청이 너무 많음)를 받게 됩니다. 초당 요청 금액을 줄여주세요.
> * 서버는 각 Azure 구독 계정에 대해 최대 **120개의** 요청을 실행하고 큐에 대기할 수 있습니다. 제한을 초과하면 서버는 429 오류 코드(요청이 너무 많음)를 반환합니다. 일부 요청이 완료될 때까지 기다렸다가 새 요청을 제출하지 마십시오.

다음과 같은 출력이 표시됩니다.

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

결과에는 입력 텍스트와 서비스에 의해 생성된 오디오 출력 파일이 포함됩니다. 이러한 파일은 zip에서 다운로드할 수 있습니다.

## <a name="remove-previous-requests"></a>이전 요청 제거

서버는 각 Azure 구독 계정에 대해 최대 **20,000개의** 요청을 유지합니다. 요청 금액이 이 제한을 초과하는 경우 새 요청을 만들기 전에 이전 요청을 제거하십시오. 기존 요청을 제거하지 않으면 오류 알림이 표시됩니다.

다음 에 `voice_synthesis_client.py`코드를 추가합니다.

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>코드 테스트

이제 이전에 제출한 요청을 확인해 보겠습니다. 계속하기 전에 이 요청에서 몇 가지 사항을 업데이트해야 합니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.
* Speech `<region>` 리소스가 만들어진 지역(예: `eastus` 또는)으로 `westus`바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.

다음 명령을 실행합니다.

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

이렇게 하면 만든 합성 요청 목록이 반환됩니다. 다음과 같은 출력이 표시됩니다.

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

이제 이전에 제출한 요청을 제거해 보겠습니다. 아래 코드에서 몇 가지 사항을 업데이트해야 합니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.
* Speech `<region>` 리소스가 만들어진 지역(예: `eastus` 또는)으로 `westus`바꿉니다. 이 정보는 [Azure 포털의](https://aka.ms/azureportal)리소스에 대한 **개요** 탭에서 사용할 수 있습니다.
* 이전 `<synthesis_id>` 요청에서 반환된 값으로 바꿉습니다.

> [!NOTE]
> '실행 중'/'대기 중' 상태의 요청은 제거하거나 삭제할 수 없습니다.

다음 명령을 실행합니다.

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

다음과 같은 출력이 표시됩니다.

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>전체 클라이언트 받기

완성된 `voice_synthesis_client.py` 내용은 [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)에서 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [긴 오디오 API에 대해 자세히 알아보기](../../long-audio-api.md)
