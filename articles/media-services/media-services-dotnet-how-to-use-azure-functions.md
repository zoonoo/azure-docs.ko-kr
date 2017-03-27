---
title: "Media Services에서 Azure Functions 개발"
description: "이 항목에서는 Azure Portal을 사용하여 Media Services에서 Azure Functions를 개발하기 시작하는 방법을 보여 줍니다."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Media Services에서 Azure Functions 개발
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

이 항목에서는 Azure Portal을 사용하여 Media Services에서 Azure Functions를 개발하기 시작하는 방법을 설명합니다. 

[여기](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)에서 **Azure에 배포** 단추를 눌러 기존 Media Services Azure Functions를 배포할 수도 있습니다. 이 리포지토리는 Blob Storage에서 직접 콘텐츠를 수집하고 Blob Storage에 콘텐츠를 인코딩 및 작성하는 데 관련된 워크플로를 표시하는 데 Azure Media Services를 사용하는 Azure Functions 예제를 포함합니다. 또한 WebHooks 및 Azure 큐를 통해 작업 알림을 모니터링하는 방법의 예도 포함되어 있습니다.

[이](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration) 리포지토리에 있는 예제에 따라 Functions를 개발할 수 있습니다. 이 항목에서는 Media Services를 사용하는 Azure Functions를 만들기 시작하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건

첫 번째 함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [무료 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.

AMS(Azure Media Services) 계정에서 작업을 수행하거나 Media Services에서 보낸 이벤트를 수신 대기하는 Azure Functions를 만들려는 경우 [여기](media-services-portal-create-account.md)에 설명한 대로 AMS 계정을 만들어야 합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

[여기](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app)에 설명한 대로 함수 앱을 만듭니다.

## <a name="create-a-function"></a>함수 만들기

함수 앱을 배포하면 **App Services** Azure Functions에서 찾을 수 있습니다. 

1. 함수 앱을 선택하고 **새 함수**를 클릭합니다.
3. **C#** 언어 및 **Webhook + API** 시나리오를 선택합니다.
3. **GenericWebHook-CSharp**(Webhook 요청을 받을 때마다 실행됨) 또는 **HttpTrigger-CSharp**(HTTP 요청을 받을 때마다 실행됨)를 선택하고 함수 이름을 지정합니다.
4. **만들기**를 클릭합니다. 

## <a name="get-function-url"></a>함수 URL 가져오기

HTTP 테스트 도구 또는 다른 브라우저 창에서 함수 실행을 트리거하려면 함수 URL 값이 필요합니다. 

![설정](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>파일

Azure Function은 이 섹션에 설명된 코드 파일 및 기타 파일과 연결됩니다. 기본적으로 함수는 **function.json** 및 **run.csx** 파일과 연결됩니다. **project.json** 파일을 추가해야 합니다. 이 섹션의 나머지 부분에서는 이러한 파일의 정의를 보여 줍니다.

![업로드](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.json 파일은 함수 바인딩 및 기타 구성 설정을 정의합니다. 런타임은 이 파일을 사용하여 모니터링할 이벤트와 함수 실행에서 데이터를 전달하고 반환하는 방법을 결정합니다. 

**function.json** 파일의 예제는 다음과 같습니다.

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

project.json 파일은 종속성을 포함합니다. AMS 라이브러리가 포함된 **function.json** 파일의 예는 다음과 같습니다.

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

함수에 대한 C# 코드입니다. Webhook 함수의 예제는 [이](media-services-dotnet-check-job-progress-with-webhooks.md) 항목을 참조하세요. 

함수를 정의하는 작업을 완료하면 **실행**을 클릭합니다.
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>함수 앱 구성 설정

Media Services 함수를 개발하는 경우 **앱 설정** 섹션에 대한 함수 전체에서 사용할 매개 변수를 쉽게 추가할 수 있습니다. 

예:

![설정](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>다음 단계

이제 미디어 서비스 응용 프로그램 개발을 시작할 준비가 되었습니다. 자세한 내용은 [Azure WebHooks를 사용하여 .NET으로 Media Services 작업 알림 모니터링](media-services-dotnet-check-job-progress-with-webhooks.md)을 참조하세요.   

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


