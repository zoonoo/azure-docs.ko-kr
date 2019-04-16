---
title: Speech C# SDK 사용
titleSuffix: Azure Cognitive Services
description: Speech Service를 사용하면 단일 요청을 통해 오디오를 수신하고 LUIS 예측 JSON 개체를 반환할 수 있습니다. 이 문서에서는 C# 프로젝트를 다운로드한 후 Visual Studio에서 사용하여 마이크에 말을 하고 LUIS 예측 정보를 수신합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: diberry
ms.openlocfilehash: 9d6173ee25f28aa884513d126c06a8a7c722098d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273841"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Language Understanding 앱을 사용하여 음성 서비스 통합
[Speech Service](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/)를 사용하면 단일 요청을 통해 오디오를 수신하고 LUIS 예측 JSON 개체를 반환할 수 있습니다. 이 문서에서는 C# 프로젝트를 다운로드한 후 Visual Studio에서 사용하여 마이크에 말을 하고 LUIS 예측 정보를 수신합니다. 이 프로젝트에서는 이미 참조로 포함되어 있는 Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) 패키지를 사용합니다. 

이 문서에서는 애플리케이션을 가져오기 위해 무료 [LUIS][LUIS] 웹 사이트 계정이 필요합니다.

## <a name="create-luis-endpoint-key"></a>LUIS 엔드포인트 키 만들기
Azure Portal에서 LUIS 앱용 **Cognitive Service**(LUIS) 키를 [만듭니다](luis-how-to-azure-subscription.md).  

## <a name="import-human-resources-luis-app"></a>Human Resources LUIS 앱 가져오기
이 문서의 의도 및 발언은 [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) GitHub 리포지토리에서 사용할 수 있는 Human Resources LUIS 앱에서 가져온 것입니다. [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json) 파일을 다운로드하고, `.json` 확장명으로 저장하고, LUIS로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 

이 앱에는 Human Resources 도메인과 관련된 의도, 엔터티 및 발언이 있습니다. 발언 예제는 다음과 같습니다.

|예제 발화|
|--|
|Who is John Smith's manager?|
|Who does John Smith manage?|
|Where is Form 123456?|
|Do I have any paid time off?|


## <a name="add-keyphrase-prebuilt-entity"></a>KeyPhrase 사전 빌드 엔터티 추가
앱을 가져온 후 **엔터티**를 선택한 다음, **사전 빌드 엔터티 추가**를 선택합니다. **KeyPhrase** 엔터티를 추가합니다. KeyPhrase 엔터티는 발언에서 핵심 주제를 추출합니다.

## <a name="train-and-publish-the-app"></a>앱 학습 및 게시
1. 상단 오른쪽 탐색 모음에서 **학습** 단추를 선택하여 LUIS 앱을 학습합니다.

2. 맨 위 오른쪽 막대에서 **관리**를 선택한 다음, 왼쪽 탐색 창에서 **키 및 엔드포인트**를 선택합니다. 

3. **키 및 엔드포인트** 페이지에서 [LUIS 엔드포인트 키 만들기](#create-luis-endpoint-key) 섹션에서 만든 LUIS 키를 할당합니다.

   이 페이지에서 [LUIS 엔드포인트 키 만들기](#create-luis-endpoint-key) 섹션에서 만든 LUIS 키의 앱 ID를 게시 지역 및 구독 ID를 수집합니다. 이 문서의 뒷부분에 나오는 이러한 값을 사용하도록 코드를 수정해야 합니다. 
  
   이 연습을 위해 무료 시작 키를 사용하지 **않도록** 합니다. Azure Portal에서 만든 **Language Understanding** 키만 이 연습에서 작동됩니다. 

   https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. 맨 위 오른쪽 막대에 있는 **게시** 단추를 선택하여 LUIS 앱을 게시합니다. 

## <a name="audio-device"></a>오디오 디바이스
이 문서에서는 컴퓨터의 오디오 디바이스를 사용합니다. 마이크가 있는 헤드셋이거나 기본 제공 오디오 디바이스일 수 있습니다. 오디오 입력 수준을 확인하여 오디오 디바이스에서 음성이 감지되기 위해 평소보다 더 크게 말해야 하는지 알아봅니다. 

## <a name="download-the-luis-sample-project"></a>LUIS 샘플 프로젝트 다운로드
 [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) 리포지토리를 복제하고 다운로드합니다. Visual Studio에서 [음성 의도 변환 프로젝트](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition)를 열고 NuGet 패키지를 복원합니다. VS 솔루션 파일은 documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln입니다.

Speech SDK는 이미 참조로 포함되어 있습니다. 

[![SMicrosoft.CognitiveServices.Speech NuGet 패키지를 표시하는 Visual Studio 2017 스크린샷](./media/luis-tutorial-speech-to-intent/nuget-package.png "Microsoft.CognitiveServices.Speech NuGet 패키지를 표시하는 Visual Studio 2017 스크린샷")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>C# 코드 수정
`Program.cs` 파일을 열고 다음 변수를 변경합니다.

|변수 이름|목적|
|--|--|
|LUIS_assigned_endpoint_key|[게시] 페이지에서 엔드포인트 URL의 할당된 구독 키 값에 해당합니다.|
|LUIS_endpoint_key_region|예를 들어, 엔드포인트 URL의 첫 번째 하위 도메인에 해당합니다 `westus`|
|LUIS_app_ID|**app/** 다음의 엔드포인트 URL 경로에 해당합니다.|

`Program.cs` 파일에는 이미 [인적 자원] 의도가 매핑되어 있습니다.

앱을 빌드 및 실행합니다. 

## <a name="test-code-with-utterance"></a>발언을 사용하여 코드 테스트
마이크에 대고 "레드몬드에서 인가된 치과 의사는 누구인가요?"라고 말합니다.

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

올바른 **GetEmployeeBenefits** 의도가 85%의 신뢰도로 검색되었습니다. keyPhrase 엔터티가 반환되었습니다. 

Speech SDK가 전체 LUIS 응답을 반환합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS HumanResources 앱을 삭제합니다. 이렇게 하려면 앱을 선택한 다음, 목록 위의 상황에 맞는 도구 모음에서 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

샘플 코드 사용이 완료되면 디렉터리를 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [봇과 LUIS 통합](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
