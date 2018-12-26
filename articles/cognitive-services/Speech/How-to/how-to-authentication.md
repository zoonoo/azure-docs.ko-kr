---
title: Bing Speech 인증 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech API를 사용하기 위한 인증 요청
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: e3bfdcfaaf752321997636811b282ea920e6144a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344203"
---
# <a name="authenticate-to-the-speech-api"></a>Speech API 인증

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech에서 인증을 지원하기 위해 사용하는 항목은 다음과 같습니다.

- 구독 키
- 권한 부여 토큰

## <a name="use-a-subscription-key"></a>구독 키 사용

Speech Service를 사용하려면 먼저 Cognitive Services(이전의 Project Oxford)의 일부인 Speech API를 구독해야 합니다. [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지에서 평가판 구독 키를 가져올 수 있습니다. Speech API를 선택한 후에 **API 키 가져오기**를 선택하여 키를 가져옵니다. 기본 및 보조 키를 반환합니다. 두 키는 모두 동일한 할당량에 연결되므로 두 키 중 하나를 사용할 수 있습니다.

장기간 사용하거나 할당량을 늘리려면 [Azure 계정](https://azure.microsoft.com/free/)에 등록합니다.

Speech REST API를 사용하려면 요청 헤더의 `Ocp-Apim-Subscription-Key` 필드에 구독 키를 전달해야 합니다.

이름| 형식| 설명
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

요청 헤더의 예제는 다음과 같습니다.

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> 응용 프로그램에서 [클라이언트 라이브러리](../GetStarted/GetStartedClientLibraries.md)를 사용하는 경우 다음 섹션에서 설명한 대로 구독 키가 있는 권한 부여 토큰을 가져올 수 있는지 확인합니다. 클라이언트 라이브러리는 구독 키를 사용하여 권한 부여 토큰을 가져온 다음, 이 토큰을 인증에 사용합니다.

## <a name="use-an-authorization-token"></a>권한 부여 토큰 사용

또는 인증에서 권한 부여 토큰을 인증/권한 부여의 증명으로 사용할 수 있습니다. 이 토큰을 가져오려면 먼저 [앞 섹션](#use-a-subscription-key)에서 설명한 대로 Speech API에서 구독 키를 획득해야 합니다.

### <a name="get-an-authorization-token"></a>권한 부여 토큰 가져오기

유효한 구독 키를 받은 후에 Cognitive Services의 토큰 서비스에 POST 요청을 보냅니다. 응답에서 권한 부여 토큰을 JWT(JSON Web Token)로 받습니다.

> [!NOTE]
> 토큰의 만료 기간은 10분입니다. 토큰을 갱신하려면 다음 섹션을 참조하세요.

토큰 서비스 URI의 위치는 다음과 같습니다.

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

다음 코드 샘플에서는 액세스 토큰을 가져오는 방법을 보여 줍니다. `YOUR_SUBSCRIPTION_KEY`를 사용자 고유의 구독 키로 바꾸세요.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

이 예제에서는 bash가 있는 Linux에서 curl을 사용합니다. 플랫폼에서 사용할 수 없는 경우 curl을 설치해야 할 수도 있습니다. 또한 이 예제는 Windows의 Cygwin, Git Bash, zsh 및 다른 셸에서도 작동합니다.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

POST 요청 샘플은 다음과 같습니다.

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

토큰 서비스에서 권한 부여 토큰을 가져올 수 없는 경우 구독 키가 여전히 유효한지 확인합니다. 평가판 키를 사용하는 경우 [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지로 이동하고, "로그인"을 클릭하여 평가판 키를 적용하는 데 사용한 계정으로 로그인한 다음, 구독 키가 만료되었거나 할당량을 초과하는지 여부를 확인합니다.

### <a name="use-an-authorization-token-in-a-request"></a>요청에 권한 부여 토큰 사용

Speech API를 호출할 때마다 `Authorization` 헤더에 권한 부여 토큰을 전달해야 합니다. `Authorization` 헤더에 JWT 액세스 토큰이 있어야 합니다.

다음 예제에서는 Speech REST API를 호출할 때 권한 부여 토큰을 사용하는 방법을 보여 줍니다.

> [!NOTE]
> `YOUR_AUDIO_FILE`을 미리 녹음된 오디오 파일의 경로로 바꾸고, `YOUR_ACCESS_TOKEN`을 이전의 [권한 부여 토큰 가져오기](#get-an-authorization-token) 단계에서 획득한 권한 부여 토큰으로 바꾸세요.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

### <a name="renew-an-authorization-token"></a>권한 부여 토큰 갱신

권한 부여 토큰은 특정 기간(현재 10분) 후에 만료됩니다. 만료되기 전에 권한 부여 토큰을 갱신해야 합니다.

다음 코드는 C#에서 권한 부여 토큰을 갱신하는 방법을 구현하는 예제입니다.

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
