---
title: '빠른 시작: Go SDK를 사용하여 Text Analytics 서비스 호출'
titleSuffix: Azure Cognitive Services
description: Microsoft Cognitive Services에서 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 받으세요.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/23/2019
ms.author: aahi
ms.openlocfilehash: 44def29292bc882fdaa08ff76667742756f178b8
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299463"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-go-sdk"></a>빠른 시작: Go SDK를 사용하여 Text Analytics 서비스 호출 
<a name="HOLTop"></a>

이 빠른 시작을 사용하여 Go용 Text Analytics SDK를 통해 언어 분석을 시작하세요. 이 문서에서는 언어 검색, 감정 분석, 핵심 구 추출 및 연결된 엔터티 식별을 수행하는 방법을 보여줍니다. REST API는 대부분의 프로그래밍 언어와 호환되는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Text Analytics [Go용 SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

등록하는 동안 생성된 [엔드포인트 및 액세스 키](../How-tos/text-analytics-how-to-access-key.md)도 있어야 합니다.

## <a name="set-up-a-new-project"></a>새 프로젝트 설정

원하는 코드 편집기 또는 IDE에서 새 Go 프로젝트를 만듭니다. 그런 다음, Go 파일에 다음 import 문을 추가합니다.

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
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

## <a name="create-text-analytics-client-and-authenticate-credentials"></a>Text Analytics 클라이언트 및 인증 자격 증명 만들기

프로젝트의 main 함수에서 새 `TextAnalytics` 개체를 호출합니다. Text Analytics 구독에 맞는 Azure 지역을 사용합니다. 예: `https://eastus.api.cognitive.microsoft.com` 평가판 키를 사용하는 경우 위치를 업데이트할 필요가 없습니다.

```golang
//Replace 'eastus' with the correct region for your Text Analytics subscription
textAnalyticsClient := textanalytics.New("https://eastus.api.cognitive.microsoft.com")
```

키의 변수를 만들어서 `autorest.NewCognitiveServicesAuthorizer` 함수에 전달합니다. 그러면 이 함수가 클라이언트의 `authorizer` 속성에 전달됩니다.

```golang
subscriptionKey := "<<subscriptionKey>>"
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>정서 분석

이전에 만든 클라이언트를 사용하는 `SentimentAnalysis()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id`, `Language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("1"),
            Text:StringPointer("This was a waste of my time. The speaker put me to sleep."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("2"),
            Text:StringPointer("No tengo dinero ni nada que dar..."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("it"),
            ID:StringPointer("3"),
            Text:StringPointer("L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 `textAnalyticsclient.Sentiment()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

프로젝트의 main 함수에서 `SentimentAnalysis()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>언어 검색

이전에 만든 클라이언트를 사용하는 `LanguageDetection()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `LanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하며, `id`는 임의의 값이 될 수 있습니다. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("1"),
            Text:StringPointer("Este es un document escrito en Español."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("2"),
            Text:StringPointer("这是一个用中文写的文件"),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 `textAnalyticsclient.DetectLanguage()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 검색된 언어를 인쇄합니다.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

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
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

프로젝트의 main 함수에서 `LanguageDetection()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 0 Detected Languages with Score: English 1.000000,
Document ID: 1 Detected Languages with Score: Spanish 1.000000,
Document ID: 2 Detected Languages with Score: Chinese_Simplified 1.000000,
```

## <a name="entity-recognition"></a>엔터티 인식

이전에 만든 클라이언트를 사용하는 `ExtractEntities()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("1"),
            Text:StringPointer("La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 `call textAnalyticsclient.Entities()`를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 엔터티 점수를 인쇄합니다.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

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
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
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

Document ID: 1
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 21  Length: 9   Score: 0.999756
        Name: Redmond (Washington)  Type: Location
            Offset: 60  Length: 7   Score: 0.991128
        Name: 21 kilómetros Type: Quantity  Sub-Type: Dimension

            Offset: 71  Length: 13  Score: 0.800000
        Name: Seattle   Type: Location
            Offset: 88  Length: 7   Score: 0.999878
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

이전에 만든 클라이언트를 사용하는 `ExtractKeyPhrases()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("ja"),
            ID:StringPointer("0"),
            Text:StringPointer("猫は幸せ"),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("de"),
            ID:StringPointer("1"),
            Text:StringPointer("Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("2"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("3"),
            Text:StringPointer("A mi me encanta el fútbol!"),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

동일한 함수에서 textAnalyticsclient.KeyPhrases()를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 핵심 구를 인쇄합니다.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

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
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

프로젝트의 main 함수에서 `ExtractKeyPhrases()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 0
    Extracted Key Phrases:
        幸せ

Document ID: 1
    Extracted Key Phrases:
        Stuttgart
        Hotel
        Fahrt
        Fu

Document ID: 2
    Extracted Key Phrases:
        cat
        veterinarian

Document ID: 3
    Extracted Key Phrases:
        fútbol
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목

 [Text Analytics 개요](../overview.md) [FAQ(질문과 대답)](../text-analytics-resource-faq.md)
