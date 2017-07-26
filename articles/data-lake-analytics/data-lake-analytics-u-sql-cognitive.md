---
title: "Azure Data Lake Analytics에서 U-SQL 인식 기능 사용 | Microsoft Docs"
description: "U-SQL에서 인텔리전스 인식 기능을 사용하는 방법에 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f77329f9838d6e824afa7234de90f62257a004de
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---

# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>자습서: U-SQL의 인식 기능 시작

U-SQL에 대한 인식 기능은 개발자가 빅 데이터 프로그램에서 인텔리전스를 사용하도록 합니다. 전체 프로세스는 간단합니다.

* 참조 어셈블리 문을 사용하여 U-SQL 스크립트에 대한 인식 기능을 사용합니다.
* 처리 작업을 호출하여 인식 기능 사용 

## <a name="imaging-scenarios"></a>이미징 시나리오

### <a name="example-image-tagging"></a>예: 이미지 태그 지정

다음 예제는 이미지에 있는 개체를 감지하기 위한 이미징 기능의 종단 간 사용을 보여 줍니다.

    REFERENCE ASSEMBLY ImageCommon;
    REFERENCE ASSEMBLY FaceSdk;
    REFERENCE ASSEMBLY ImageEmotion;
    REFERENCE ASSEMBLY ImageTagging;
    REFERENCE ASSEMBLY ImageOcr;

    @imgs =
        EXTRACT FileName string, ImgData byte[]
        FROM @"/images/{FileName:*}.jpg"
        USING new Cognition.Vision.ImageExtractor();

    // Extract the number of objects on each image and tag them 
    @objects =
        PROCESS @imgs 
        PRODUCE FileName,
                NumObjects int,
                Tags string
        READONLY FileName
        USING new Cognition.Vision.ImageTagger();


### <a name="extract-emotions-from-human-faces"></a>사람 얼굴에서 감정을 추출합니다. 

    @emotions =
        PROCESS @imgs
        PRODUCE FileName string,
                NumFaces int,
                Emotion string
        READONLY FileName
        USING new Cognition.Vision.EmotionAnalyzer();

### <a name="estimate-age-and-gender-for-human-faces"></a>사람 얼굴에서 연령 및 성별을 예측합니다.

    @faces = 
            PROCESS @imgs
            PRODUCE FileName,
                    NumFaces int,
                    FaceAge string,
                    FaceGender string
            READONLY FileName
            USING new Cognition.Vision.FaceDetector();

### <a name="detect-text-in-images-ocr"></a>이미지 (OCR)에 있는 텍스트를 감지합니다.

    @ocrs =
            PROCESS @imgs
            PRODUCE FileName,
                    Text string
            READONLY FileName
            USING new Cognition.Vision.OcrExtractor();

## <a name="text-scenarios"></a>텍스트 시나리오

### <a name="input-data"></a>데이터 입력

레프 톨스토이의 "전쟁과 평화"로 구성된 입력이 있다고 가정합니다.

    REFERENCE ASSEMBLY [TextCommon];
    REFERENCE ASSEMBLY [TextSentiment];
    REFERENCE ASSEMBLY [TextKeyPhrase];

    @WarAndPeace =
        EXTRACT No int,
                Year string,
                Book string,
                Chapter string,
                Text string
        FROM @"/usqlext/samples/cognition/war_and_peace.csv"
        USING Extractors.Csv();

### <a name="extract-key-phrases-for-each-paragraph"></a>각 단락에 대한 핵심 문구 추출

    @keyphrase =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                KeyPhrase string
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.KeyPhraseExtractor();

    // Tokenize the key phrases.
    @kpsplits =
        SELECT No,
            Year,
            Book,
            Chapter,
            Text,
            T.KeyPhrase
        FROM @keyphrase
            CROSS APPLY
                new Cognition.Text.Splitter("KeyPhrase") AS T(KeyPhrase);
    
### <a name="perform-sentiment-analysis-on-each-paragraph"></a>각 단락에서 감정 분석을 수행합니다.

    @sentiment =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                Sentiment string,
                Conf double
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.SentimentAnalyzer(true);


