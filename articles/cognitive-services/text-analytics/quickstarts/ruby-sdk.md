---
title: '빠른 시작: Ruby SDK를 사용하여 Text Analytics Cognitive Service 호출'
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 688887826fa803b616ca737bc8558aa17ed80e37
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297763"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>빠른 시작: Ruby SDK를 사용하여 Text Analytics 서비스 호출

<a name="HOLTop"></a>


이 빠른 시작을 사용하여 Ruby용 Text Analytics SDK를 통해 언어 분석을 시작합니다. [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API는 대부분의 프로그래밍 언어와 호환되는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb)에서 확인할 수 있습니다.

API 기술 문서는 [API 정의](//go.microsoft.com/fwlink/?LinkID=759346)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Ruby 2.5.5 이상](https://www.ruby-lang.org/)
* Text Analytics [Ruby용 SDK](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

등록하는 동안 생성된 [엔드포인트 및 액세스 키](../How-tos/text-analytics-how-to-access-key.md)도 있어야 합니다. 

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Ruby 프로젝트 만들기 및 SDK 설치

1. 새 Ruby 프로젝트를 만들고, `Gemfile`이라는 새 파일을 추가합니다.
2. 아래 코드를 `Gemfile`에 추가하여 Text Analytics SDK를 프로젝트에 추가합니다.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Text Analytics 클라이언트 만들기

1. 즐겨찾는 편집기 또는 IDE에서 `TextAnalyticsExamples.rb`라는 새 파일을 만듭니다. Text Analytics SDK를 가져옵니다.

2. 자격 증명 개체는 Text Analytics 클라이언트에서 사용됩니다. `CognitiveServicesCredentials.new()`를 사용하여 이 개체를 만들고 구독 키를 전달합니다.

3. 올바른 Text Analytics 엔드포인트를 사용하여 클라이언트를 만듭니다.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>정서 분석

Text Analytics SDK 또는 API를 사용하여 텍스트 레코드 세트에 대한 감정 분석을 수행할 수 있습니다. 다음 예제에서는 여러 문서에 대한 감정 점수를 표시합니다.

1. 위에서 만든 Text Analytics 클라이언트를 매개 변수로 사용하는 `SentimentAnalysisExample()`이라는 새 함수를 만듭니다.

2. 분석할 `MultiLanguageInput` 개체 세트를 정의합니다. 각 개체에 대한 언어와 텍스트를 추가합니다. ID는 임의의 값일 수 있습니다.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. 동일한 함수 내에서 문서를 목록으로 결합합니다. 이 목록을 `MultiLanguageBatchInput` 개체의 `documents` 필드에 추가합니다. 

4. 문서를 보내는 매개 변수로 `MultiLanguageBatchInput` 개체를 사용하여 클라이언트의 `sentiment()` 함수를 호출합니다. 결과가 반환되면 인쇄합니다.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. `SentimentAnalysisExample()` 함수를 호출합니다.

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>출력

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>언어 검색

Text Analytics 서비스는 많은 언어와 로캘에서 텍스트 문서의 언어를 검색할 수 있습니다. 다음 예제에서는 여러 문서가 작성된 언어를 표시합니다.

1. 위에서 만든 Text Analytics 클라이언트를 매개 변수로 사용하는 `DetectLanguageExample()`이라는 새 함수를 만듭니다. 

2. 분석할 `LanguageInput` 개체 세트를 정의합니다. 각 개체에 대한 언어와 텍스트를 추가합니다. ID는 임의의 값일 수 있습니다.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. 동일한 함수 내에서 문서를 목록으로 결합합니다. 이 목록을 `LanguageBatchInput` 개체의 `documents` 필드에 추가합니다. 

4. 문서를 보내는 매개 변수로 `LanguageBatchInput` 개체를 사용하여 클라이언트의 `detect_language()` 함수를 호출합니다. 결과가 반환되면 인쇄합니다.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. `DetectLanguageExample` 함수를 호출합니다.

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>출력

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>엔터티 인식

Text Analytics 서비스는 텍스트 문서에서 다양한 엔터티(사람, 장소 및 사물)를 구분하고 추출할 수 있습니다. 다음 예제에서는 몇 가지 예제 문서에서 찾은 엔터티를 표시합니다.

1. 위에서 만든 Text Analytics 클라이언트를 매개 변수로 사용하는 `Recognize_Entities()`라는 새 함수를 만듭니다.

2. 분석할 `MultiLanguageInput` 개체 세트를 정의합니다. 각 개체에 대한 언어와 텍스트를 추가합니다. ID는 임의의 값일 수 있습니다.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. 동일한 함수 내에서 문서를 목록으로 결합합니다. 이 목록을 `MultiLanguageBatchInput` 개체의 `documents` 필드에 추가합니다. 

4. 문서를 보내는 매개 변수로 `MultiLanguageBatchInput` 개체를 사용하여 클라이언트의 `entities()` 함수를 호출합니다. 결과가 반환되면 인쇄합니다.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. `RecognizeEntitiesExample` 함수를 호출합니다.
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>출력

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>핵심 문구 추출

Text Analytics 서비스는 문장에서 핵심 구를 추출할 수 있습니다. 다음 예제에서는 여러 언어의 여러 예제 문서에 찾은 엔터티를 표시합니다.

1. 위에서 만든 Text Analytics 클라이언트를 매개 변수로 사용하는 `KeyPhraseExtractionExample()`이라는 새 함수를 만듭니다.

2. 분석할 `MultiLanguageInput` 개체 세트를 정의합니다. 각 개체에 대한 언어와 텍스트를 추가합니다. ID는 임의의 값일 수 있습니다.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. 동일한 함수 내에서 문서를 목록으로 결합합니다. 이 목록을 `MultiLanguageBatchInput` 개체의 `documents` 필드에 추가합니다. 

4. 문서를 보내는 매개 변수로 `MultiLanguageBatchInput` 개체를 사용하여 클라이언트의 `key_phrases()` 함수를 호출합니다. 결과가 반환되면 인쇄합니다.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. `KeyPhraseExtractionExample` 함수를 호출합니다.

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>출력

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)
