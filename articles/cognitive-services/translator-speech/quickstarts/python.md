---
title: '빠른 시작: Translator Speech API Python'
titlesuffix: Azure Cognitive Services
description: Translator Speech API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: f6386fbb54ab78def289f5eee9f957bb7a11ff65
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341223"
---
# <a name="quickstart-translator-speech-api-with-python"></a>빠른 시작: Python을 사용한 Translator Speech API
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

이 문서에서는 Translator Speech API를 사용하여 .wav 파일에서 발화된 단어를 변환하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Python 3.x](https://www.python.org/downloads/)가 필요합니다.

Python용 [websocket-client 패키지](https://pypi.python.org/pypi/websocket-client)를 설치해야 합니다.

아래 코드에서 컴파일하는 실행 파일과 동일한 폴더에 "speak.wav"라는 .wav 파일이 필요합니다. 이 .wav 파일은 표준 PCM, 16비트, 16kHz, 모노 형식이어야 합니다. 

**Microsoft Translator Speech API**와 함께 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. [Azure 대시보드](https://portal.azure.com/#create/Microsoft.CognitiveServices)에서 유료 구독 키가 필요합니다.

## <a name="translate-speech"></a>음성 변환

다음 코드에서는 음성을 한 언어에서 다른 언어로 변환합니다.

1. 좋아하는 IDE에 새 Python 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `key` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
```

**음성 응답 변환**

성공적인 결과는 "speak2.wav"라는 파일을 만드는 것입니다. 이 파일에는 "speak.wav"에서 발화된 단어에 대한 변환이 포함됩니다.

[맨 위로 이동](#HOLTop)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Translator Speech 자습서](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>참고 항목 

[Translator Speech 개요](../overview.md)
[API 참조](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
