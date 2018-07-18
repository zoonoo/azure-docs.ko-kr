---
title: 인사이트를 위한 Bing 이미지 업로드 | Microsoft Docs
description: Bing Image Search API를 사용하여 이미지를 업로드하고 이미지 인사이트를 가져오는 콘솔 응용 프로그램입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372839"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>자습서: 인사이트를 위한 Bing 이미지 업로드

Bing Image Search API는 이미지를 업로드하고 해당 이미지와 관련된 정보를 검색하는 `POST` 옵션을 제공합니다. 이 C# 콘솔 응용 프로그램은 이미지에 대한 세부 정보를 가져오기 위해 Image Search 엔드포인트를 사용하여 이미지를 업로드합니다.
그 결과는 간략하게 말해서 다음과 같은 JSON 개체입니다.

![[JSON 결과]](media/cognitive-services-bing-images-api/jsonResult.jpg)

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * `POST` 요청에서 Image Search `/details` 엔드포인트 사용
> * 요청에 대한 헤더 지정
> * URL 매개 변수를 사용하여 결과 지정
> * 이미지 데이터를 업로드하고 `POST` 요청 보내기
> * JSON 결과를 콘솔에 인쇄

## <a name="app-components"></a>앱 구성 요소

자습서 응용 프로그램에는 다음과 같은 세 부분이 포함되어 있습니다.

> [!div class="checklist"]
> * Bing Image Search 엔드포인트와 필요한 헤더를 지정하는 엔드포인트 구성
> * 엔드포인트에 대한 `POST` 요청에 이미지 파일 업로드
> * `POST` 요청에서 반환된 세부 정보인 JSON 결과를 구문 분석

## <a name="scenario-overview"></a>시나리오 개요
[3개의 Image Search 엔드포인트](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint)가 있습니다. `/details` 엔드포인트는 요청 본문에 이미지 데이터와 함께 `POST` 요청을 사용할 수 있습니다.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
`/details?` 다음에 나오는 `modules` URL 매개 변수는 결과에 포함될 세부 정보의 종류를 지정합니다.
* `modules=All`
* `modules=RecognizedEntities`(이미지에 표시되는 사람 또는 위치)

`POST` 요청에서 다음 목록을 포함하는 JSON 텍스트를 가져오는 `modules=All`을 지정합니다.
* `bestRepresentativeQuery` - 업로드된 이미지와 비슷한 이미지를 반환하는 Bing 쿼리
* 이미지의 경계 상자 또는 핫 스폿 같은 `detectedObjects`
* `image` 메타데이터
* `imageInsightsToken` - `RecognizedEntities`(이미지에 표시되는 사람 또는 위치)를 가져오는 후속 `GET` 요청의 토큰 
* `imageTags`
* `pagesIncluding` - 이미지를 포함하는 웹 페이지
* `relatedSearches`
* `visuallySimilarImages`

`POST` 요청에서 후속 `GET` 요청에 사용되는 `imageInsightsToken`만 가져오는 `modules=RecognizedEntities`를 지정합니다. 이미지에 표시되는 사람 또는 위치를 식별합니다.

## <a name="webclient-and-headers-for-the-post-request"></a>POST 요청에 대한 WebClient 및 헤더
`WebClient` 개체를 만들고, 헤더를 설정합니다. Bing Search API에 대한 모든 요청에는 `Ocp-Apim-Subscription-Key`가 필요합니다. 이미지를 업로드하려는 `POST` 요청 역시 `ContentType: multipart/form-data`를 지정해야 합니다.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>이미지를 업로드하고 결과 가져오기

`WebClient` 클래스에는 `POST` 요청의 데이터 형식을 지정하는 `UpLoadFile` 메서드가 포함되어 있습니다. 이 메서드는 `RequestStream` 형식을 지정하고 `HttpWebRequest`를 호출하여 복잡성을 줄입니다.
`/details` 엔드포인트 및 업로드할 이미지 파일을 사용하여 `WebClient.UpLoadFile`을 호출합니다. 응답은 JSON으로 쉽게 변환되는 이진 데이터입니다. 

JSON 텍스트를 사용하여 `SearchResult` 구조의 인스턴스를 초기화하세요(컨텍스트에 대한 [응용 프로그램 소스 코드](tutorial-image-post-source.md) 참조).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>결과 인쇄
나머지 코드는 JSON 결과를 구문 분석하여 콘솔에 출력합니다.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>ImageInsightsToken를 사용한 GET 요청
`POST` 결과와 함께 반환된 `ImageInsightsToken`을 사용하려면 다음과 같은 `GET` 요청을 만듭니다.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
이미지에 식별 가능한 사람 또는 위치가 있는 경우 이 요청은 그 사람 또는 위치에 대한 정보를 반환합니다.
[빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-image-search)에는 다양한 코드 예제가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

