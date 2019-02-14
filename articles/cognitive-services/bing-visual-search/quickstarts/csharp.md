---
title: '빠른 시작: Bing Visual Search REST API 및 C#을 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: 이미지를 Bing Visual Search API에 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: d4a1ff43f6165e2620bbc3a5fbbf381ca114062e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884259"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>빠른 시작: Bing Visual Search REST API 및 C#을 사용하여 이미지 인사이트 가져오기

이 빠른 시작을 사용하여 Bing Visual Search API를 처음 호출하고 검색 결과를 확인합니다. 이 간단한 C# 애플리케이션은 API에 이미지를 업로드하고, 이미지에 대한 반환된 정보를 표시합니다.

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
* NuGet 패키지로 사용 가능한 [Json.NET](https://www.newtonsoft.com/json) 프레임워크.
* Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](http://www.mono-project.com/)를 사용하여 실행할 수 있습니다.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. Visual Studio에서 `BingSearchApisQuickStart`라는 새 콘솔 솔루션을 만듭니다. 그런 다음, 주 코드 파일에 다음 네임스페이스를 추가합니다.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. 구독 키, 엔드포인트에 대한 변수 및 업로드할 이미지에 대한 경로를 추가합니다.

    ```csharp
        const string accessKey = "<yoursubscriptionkeygoeshere>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<pathtoimagegoeshere>";
    ```


1. `GetImageFileName()`이라는 메서드를 만들어 이미지에 대한 경로 가져오기
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

2. 메서드를 만들어 이미지의 이진 문자를 가져옵니다.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>양식 데이터 작성

로컬 이미지를 업로드하는 경우 API에 전송되는 양식 데이터는 올바르게 포맷되어야 합니다. Content-Disposition 헤더를 포함해야 하며, 해당 `name` 매개 변수를 "image"로 설정해야 하고, `filename` 매개 변수를 임의의 문자열로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진을 포함합니다. 업로드할 수는 최대 이미지 크기는 1MB입니다.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. 양식 데이터를 서식 지정하려면 데이터에 대한 시작, 종료 및 새 줄 문자를 결정하는 POST 양식 데이터를 올바르게 서식 지정하기 위한 경계 문자열을 추가합니다.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. 다음 매개 변수는 양식 데이터에 매개 변수를 추가하는 데 사용됩니다. 

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. 경계 문자열 및 이미지 경로를 사용하는 필요한 양식 데이터의 시작 부분을 만드는 `BuildFormDataStart()`라는 함수를 만듭니다.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. 경계 문자열을 사용하는 필요한 양식 데이터의 끝 부분을 만드는 `BuildFormDataEnd()`라는 함수를 만듭니다.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Bing Visual Search API 호출

1. Bing Visual Search 엔드포인트를 호출하는 함수를 만들고 json 응답을 반환합니다. 함수는 양식 데이터에 대한 시작 및 끝 부분, 이미지 데이터를 포함하는 바이트 배열 및 contentType 값을 사용해야 합니다.

2. `WebRequest`를 사용하여 URI, contentType 값 및 헤더를 저장합니다.  

3. `request.GetRequestStream()`을 사용하여 양식 및 이미지 데이터를 작성합니다. 그런 다음, 응답을 가져옵니다. 이 함수는 아래 코드와 같습니다.
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>기본 메서드 만들기

1. 애플리케이션의 기본 메서드에서 이미지에 대한 파일 이름 및 이미지 이진을 가져옵니다. 

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. 경계를 서식 지정하여 POST 본문을 설정합니다. 그런 다음, `startFormData()` 및 `endFormData`를 호출하여 양식 데이터를 만듭니다. 

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. `CONTENT_TYPE_HEADER_PARAMS` 및 양식 데이터 경계를 서식 지정하여 ContentType 값을 만듭니다.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. `BingImageSearch()`를 호출하여 API 응답을 가져옵니다. 그런 다음, 응답을 인쇄합니다.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HttpClient 사용

HttpClient를 사용하는 경우 MultipartFormDataContent를 사용하여 양식 데이터를 작성할 수 있습니다. 코드의 다음 섹션을 사용하여 이전 예제에 사용된 같은 이름의 메서드를 바꾸기만 하면 됩니다.

Main 메서드를 다음 코드로 바꿉니다.

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

BingImageSearch 메서드를 다음 코드로 바꿉니다.

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](../tutorial-bing-visual-search-single-page-app.md)
