---
title: '빠른 시작: Go용 Text Analytics 클라이언트 라이브러리 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 1ba2ec6b5c0c59be7b7264f7558fbb393adcc2d8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142799"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>빠른 시작: Go용 Text Analytics 클라이언트 라이브러리

Go용 Text Analytics 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. 

Go용 Text Analytics 클라이언트 라이브러리를 사용하여 수행하는 작업은 다음과 같습니다.

* 정서 분석
* 언어 검색
* 엔터티 인식
* 핵심 문구 추출

[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [패키지(Github)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 최신 버전의 [Go](https://golang.org/dl/)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기 

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Text Analytics용 리소스를 만듭니다. 또한 다음을 수행할 수 있습니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져옵니다. 키를 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.
* [Azure Portal](https://portal.azure.com)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키를 가져오면 `TEXT_ANALYTICS_SUBSCRIPTION_KEY`라는 키에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-go-project"></a>새 Go 프로젝트 만들기

콘솔 창(cmd, PowerShell, 터미널, Bash)에서 Go 프로젝트에 대한 새 작업 영역을 만들고 해당 작업 영역으로 이동합니다. 작업 영역에는 다음 세 개의 폴더가 있습니다. 

* **src** - 이 디렉터리에는 소스 코드와 패키지가 포함되어 있습니다. 여기에는 `go get` 명령으로 설치된 패키지가 모두 있습니다.
* **pkg** - 이 디렉터리에는 컴파일된 Go 패키지 개체가 포함되어 있습니다. 이러한 파일의 확장명은 모두 `.a`입니다.
* **bin** - 이 디렉터리에는 `go install`을 실행할 때 만들어지는 이진 실행 파일이 포함되어 있습니다.

> [!TIP]
> [Go 작업 영역](https://golang.org/doc/code.html#Workspaces)의 구조에 대해 자세히 알아보세요. 이 가이드에는 `$GOPATH` 및 `$GOROOT` 설정에 대한 정보가 있습니다.

`my-app`이라는 작업 영역과 `src`, `pkg` 및 `bin`에 필요한 하위 디렉터리를 만듭니다.

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Go용 Text Analytics 클라이언트 라이브러리 설치

Go용 클라이언트 라이브러리를 설치합니다. 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

dep를 사용하는 경우에는 리포지토리 내에서 다음을 실행합니다.

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Go 애플리케이션 만들기

다음으로, `src/quickstart.go`라는 파일을 만듭니다.

```bash
$ cd src
$ touch quickstart.go
```

즐겨찾는 IDE 또는 텍스트 편집기에서 `quickstart.go`를 엽니다. 그런 다음, 패키지 이름을 추가하고 다음 라이브러리를 가져옵니다.

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"    
)
```

이 빠른 시작의 매개 변수 및 속성은 대부분 문자열과 부울 포인터가 필요하므로 다음 함수를 프로젝트에 추가합니다.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

애플리케이션의 `main` 함수에서 리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다. 환경 변수 TEXT_ANALYTICS_SUBSCRIPTION_KEY 및 TEXT_ANALYTICS_ENDPOINT에서 이러한 값을 가져옵니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if "" == os.Getenv(subscriptionKeyVar) {
    log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
}
var subscriptionKey string = os.Getenv(subscriptionKeyVar)
var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
if "" == os.Getenv(endpointVar) {
    log.Fatal("Please set/export the environment variable " + endpointVar + ".")
}
var endpoint string = os.Getenv(endpointVar)
```

## <a name="object-model"></a>개체 모델 

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 개체입니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다. 

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Text Analytics 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [엔터티 인식](#entity-recognition)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증

프로젝트의 main 함수에서 새 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 개체를 호출합니다. 키를 [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 함수에 전달하면 해당 키가 클라이언트의 `authorizer` 속성에 전달됩니다.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>정서 분석

이전에 만든 클라이언트를 사용하는 `SentimentAnalysis()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 개체 목록을 만듭니다. 각 개체에는 `id`, `Language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 클라이언트의 [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

```golang
result, err := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s\n", *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

프로젝트의 main 함수에서 `SentimentAnalysis()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>언어 검색

이전에 만든 클라이언트를 사용하는 `LanguageDetection()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) 개체 목록을 만듭니다. 각 개체에는 `id` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하며, `id`는 임의의 값이 될 수 있습니다. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 클라이언트의 [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 검색된 언어를 인쇄합니다.

```golang
result, err := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

프로젝트의 main 함수에서 `LanguageDetection()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>엔터티 인식

이전에 만든 클라이언트를 사용하는 `ExtractEntities()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

```golang
func ExtractEntities(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 클라이언트의 [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 엔터티 점수를 인쇄합니다.

```golang
    result, err := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

프로젝트의 main 함수에서 `ExtractEntities()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

이전에 만든 클라이언트를 사용하는 `ExtractKeyPhrases()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 클라이언트의 [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 핵심 구를 인쇄합니다.

```golang
    result, err := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

프로젝트의 main 함수에서 `ExtractKeyPhrases()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 감지](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices)에서 확인할 수 있습니다.
