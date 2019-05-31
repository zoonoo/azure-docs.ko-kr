---
title: Microsoft Speech Service의 Text to Speech API | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Text to Speech API를 사용하여 다양한 음성과 언어로 실시간 텍스트 음성 변환을 제공할 수 있습니다.
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a046bec5d81d828d88716d31c84e9cbcdcea1a08
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60515435"
---
# <a name="bing-text-to-speech-api"></a>Bing Text to Speech API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>소개

Bing Text to Speech API를 사용하면 애플리케이션이 HTTP 요청을 클라우드 서버로 전송할 수 있으며, 여기서 텍스트가 즉시 음성으로 합성되어 오디오 파일로 반환됩니다. 여러 컨텍스트에서 이 API를 사용하여 다양한 음성과 언어로 실시간 텍스트 음성 변환을 제공할 수 있습니다.

## <a name="VoiceSynReq"></a>음성 합성 요청

### <a name="Subscription"></a>권한 부여 토큰

모든 음성 합성 요청에는 JWT(JSON Web Token) 액세스 토큰이 필요합니다. JWT 액세스 토큰은 음성 요청 헤더를 통해 전달됩니다. 토큰의 만료 시간은 10분입니다. 유효한 JWT 액세스 토큰을 검색하는 데 사용되는 API 키를 구독하고 가져오는 방법에 대한 자세한 내용은 [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요.

API 키는 토큰 서비스에 전달됩니다. 예를 들면 다음과 같습니다.

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

토큰 액세스에 필요한 헤더 정보는 다음과 같습니다.

이름| 형식 | 설명
----|----|----
Ocp-Apim-Subscription-Key | ASCII | 구독 키

토큰 서비스는 JWT 액세스 토큰을 `text/plain`으로 반환합니다. 그런 다음, JWT는 문자열 `Bearer`가 앞에 추가된 인증 헤더로 음성 엔드포인트에 `Base64 access_token`으로 전달됩니다. 예를 들면 다음과 같습니다.

`Authorization: Bearer [Base64 access_token]`

클라이언트가 다음 엔드포인트를 사용하여 텍스트 음성 변환 서비스에 액세스해야 합니다.

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>앞에서 설명한 대로 구독 키를 사용하여 액세스 토큰을 획득할 때까지 이 링크는 `403 Forbidden` 응답 오류를 생성합니다.

### <a name="Http"></a>HTTP 헤더

다음 표에서는 음성 합성 요청에 사용되는 HTTP 헤더를 보여 줍니다.

헤더 |Value |설명
----|----|----
콘텐츠 형식 | application/ssml+xml | 입력 콘텐츠 형식입니다.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | 출력 오디오 형식입니다.
X-Search-AppId | GUID(16진수만, 대시 없음) | 클라이언트 애플리케이션을 고유하게 식별하는 ID입니다. 앱의 스토어 ID일 수 있습니다. 스토어 ID를 사용할 수 없는 경우 사용자가 생성한 애플리케이션 ID일 수 있습니다.
X-Search-ClientID | GUID(16진수만, 대시 없음) | 각 설치에서 애플리케이션 인스턴스를 고유하게 식별하는 ID입니다.
사용자 에이전트 | 애플리케이션 이름 | 애플리케이션 이름은 필수이며 255자 미만이어야 합니다.
권한 부여 | 권한 부여 토큰 |  <a href="#Subscription">인증 토큰</a> 섹션을 참조하세요.

### <a name="InputParam"></a>입력 매개 변수

Bing Text to Speech API에 대한 요청은 HTTP POST 호출을 사용하여 수행됩니다. 헤더는 이전 섹션에서 지정되었습니다. 본문에는 합성할 텍스트를 나타내는 SSML(Speech Synthesis Markup Language) 입력이 포함되어 있습니다. 화자의 언어 및 성별과 같은 음성 측면을 제어하는 데 사용되는 태그에 대한 설명은 [SSML W3C 사양](https://www.w3.org/TR/speech-synthesis/)을 참조하세요.

>[!NOTE]
>지원되는 SSML 입력의 최대 크기는 모든 태그를 포함하여 1,024자입니다.

###  <a name="SampleVoiceOR"></a>예: 음성 출력 요청

음성 출력 요청의 예는 다음과 같습니다.

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>음성 출력 응답

Bing Text to Speech API는 HTTP POST를 사용하여 오디오를 클라이언트에 다시 전송합니다. API 응답에는 은 오디오 스트림과 코덱이 포함되며, 요청된 출력 형식과 일치합니다. 지정된 요청에 대해 반환된 오디오가 15초를 초과하면 안 됩니다.

### <a name="SuccessfulRecResponse"></a>예: 성공적인 합성 응답

다음 코드는 성공적인 음성 합성 요청에 대한 JSON 응답의 예입니다. 코드의 주석과 서식은 이 예의 목적으로만 사용되고 실제 응답에서는 생략됩니다.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>예: 합성 실패

다음 예제 코드는 음성 합성 쿼리 실패에 대한 JSON 응답을 보여 줍니다.

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>오류 응답

오류 | 설명
----|----
HTTP/400 잘못된 요청 | 필수 매개 변수가 누락되었거나, 비어 있거나, Null이거나 필수 또는 선택적 매개 변수에 전달된 값이 잘못되었습니다. “잘못된” 응답이 표시되는 한 가지 이유는 허용 길이보다 긴 문자열 값을 전달하는 경우입니다. 문제가 있는 매개 변수에 대한 간단한 설명이 포함됩니다.
HTTP/401 권한 없음 | 요청에 권한이 없습니다.
HTTP/413 RequestEntityTooLarge  | SSML 입력이 지원되는 크기보다 큽니다.
HTTP/502 BadGateway | 네트워크 관련 문제 또는 서버 쪽 문제가 있습니다.

오류 응답의 예는 다음과 같습니다.

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>SSML을 통해 음성 출력 변경

Microsoft Text-to-Speech API는 W3C [SSML(Speech Synthesis Markup Language) 버전 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)에 정의된 대로 SSML 1.0을 지원합니다. 이 섹션에서는 SSML 태그를 사용하여 말하기 속도, 발음 등 생성된 음성 출력의 특정 특성을 변경하는 예를 보여 줍니다.

1. 중단 추가

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. 말하기 속도 변경

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. 발음

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. 볼륨 변경

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. 피치 변경

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. 운율 곡선 변경

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> 오디오 데이터는 다음 형식의 8k 또는 16k wav 파일이어야 합니다. **CRC 코드**(CRC-32): 유효한 범위가 0x00000000 ~ 0xFFFFFFFF인 4바이트(DWORD)/**오디오 형식 플래그**: 유효한 범위가 0x00000000 ~ 0xFFFFFFFF인 4바이트(DWORD)/**샘플 수**: 유효한 범위가 0x00000000 ~ 0x7FFFFFFF인 4바이트(DWORD)/**이진 본문 크기**: 유효한 범위가 0x00000000 ~ 0x7FFFFFFF인 4바이트(DWORD)/**이진 본문**: n바이트

## <a name="SampleApp"></a>샘플 애플리케이션

구현 세부 정보는 [Visual C# .NET 텍스트 음성 변환 샘플 애플리케이션](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs)을 참조하세요.

## <a name="SupLocales"></a>지원되는 로캘 및 음성 글꼴

다음 표에는 지원되는 몇 가지 로캘 및 관련 음성 글꼴이 나와 있습니다.

로캘 | 성별 | 서비스 이름 매핑
---------|--------|------------
ar-EG* | Female | "Microsoft Server Speech Text to Speech Voice(ar-EG, Hoda)"
ar-SA | Male | “Microsoft Server Speech Text to Speech Voice(ar-SA, Naayf)”
bg-BG | Male | “Microsoft Server Speech Text to Speech Voice(bg-BG, Ivan)”
ca-ES | Female | “Microsoft Server Speech Text to Speech Voice(ca-ES, HerenaRUS)”
cs-CZ | Male | “Microsoft Server Speech Text to Speech Voice(cs-CZ, Jakub)”
da-DK | Female | “Microsoft Server Speech Text to Speech Voice(da-DK, HelleRUS)”
de-AT | Male | “Microsoft Server Speech Text to Speech Voice(de-AT, Michael)”
de-CH | Male | “Microsoft Server Speech Text to Speech Voice(de-CH, Karsten)”
de-DE | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, Hedda) "
de-DE | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, HeddaRUS)"
de-DE | Male | “Microsoft Server Speech Text to Speech Voice(de-DE, Stefan, Apollo)”
el-GR | Male | “Microsoft Server Speech Text to Speech Voice(el-GR, Stefanos)”
en-AU | Female | “Microsoft Server Speech Text to Speech Voice(en-AU, Catherine)”
en-AU | Female | “Microsoft Server Speech Text to Speech Voice(en-AU, HayleyRUS)”
en-CA | Female | "Microsoft Server Speech Text to Speech Voice(en-CA, Linda)"
en-CA | Female | “Microsoft Server Speech Text to Speech Voice(en-CA, HeatherRUS)”
en-GB | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, Susan, Apollo)”
en-GB | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, HazelRUS)”
en-GB | Male | “Microsoft Server Speech Text to Speech Voice(en-GB, George, Apollo)”
en-IE | Male | “Microsoft Server Speech Text to Speech Voice(en-IE, Sean)”
en-IN | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, Heera, Apollo)”
en-IN | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, PriyaRUS)”
en-IN | Male | “Microsoft Server Speech Text to Speech Voice(en-IN, Ravi, Apollo)”
en-US | Female | “Microsoft Server Speech Text to Speech Voice(en-US, ZiraRUS)”
en-US | Female | “Microsoft Server Speech Text to Speech Voice(en-US, JessaRUS)”
en-US | Male | “Microsoft Server Speech Text to Speech Voice(en-US, BenjaminRUS)”
es-ES | Female | “Microsoft Server Speech Text to Speech Voice(es-ES, Laura, Apollo)”
es-ES | Female | “Microsoft Server Speech Text to Speech Voice(es-ES, HelenaRUS)”
es-ES | Male | “Microsoft Server Speech Text to Speech Voice(es-ES, Pablo, Apollo)”
es-MX | Female | “Microsoft Server Speech Text to Speech Voice(es-MX, HildaRUS)”
es-MX | Male | “Microsoft Server Speech Text to Speech Voice(es-MX, Raul, Apollo)”
fi-FI | Female | “Microsoft Server Speech Text to Speech Voice(fi-FI, HeidiRUS)”
fr-CA | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, Caroline)”
fr-CA | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, HarmonieRUS)”
fr-CH | Male | “Microsoft Server Speech Text to Speech Voice(fr-CH, Guillaume)”
fr-FR | Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, Julie, Apollo)”
fr-FR | Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, HortenseRUS)”
fr-FR | Male | “Microsoft Server Speech Text to Speech Voice(fr-FR, Paul, Apollo)”
he-IL| Male| “Microsoft Server Speech Text to Speech Voice(he-IL, Asaf)”
hi-IN | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana, Apollo)”
hi-IN | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana)”
hi-IN | Male | “Microsoft Server Speech Text to Speech Voice(hi-IN, Hemant)”
hr-HR | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)”
hu-HU | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)”
id-ID | Male | "Microsoft Server Speech Text to Speech Voice(id-ID, Andika)"
it-IT | Male | “Microsoft Server Speech Text to Speech Voice(it-IT, Cosimo, Apollo)”
it-IT | Female | "Microsoft Server Speech Text to Speech Voice(it-IT, LuciaRUS)"
ja-JP | Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ayumi, Apollo)”
ja-JP | Male | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ichiro, Apollo)”
ja-JP | Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, HarukaRUS)”
en-US | Female | “Microsoft Server Speech Text to Speech Voice(ko-KR, HeamiRUS)”
ms-MY | Male | “Microsoft Server Speech Text to Speech Voice(ms-MY, Rizwan)”
nb-NO | Female | “Microsoft Server Speech Text to Speech Voice(nb-NO, HuldaRUS)”
nl-NL | Female | “Microsoft Server Speech Text to Speech Voice(nl-NL, HannaRUS)”
pl-PL | Female | “Microsoft Server Speech Text to Speech Voice(pl-PL, PaulinaRUS)”
pt-BR | Female | “Microsoft Server Speech Text to Speech Voice(pt-BR, HeloisaRUS)”
pt-BR | Male | “Microsoft Server Speech Text to Speech Voice(pt-BR, Daniel, Apollo)”
pt-PT | Female | “Microsoft Server Speech Text to Speech Voice(pt-PT, HeliaRUS)”
ro-RO | Male | “Microsoft Server Speech Text to Speech Voice(ro-RO, Andrei)”
ru-RU | Female | “Microsoft Server Speech Text to Speech Voice(ru-RU, Irina, Apollo)”
ru-RU | Male | “Microsoft Server Speech Text to Speech Voice(ru-RU, Pavel, Apollo)”
ru-RU | Female | "Microsoft Server Speech Text to Speech Voice(ru-RU, EkaterinaRUS)"
sk-SK | Male | “Microsoft Server Speech Text to Speech Voice(sk-SK, Filip)”
sl-SI | Male | “Microsoft Server Speech Text to Speech Voice(sl-SI, Lado)”
sv-SE | Female | “Microsoft Server Speech Text to Speech Voice(sv-SE, HedvigRUS)”
ta-IN | Male | “Microsoft Server Speech Text to Speech Voice(ta-IN, Valluvar)”
th-TH | Male | “Microsoft Server Speech Text to Speech Voice(th-TH, Pattara)”
tr-TR | Female | “Microsoft Server Speech Text to Speech Voice(tr-TR, SedaRUS)”
vi-VN | Male | “Microsoft Server Speech Text to Speech Voice(vi-VN, An)”
zh-CN | Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, HuihuiRUS)”
zh-CN | Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, Yaoyao, Apollo)”
zh-CN | Male | “Microsoft Server Speech Text to Speech Voice(zh-CN, Kangkang, Apollo)”
zh-HK | Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, Tracy, Apollo)”
zh-HK | Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, TracyRUS)”
zh-HK | Male | “Microsoft Server Speech Text to Speech Voice(zh-HK, Danny, Apollo)”
zh-TW | Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, Yating, Apollo)”
zh-TW | Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, HanHanRUS)”
zh-TW | Male | “Microsoft Server Speech Text to Speech Voice(zh-TW, Zhiwei, Apollo)”

 *ar-EG는 MSA(Modern Standard Arabic)를 지원합니다.

> [!NOTE]
> Bing Speech API의 기능을 최적화하기 위해 이전 서비스 이름 **Microsoft Server Speech Text to Speech Voice(cs-CZ, Vit)** 및 **Microsoft Server Speech Text to Speech Voice(en-IE, Shaun)** 는 2018년 3월 31일부터 더 이상 사용되지 않습니다. 업데이트된 이름으로 코드를 업데이트하세요.

## <a name="TrouNSupport"></a>문제 해결 및 지원

모든 질문과 문제는 [Bing Speech Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN 포럼에 게시하세요. 다음과 같은 전체 세부 정보를 포함합니다.

* 전체 요청 문자열의 예입니다.
* 해당하는 경우, 로그 ID를 포함하는 실패한 요청의 전체 출력입니다.
* 오류가 발생하는 요청의 백분율입니다.
