---
title: 오디오 스트림을 청크 분할하여 전송하는 방법 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 청크 분할 전송을 사용하여 오디오 스트림을 Bing Speech 서비스로 보내는 방법
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 82c07332af7d4a5df4a6af15ef65abcd8a00f603
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216883"
---
# <a name="chunked-transfer-encoding"></a>청크 분할 전송 인코딩

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

음성 - 텍스트 변환을 전사하기 위해 Microsoft 음성 인식 API를 사용하면 오디오를 하나의 청크 전체로 보내거나 작은 청크로 분할하여 보낼 수 있습니다. 효율적으로 오디오를 스트림하고 전사 대기 시간을 줄이려면 [청크 분할 전송 인코딩](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)을 사용하여 오디오를 서비스로 스트림하는 것이 좋습니다. 다른 구현은 사용자가 인식하는 대기 시간을 늘릴 수 있습니다. 자세한 내용은 [오디오 스트림](../concepts.md#audio-streams) 페이지를 참조하세요.

> [!NOTE]
> 한 요청에서 10초를 초과하여 오디오를 업로드할 수 없으며, 총 요청 기간은 14초를 초과할 수 없습니다.
> [!NOTE]
> [REST API](../GetStarted/GetStartedREST.md)를 사용하여 음성 서비스를 호출하는 경우에만 청크 분할 전송 인코딩을 지정해야 합니다. [클라이언트 라이브러리](../GetStarted/GetStartedClientLibraries.md)를 사용하는 애플리케이션에서 청크 분할 전송 인코딩을 구성할 필요가 없습니다.

다음 코드에서는 청크 분할 전송 인코딩을 설정하고, 청크 분할되는 오디오 파일을 1,024바이트 청크로 보내는 방법을 보여 줍니다.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

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
