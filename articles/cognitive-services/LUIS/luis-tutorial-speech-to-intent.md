---
title: LUIS에서 Speech C# SDK 사용 - Azure | Microsoft Docs
titleSuffix: Azure
description: Speech C# SDK 샘플을 사용하여 마이크에 말하고 LUIS 의도 및 엔터티 예측을 반환합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: diberry
ms.openlocfilehash: aadca428fa076d697cc0f893673672850ddc27d4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124399"
---
# <a name="integrate-speech-service"></a>Speech Service 통합
[Speech Service](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/)를 사용하면 단일 요청을 통해 오디오를 수신하고 LUIS 예측 JSON 개체를 반환할 수 있습니다.

이 문서에서는 C# 프로젝트를 다운로드한 후 Visual Studio에서 사용하여 마이크에 말을 하고 LUIS 예측 정보를 수신합니다. 이 프로젝트에서는 이미 참조로 포함되어 있는 Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) 패키지를 사용합니다. 

이 문서에서는 응용 프로그램을 가져오기 위해 무료 [LUIS][LUIS] 웹 사이트 계정이 필요합니다.

## <a name="create-luis-endpoint-key"></a>LUIS 엔드포인트 키 만들기
Azure Portal에서 LUIS(**Language Understanding**) 키를 [만듭니다](luis-how-to-azure-subscription.md#create-luis-endpoint-key). 

## <a name="import-human-resources-luis-app"></a>Human Resources LUIS 앱 가져오기
이 문서의 의도 및 발언은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) Github 리포지토리에서 사용할 수 있는 Human Resources LUIS 앱에서 가져온 것입니다. [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) 파일을 다운로드하고 *.json 확장명으로 저장한 후 LUIS로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 

이 앱에는 Human Resources 도메인과 관련된 의도, 엔터티 및 발언이 있습니다. 발언 예제는 다음과 같습니다.

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>KeyPhrase 사전 빌드 엔터티 추가
앱을 가져온 후 **엔터티**를 선택하고 **사전 빌드 엔터티 관리**를 선택합니다. **KeyPhrase** 엔터티를 추가합니다. KeyPhrase 엔터티는 발언에서 핵심 주제를 추출합니다.

## <a name="train-and-publish-the-app"></a>앱 학습 및 게시
1. 상단 오른쪽 탐색 모음에서 **학습** 단추를 선택하여 LUIS 앱을 학습합니다.

2. **게시**를 선택하여 게시 페이지로 이동합니다. 

3. **게시** 페이지 아래쪽에서 [LUIS 엔드포인트 키 만들기](#create-luis-endpoint-key) 섹션에서 만든 LUIS 키를 추가합니다.

4. 게시 슬롯 오른쪽에 있는 **게시** 단추를 선택하여 LUIS 앱을 게시합니다. 

  **게시** 페이지에서 [LUIS 엔드포인트 키 만들기](#create-luis-endpoint-key) 섹션에서 만든 LUIS 키의 앱 ID를 게시 지역 및 구독 ID를 수집합니다. 이 문서의 뒷부분에 나오는 이러한 값을 사용하도록 코드를 수정해야 합니다. 

  이러한 모든 값은 만든 키에 대한 **게시** 페이지 맨 아래에 있는 엔드포인트 URL에 포함됩니다. 
  
  이 연습을 위해 무료 시작 키를 사용하지 **않도록** 합니다. Azure Portal에서 만든 **Language Understanding** 키만 이 연습에서 작동됩니다. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=

## <a name="audio-device"></a>오디오 장치
이 문서에서는 컴퓨터의 오디오 장치를 사용합니다. 마이크가 있는 헤드셋이거나 기본 제공 오디오 장치일 수 있습니다. 오디오 입력 수준을 확인하여 오디오 장치에서 음성이 감지되기 위해 평소보다 더 크게 말해야 하는지 알아봅니다. 

## <a name="download-the-luis-sample-project"></a>LUIS 샘플 프로젝트 다운로드
 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) 리포지토리를 복제하고 다운로드합니다. Visual Studio에서 [음성 의도 변환 프로젝트](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition)를 열고 NuGet 패키지를 복원합니다. VS 솔루션 파일은 .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln입니다.

Speech SDK는 이미 참조로 포함되어 있습니다. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Microsoft.CognitiveServices.Speech NuGet 패키지를 표시하는 Visual Studio 2017 스크린샷")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>C# 코드 수정
**LUIS_samples.cs** 파일을 열고 다음 변수를 변경합니다.

|변수 이름|목적|
|--|--|
|luisSubscriptionKey|게시 페이지에서 엔드포인트 URL 등록 키 값에 해당합니다.|
|luisRegion|엔드포인트 URL의 첫 번째 하위 도메인에 해당합니다.|
|luisAppId|**app/** 다음의 엔드포인트 URL 경로에 해당합니다.|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "LUIS_samples.cs 변수를 표시하는 Visual Studio 2017 스크린샷")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

파일에는 이미 Human Resources 의도가 매핑되어 있습니다.

[![](./media/luis-tutorial-speech-to-intent/intents.png "LUIS_samples.cs 의도를 표시하는 Visual Studio 2017 스크린샷")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

앱을 빌드 및 실행합니다. 

## <a name="test-code-with-utterance"></a>발언을 사용하여 코드 테스트
**1**을 선택하고 마이크에 대고 "Who is the manager of John Smith"라고 말합니다.

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

올바른 의도 **GetEmployeeOrgChart**가 61% 신뢰도로 검색되었습니다. keyPhrase 엔터티가 반환되었습니다. 

Speech SDK가 전체 LUIS 응답을 반환합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS HumanResources 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 줄임표(***...***) 단추를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

샘플 코드 사용이 완료되면 LUIS-Samples 디렉터리를 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [봇과 LUIS 통합](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website