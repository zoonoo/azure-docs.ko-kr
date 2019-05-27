---
title: '빠른 시작: Bing Visual Search REST API 및 Go를 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: 이미지를 Bing Visual Search API에 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: rosh
ms.openlocfilehash: dcfb2d7dc49dae03c733a3016d37425e33fb0535
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796474"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-go"></a>빠른 시작: Bing Visual Search REST API 및 Go를 사용하여 이미지 인사이트 가져오기

이 빠른 시작에서는 Go 프로그래밍 언어를 사용하여 Bing Visual Search API를 호출하고 결과를 표시합니다. POST 요청은 API 엔드포인트에 이미지를 업로드합니다. 결과는 업로드된 이미지와 비슷한 이미지의 URL과 설명 정보를 포함합니다.

## <a name="prerequisites"></a>필수 조건

* [Go 이진 파일](https://golang.org/dl/)을 설치합니다.
* go-spew 깔끔한 프린터는 결과를 표시하는 데 사용됩니다. go-spew는 `$ go get -u https://github.com/davecgh/go-spew` 명령으로 설치할 수 있습니다.

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="project-and-libraries"></a>프로젝트 및 라이브러리

IDE 또는 편집기에서 Go 프로젝트를 만듭니다. 그런 다음, 요청하기 위해 `net/http`, 응답을 읽기 위해 `ioutil`, JSON 텍스트 결과를 처리하기 위해 `encoding/json`을 가져옵니다. `go-spew` 라이브러리는 JSON 결과 구문 분석에 사용됩니다.

```
package main

import (
    "bytes"
    "io"
    "fmt"
    "io/ioutil"
    "mime/multipart"
    "net/http"
    "os"
    "path/filepath"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="struct-to-format-results"></a>결과 형식을 지정하는 구조체

`BingAnswer` 구조체는 복잡한 다단계로 이루어진 JSON 응답에 반환된 데이터의 형식을 지정합니다. 아래 구현에서는 몇 가지 주요 정보를 설명합니다.

```
type BingAnswer struct {
    Type         string `json:"_type"`
    Instrumentation struct {
        Type string   `json:"_type"`
        PingUrlBase   string  `json:"_pingUrlBase"`
        PageLoadPingUrl  string `json:"_pageLoadPingUrl"`
    } `json:"instrumentation"`
    Tags []struct  {
        DisplayName       string    `json:"displayName"`
        Actions                 []struct {
            Type      string `json:"_type"`
            ActionType    string    `json:"actionType"`
            Data             struct {
                Value     []struct {
                    WebSearchUrl          string `json:"webSearchUrl"`
                    Name        string  `json:"name"`
                }`json:"value"`
                ImageInsightsToken string `json:"imageInsightsToken"`
                InsightsMetadata struct {
                    PagesIncludingCount int `json:"pagesIncludingCount"`
                    AvailableSizesCount  int  `json:"availableSizesCount"`
                }
                ImageId string  `json:"imageId"`
                AccentColor  string `json:"accentColor"`
            } `json:"data"`
        } `json:"actions"`
    } `json:"tags"`
    Image struct {
        WebSearchUrl   string  `json:"webSearchUrl"`
        WebSearchUrlPingSuffix  string `json:"webSearchUrlPingSuffix"`
        Name   string   `json:"name"`
        IsFamilyFriendly   bool  `json:"isFamilyFriendly"`
        ContentSize   string   `json:"contentSize"`
        EncodingFormat    string   `json:"encodingFormat"`
        HostPageDisplayUrl   string    `json:"hostPageDisplayUrl"`
        Width     int   `json:"width"`
        Height    int    `json:"height"`
        Thumbnail    struct  {
            Width   int   `json:"width"`
            Height  int   `json:"height"`
        }
        InsightsMetadata  struct {
            PagesIncludingCount   int   `json:"pagesIncludingCount"`
            AvailableSizesCount    int    `json:"availableSizesCount"`
        }
        AccentColor   string     `json:"accentColor"`
        VisualWords   string   `json:"visualWords"`
    } `json:"image"`
}

```

## <a name="main-function-and-variables"></a>Main 함수 및 변수  

다음 코드는 main 함수를 선언하고 필요한 변수를 할당합니다. 엔드포인트가 올바른지 확인하고 `token` 값을 Azure 계정의 유효한 구독 키로 바꿉니다. `batchNumber`는 POST 데이터의 선행 및 후행 경계에 필요한 GUID입니다. `fileName` 변수는 POST의 이미지 파일을 식별합니다. 다음 섹션에서는 코드에 대해 자세히 설명합니다.

```
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.se
    endpoint := "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch"
    token := "YOUR-ACCESS-KEY"
    client := &http.Client{}
    batchNumber := "d7ecc447-912f-413e-961d-a83021f1775f"
    fileName := "ElectricBike.JPG"

    body, contentType := createRequestBody(fileName, batchNumber)
    
    req, err := http.NewRequest("POST", endpoint, body)
    req.Header.Add("Content-Type", contentType)
    req.Header.Set("Ocp-Apim-Subscription-Key", token)
    
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    
       defer resp.Body.Close()
    
    resbody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    //fmt.Print(string(resbody))
    
    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(resbody, &ans)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Print("Output of BingAnswer: \r\n\r\n")
    
    // Iterate over search results and print the result name and URL.
    for _, result := range ans.Tags {
        spew.Dump(result)
    }
}

```

## <a name="boundaries-of-post-body"></a>POST 본문의 경계

Visual Search 엔드포인트에 대한 POST 요청에서는 선행 및 후행 경계로 POST 데이터를 둘러싸야 합니다. 선행 경계에는 일괄 처리 번호, 콘텐츠 형식 식별자 `Content-Disposition: form-data; name="image"; filename=`과 POST에 대한 이미지 파일 이름이 포함됩니다. 후행 경계는 일괄 처리 번호만으로 이루어집니다. 이러한 함수는 `main` 블록에 포함되지 않습니다.

```
func BuildFormDataStart(batNum string, fileName string) string{

    startBoundary := "--batch_" + batNum + "\r\n"
    requestBoundary := startBoundary + "\r\n" + "Content-Disposition: form-data; name=\"image\"; filename=" + fileName + "\r\n\r\n"
    return requestBoundary
}

func BuildFormDataEnd(batNum string) string{

    return "\r\n" + "\r\n" + "--batch_" + batNum + "\r\n"

}

```
## <a name="add-image-bytes-to-post-body"></a>POST 본문에 이미지 바이트 추가

이 코드 세그먼트에서는 이미지 데이터를 포함하는 POST 요청을 만듭니다.

```
func createRequestBody(fileName string, batchNumber string) (*bytes.Buffer, string) {
    file, err := os.Open(fileName)
    if err != nil {
        panic(err)
    }
    defer file.Close()

    body := &bytes.Buffer{}
    writer := multipart.NewWriter(body)
    
    writer.SetBoundary(BuildFormDataStart(batchNumber, fileName))
    
    part, err := writer.CreateFormFile("image", filepath.Base(file.Name()))
    if err != nil {
        panic(err)
    }
    io.Copy(part, file)
    writer.WriteField("lastpart", BuildFormDataEnd(batchNumber))    
    writer.Close()
    return body, writer.FormDataContentType()
}

```

## <a name="send-the-request"></a>요청 보내기

다음 코드는 요청을 보내고 결과를 읽습니다.

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    
       defer resp.Body.Close()
    
    resbody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

```

## <a name="handle-the-response"></a>응답 처리

`Unmarshall` 함수는 Visual Search API에서 반환한 JSON 텍스트에서 정보를 추출합니다. `go-spew` 깔끔한 프린터에 결과가 표시됩니다.

```
    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(resbody, &ans)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Print("Output of BingAnswer: \r\n\r\n")
    
    // Iterate over search results and print the result name and URL.
    for _, result := range ans.Tags {
        spew.Dump(result)
    }

```
> [!NOTE]
> Francesco Giordano가 이 예제의 코드를 제공했습니다.

## <a name="results"></a>결과

결과는 POST 본문에 포함된 이미지와 유사한 이미지를 식별합니다. 유용한 필드는 `WebSearchUrl` 및 `Name`입니다.

```
    Value: ([]struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) (len=66 cap=94) {
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B9E6621161769D578A9E4DD9FD742128DE65225A&simid=608046863828453626",
      Name: (string) (len=87) "26\"Foldable Electric Mountain Bike Bicycle Ebike W/Lithium Battery 250W 36V MY8L | eBay"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=5554757348A9E88E9EEAB74217E228689E716B61&simid=607988237543409883",
      Name: (string) (len=96) "Best 25+ Electric mountain bike ideas on Pinterest | Mountain bicycle, Mtb mountain bike and ..."
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B56F626A4803D829FE326842E2B97CE5B87F17F2&simid=607991699288949513",
      Name: (string) (len=100) "Electric Fat Bike Mountain Bicycle Snow Bike Cruiser Ebike Motor Lithium Battery Dual Oil Brakes ..."
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=CCAE400EE510DCA6F5740ABAF08A5310B4EF0698&simid=608003854048890458",
      Name: (string) (len=81) "Best Mountain Bikes Under 1500 (Outstanding Performance) | Mountain Bicycle World"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=1244730E2E3F2D5DFBED9A9CFE1DBE27B09F08BC&simid=608005181199745622",
      Name: (string) (len=98) "ANCHEER Folding Electric Mountain Bike with 26\" Super Lightweight Magnesium (36V 250W) - GearScoot"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=38AAD876E57BB0D502FBDD5B1CB29EB7EB8DD9E2&simid=608041654062220328",
      Name: (string) (len=97) "29 best CB Bikes Gadgets & Accessories images on Pinterest | Bicycles, Bicycling and Bike gadgets"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=6892C8FD22D0E42911C99AFD8FE1FD37BD82E02C&simid=608052803759703185",
      Name: (string) (len=98) "ANCHEER Folding Electric Mountain Bike with 26\" Super Lightweight Magnesium (36V 250W) - GearScoot"
     },

```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Visual Search API란?](../overview.md)
> [Go에서 Bing Web Search 빠른 시작](../../Bing-Web-Search/quickstarts/go.md)
