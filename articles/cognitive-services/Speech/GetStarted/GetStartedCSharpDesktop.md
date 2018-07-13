---
title: C# 데스크톱 라이브러리를 사용하여 Microsoft Speech Recognition API 시작 | Microsoft Docs
description: Microsoft Speech Recognition API를 사용하여 음성 오디오를 텍스트로 변환하는 기본 Windows 응용 프로그램을 개발합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373887"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>Windows의.NET용 C#에서 Speech Recognition API 시작

이 페이지에서는 Speech Recognition API를 사용하여 음성 오디오를 텍스트로 변환하는 기본 Windows 응용 프로그램을 개발하는 방법을 보여 줍니다. 클라이언트 라이브러리를 사용하면 실시간 스트리밍이 가능합니다. 즉, 클라이언트 응용 프로그램에서 오디오를 서비스로 보낼 때 부분 인식 결과를 동시에 비동기적으로 다시 받습니다.

모든 장치에서 실행되는 응용 프로그램에서 Speech Service를 사용하려는 개발자는 C# 데스크톱 라이브러리를 사용할 수 있습니다. 라이브러리를 사용하려면 32비트 플랫폼의 경우 [Microsoft.ProjectOxford.SpeechRecognition-x86 NuGet 패키지](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/)를 설치하고, 64비트 플랫폼의 경우 [Microsoft.ProjectOxford.SpeechRecognition-x64 NuGet 패키지](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/)를 설치합니다. 클라이언트 라이브러리 API 참조는 [Microsoft Speech C# 데스크톱 라이브러리](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)를 참조하세요.

다음 섹션에서는 C# 데스크톱 라이브러리를 사용하여 C# 샘플 응용 프로그램을 설치, 빌드 및 실행하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

다음 샘플은 [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)을 사용하여 Windows 8 이상 및 .NET Framework 4.5 이상용으로 개발되었습니다.

### <a name="get-the-sample-application"></a>샘플 응용 프로그램 가져오기

[Speech C# 데스크톱 라이브러리 샘플](https://github.com/microsoft/cognitive-speech-stt-windows) 리포지토리에서 샘플을 복제합니다.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API 구독 및 평가판 구독 키 가져오기

Speech API는 Cognitive Services(이전의 Project Oxford)의 일부입니다. [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지에서 평가판 구독 키를 가져올 수 있습니다. Speech API를 선택한 후에 **API 키 가져오기**를 선택하여 키를 가져옵니다. 기본 및 보조 키를 반환합니다. 두 키는 모두 동일한 할당량에 연결되므로 두 키 중 하나를 사용할 수 있습니다.

> [!IMPORTANT]
> * 구독 키를 가져오세요. Speech 클라이언트 라이브러리를 사용하려면 먼저 [구독 키](https://azure.microsoft.com/try/cognitive-services/)가 있어야 합니다.
>
> * 구독 키를 사용하세요. 제공된 C# 데스크톱 샘플 응용 프로그램을 사용하여 샘플을 실행할 때 구독 키를 텍스트 상자에 붙여넣습니다. 자세한 내용은 [샘플 응용 프로그램 실행](#step-3-run-the-sample-application)을 참조하세요.

## <a name="step-1-install-the-sample-application"></a>1단계: 샘플 응용 프로그램 설치

1. Visual Studio 2015를 시작하고, **파일** > **열기** > **프로젝트/솔루션**을 차례로 선택합니다.

2. 다운로드한 Speech Recognition API 파일을 저장한 폴더를 찾습니다. **음성** > **Windows**를 차례로 선택한 다음, Sample-WP 폴더를 선택합니다.

3. SpeechToText-WPF-Samples.sln이라는 Visual Studio 2015 솔루션(.sln) 파일을 두 번 클릭하여 엽니다. 솔루션이 Visual Studio에서 열립니다.

## <a name="step-2-build-the-sample-application"></a>2단계: 샘플 응용 프로그램 빌드

1. *의도가 있는 인식*을 사용하려면 먼저 [LUIS(Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)에 등록해야 합니다. 그런 다음, LUIS 앱의 엔드포인트 URL을 사용하여 samples/SpeechRecognitionServiceExample 폴더의 app.config 파일에서 `LuisEndpointUrl` 키의 값을 설정합니다. LUIS 앱의 엔드포인트 URL에 대한 자세한 내용은 [앱 게시](../../luis/luis-get-started-create-app.md#publish-your-app)를 참조하세요.

   > [!TIP]
   > XML 파서에서 URL을 올바르게 해석하도록 LUIS 엔드포인트 URL의 `&` 문자를 `&amp;`로 바꿉니다.

2. Ctrl+Shift+B를 누르거나 리본 메뉴에서 **빌드**를 선택합니다. 그런 다음, **솔루션 빌드**를 선택합니다.

## <a name="step-3-run-the-sample-application"></a>3단계: 샘플 응용 프로그램 실행

1. 빌드가 완료되면 F5 키를 누르거나 리본 메뉴에서 **시작**을 선택하여 샘플을 실행합니다.

2. **Project Oxford 음성 - 텍스트 변환 샘플** 창으로 이동합니다. 표시된 대로 구독 키를 **시작하려면 여기에 구독 키 붙여넣기** 텍스트 상자에 붙여넣습니다. PC 또는 랩톱에서 구독 키를 유지하려면 **키 저장**을 선택합니다. 시스템에서 구독 키를 삭제하려면 **키 삭제**를 선택하여 PC 또는 랩톱에서 제거합니다.

   ![음성 인식 키 붙여넣기](../Images/SpeechRecog_paste_key.PNG)

3. **음성 인식 소스** 아래에서 6개의 음성 소스 중 하나를 선택합니다. 이 소스는 다음 두 가지 기본 입력 범주에 속합니다.

   * 컴퓨터의 마이크 또는 연결된 마이크를 사용하여 음성을 캡처합니다.
   * 오디오 파일을 재생합니다.

   각 범주에는 다음 세 가지 인식 모드가 있습니다.

    * **ShortPhrase 모드**: 최대 15초가 걸리는 발화입니다. 데이터가 서버로 보내지면 클라이언트에서 여러 개의 부분 결과 및 여러 개의 N 상위 선택 항목이 있는 하나의 최종 결과를 받습니다.
    * **LongDictation 모드**: 최대 2분이 걸리는 발화입니다. 데이터가 서버로 보내지면 서버에서 문장 일시 중지를 나타내는 위치에 따라 클라이언트에서 여러 개의 부분 결과 및 여러 개의 최종 결과를 받습니다.
    * **의도 감지**: 서버에서 음성 입력에 대해 추가 구조화된 정보를 반환합니다. 의도 감지를 사용하려면 먼저 [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)를 사용하여 모델이 학습되어야 합니다.

이 샘플 응용 프로그램에서 샘플 오디오 파일을 사용합니다. samples/SpeechRecognitionServiceExample 폴더 아래에서 이 샘플과 함께 다운로드한 리포지토리의 파일을 찾습니다. **Shortphrase 모드에 wav 파일 사용** 또는 **Longdictation 모드에 wav 파일 사용**을 음성 입력으로 선택할 때 다른 파일이 선택되지 않으면 이러한 샘플 오디오 파일이 자동으로 실행됩니다. 현재 WAV 오디오 형식만 지원됩니다.

![Speech to Text 인터페이스](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>샘플 설명

### <a name="recognition-events"></a>인식 이벤트

* **부분 결과 이벤트**: 이 이벤트는 말하기를 끝내거나(`MicrophoneRecognitionClient`를 사용하는 경우) 데이터 보내기를 끝내기(`DataRecognitionClient`를 사용하는 경우) 전에도 Speech Service에서 사용자가 말할 수 있는 내용을 예측할 때마다 호출됩니다.
* **오류 이벤트**: 서비스에서 오류를 감지하면 호출됩니다.
* **의도 이벤트**: 최종 인식 결과가 구조화된 JSON 의도로 구문 분석된 후 "WithIntent" 클라이언트(ShortPhrase 모드의 경우만)에서 호출됩니다.
* **결과 이벤트**:
  * `ShortPhrase` 모드에서는 이 이벤트가 호출되고, 말하기를 끝낸 후 N 상위 결과를 반환합니다.
  * `LongDictation` 모드에서는 서비스에서 문장 일시 중지를 식별하는 위치에 따라 이벤트 처리기가 여러 번 호출됩니다.
  * **N 상위 선택 항목 각각에 대해** 신뢰도 값 및 인식된 텍스트의 몇 가지 다른 형식이 반환됩니다. 자세한 내용은 [출력 형식](../Concepts.md#output-format)을 참조하세요.

이벤트 처리기는 이미 코드에서 코드 주석 형식으로 지정되었습니다.

## <a name="related-topics"></a>관련된 항목

* [Microsoft Speech 데스크톱 라이브러리 참조](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Android의 Java에서 Microsoft Speech Recognition API 시작](GetStartedJavaAndroid.md)
* [iOS의 Objective-C에서 Microsoft Speech Recognition API 시작](Get-Started-ObjectiveC-iOS.md)
* [JavaScript에서 Microsoft Speech Recognition API 시작](GetStartedJSWebsockets.md)
* [REST를 통해 Microsoft Speech Recognition API 시작](GetStartedREST.md)
