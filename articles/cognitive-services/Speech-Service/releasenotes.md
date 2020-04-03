---
title: 릴리스 노트 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스 기능 릴리스, 개선 사항, 버그 수정 및 알려진 문제의 실행 중인 로그입니다.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607410"
---
# <a name="release-notes"></a>릴리스 정보
## <a name="speech-sdk-1110-2020-march-release"></a>음성 SDK 1.11.0: 2020-3월 릴리스

**새로운 기능**

- 리눅스 : 레드 햇 엔터프라이즈 리눅스 (RHEL)/CentOS 7 x64 음성 SDK에 대한 시스템을 구성하는 방법에 대한 [지침에](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) 대한 지원을 추가했습니다.
- 리눅스: 리눅스 ARM32 및 ARM64에 .NET 코어 C #에 대한 지원을 추가했습니다. 자세한 내용은 [여기](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)를 읽어보세요. 
- C#, C++: `UtteranceId` `ConversationTranscriptionResult`모든 중간 및 최종 음성 인식 결과에서 일관된 ID를 추가했습니다. C [#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C ++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)에 대한 세부 정보 .
- 파이썬 : `Language ID`에 대한 지원을 추가했습니다. [GitHub 리포지토리에서](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)speech_sample.py를 참조하십시오.
- Windows: 모든 win32 콘솔 응용 프로그램에 대해 Windows 플랫폼에 압축된 오디오 입력 형식 지원을 추가했습니다. 자세한 내용은 [여기 .](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams) 
- 자바 스크립트 : NodeJS에서 음성 합성 (텍스트 - 음성 변환)을 지원합니다. 자세한 내용은 [여기를 참조하십시오.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) 
- 자바 스크립트 : 모든 송신 및 수신 된 메시지를 검사 할 수 있도록 새 API를 추가합니다. 자세한 내용은 [여기를 참조하십시오.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) 
        
**버그 수정**

- C #, C ++: `SendMessageAsync` 이제 이진 메시지를 이진 문자로 전송하는 문제를 수정했습니다. C [#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C ++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)에 대한 세부 정보 .
- C#, C++: 개체 앞에 `Connection MessageReceived` `Connection` 삭제된 경우 `Recognizer` 이벤트를 사용하면 충돌이 발생할 수 있는 문제를 수정했습니다. C [#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C ++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived)에 대한 세부 정보 .
- Android: 마이크의 오디오 버퍼 크기가 800ms에서 100ms로 감소하여 대기 시간을 개선했습니다.
- 안드로이드 : [issue](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) 안드로이드 스튜디오에서 x86 안드로이드 에뮬레이터문제를 수정했습니다.
- 자바 스크립트 : API와 중국의 `fromSubscription` 지역에 대한 지원을 추가했습니다. 자세한 내용은 [여기 .](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-) 
- 자바 스크립트: NodeJS의 연결 오류에 대한 오류 정보를 추가합니다.
        
**샘플**

- Unity: 의도 인식 공개 샘플이 수정되어 LUIS json 가져오기가 실패했습니다. 자세한 내용은 [여기 .](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- 파이썬 : `Language ID`샘플이 추가되었습니다. 자세한 내용은 [여기 .](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Covid19 요약 테스트**

지난 몇 주 동안 원격으로 작업했기 때문에 평소처럼 수동 장치 확인 테스트를 수행할 수 없었습니다. 이 것의 예는 리눅스, iOS 및 macOS에서 마이크 입력 및 스피커 출력을 테스트하는 것입니다. 이러한 플랫폼에서 아무 것도 망칠 수 있다고 생각되는 변경 사항을 변경하지 않았으며 자동화된 테스트는 모두 통과했습니다. 우리가 뭔가를 놓친 드문 경우에, 우리는 [GitHub에](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen)알려주시기 바랍니다 .<br> 지속적인 지원에 감사드립니다. 언제나 처럼, [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) 또는 스택 [오버플로에](https://stackoverflow.microsoft.com/questions/tagged/731)질문 또는 피드백을 게시 하십시오.<br>
건강을 유지!

## <a name="speech-sdk-1100-2020-february-release"></a>음성 SDK 1.10.0: 2020-2월 릴리스

**새로운 기능**

 - 파이썬의 새로운 3.8 릴리스를 지원하기 위해 파이썬 패키지를 추가했습니다.
 - 레드 햇 엔터프라이즈 리눅스 (RHEL)/CentOS 8 x64 지원 (C ++, C #, 자바, 파이썬).
   > [!NOTE] 
   > 고객은 [다음 지침에](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)따라 OpenSSL을 구성해야 합니다.
 - 데비안과 우분투에 대한 리눅스 ARM32 지원.
 - 대화 상자서비스 커넥터는 이제 BotFrameworkConfig에서 선택적 "봇 ID" 매개 변수를 지원합니다. 이 매개 변수를 사용하면 단일 Azure 음성 리소스를 사용하여 여러 직접 회선 음성 봇을 사용할 수 있습니다. 매개 변수를 지정하지 않으면 기본 봇(직접 회선 음성 채널 구성 페이지에서 결정)이 사용됩니다.
 - 대화 상자서비스커넥터에는 이제 SpeechActivityTemplate 속성이 있습니다. 이 JSON 문자열의 내용은 음성 인식과 같은 이벤트에 대한 응답으로 자동으로 생성되는 활동을 포함하여 직접 회선 음성 봇에 도달하는 모든 활동에서 지원되는 다양한 필드를 미리 채우기 위해 직접 라인 음성에서 사용됩니다.
 - TTS는 이제 인증에 구독 키를 사용하여 신디사이저를 만든 후 첫 번째 합성 결과의 첫 번째 바이트 대기 시간을 줄입니다.
 - Updated speech recognition models for 19 locales for an average word error rate reduction of 18.6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). 새로운 모델은 받아쓰기, 콜 센터 전사 및 비디오 인덱싱 시나리오를 비롯한 여러 도메인에서 상당한 개선을 가져왔습니다.

**버그 수정**

 - JAVA API에서 대화 전사가 제대로 기다리지 않는 버그를 수정했습니다. 
 - 자마린 [GitHub 문제에](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) 대한 안드로이드 x86 에뮬레이터 수정
 - 누락 추가(받기 | 설정)오디오 구성에 속성 메서드
 - 연결이 실패할 때 audioDataStream을 중지할 수 없는 TTS 버그 수정
 - 지역 없이 끝점을 사용하면 대화 변환기에 USP 오류가 발생합니다.
 - 유니버설 Windows 응용 프로그램의 ID 생성은 이제 적절히 고유한 GUID 알고리즘을 사용합니다. 이전에는 의도치 않게 대규모 상호 작용 집합에 대한 충돌을 발생시키는 스텁 구현으로 기본 설정되었습니다.
 
 **샘플**
 
 - [Unity 마이크 및 푸시 모드 스트리밍과](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) 함께 음성 SDK를 사용하기 위한 Unity 샘플

**기타 변경 사항**

 - [리눅스에 대 한 업데이트 된 OpenSSL 구성 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>음성 SDK 1.9.0: 2020-1월 릴리스

**새로운 기능**

- 다중 기기 대화: 여러 장치를 동일한 음성 또는 텍스트 기반 대화에 연결하고 선택적으로 서로 간에 전송된 메시지를 번역합니다. [이 문서에서](multi-device-conversation.md)자세히 알아봅니다. 
- Android .aar 패키지에 대한 키워드 인식 지원이 추가되었으며 x86 및 x64 맛에 대한 지원이 추가되었습니다. 
- 목표-C: `SendMessage` `SetMessageProperty` 및 메서드개체에 추가됩니다. `Connection` [여기에](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)설명서를 참조하십시오.
- TTS C ++ `std::wstring` API는 이제 합성 텍스트 입력으로 지원되어 SDK에 전달하기 전에 문자열을 문자열로 변환 할 필요가 없습니다. 자세한 내용은 [여기](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)를 참조하세요. 
- C#: [언어 ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) 및 소스 언어 [구성을](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) 사용할 수 있습니다.
- JavaScript: 음성 서비스에서 `Connection` 사용자 지정 메시지를 콜백으로 `receivedServiceMessage`전달하는 기능을 개체에 추가했습니다.
- 자바 스크립트 : `FromHost API` 온 프렘 컨테이너 와 주권 구름과 함께 사용하기 쉽도록 지원을 추가했습니다. [여기에](speech-container-howto.md)설명서를 참조하십시오.
- 자바 스크립트 : `NODE_TLS_REJECT_UNAUTHORIZED` 우리는 지금 [orgads에서](https://github.com/orgads)기여 덕분에 영광 . 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)를 참조하세요.

**호환성이 손상되는 변경**

- `OpenSSL`버전 1.1.1b로 업데이트되었으며 Linux용 음성 SDK 코어 라이브러리에 정적으로 연결됩니다. 시스템의 `OpenSSL` `/usr/lib/ssl` 디렉터리에 받은 편지함이 설치되지 않은 경우 중단될 수 있습니다. 이 문제를 해결하려면 음성 SDK 문서에서 [설명서를](how-to-configure-openssl-linux.md) 확인하십시오.
- 음성 데이터가 2분 이상인 `WordLevelTimingResult.Offset` `WordLevelTimingResults` 경우 `int` `long` 액세스를 허용하도록 C#에 대해 반환되는 데이터 형식을 변경했습니다.
- `PushAudioInputStream`이제 `PullAudioInputStream` wav 헤더 정보를 생성할 때 `AudioStreamFormat`선택적으로 지정한 을 기반으로 음성 서비스로 보냅니다. 이제 고객은 [지원되는 오디오 입력 형식을](how-to-use-audio-input-streams.md)사용해야 합니다. 다른 형식은 최적이 아닌 인식 결과를 얻거나 다른 문제를 일으킬 수 있습니다. 

**버그 수정**

- 위의 `OpenSSL` 주요 변경 사항 아래에서 업데이트를 참조하십시오. Linux 및 Java에서 간헐적인 충돌과 성능 문제(높은 부하에서 잠금 경합)를 모두 수정했습니다. 
- Java: 동시성 시나리오가 높은 개체 폐쇄를 개선했습니다.
- NuGet 패키지를 재구성했습니다. lib 폴더의 `Microsoft.CognitiveServices.Speech.core.dll` 사본 과 `Microsoft.CognitiveServices.Speech.extension.kws.dll` 아래의 세 복사본을 제거하여 NuGet 패키지를 더 작고 빠르게 다운로드할 수 있도록 했으며 일부 C++ 네이티브 앱을 컴파일하는 데 필요한 헤더를 추가했습니다.
- [여기에](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)빠른 시작 샘플을 수정했습니다. 이들은 리눅스, 맥 OS, 윈도우에 "마이크를 찾을 수 없습니다"예외를 표시하지 않고 종료했다.
- [이 샘플과](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)같은 특정 코드 경로에서 긴 음성 인식 결과와 함께 SDK 충돌을 수정했습니다.
- Azure Web App 환경에서 [이 고객 문제를](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)해결하기 위해 SDK 배포 오류가 수정되었습니다.
- [이 고객 문제를](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)해결하기 위해 `<audio>` 다중 `<voice>` 태그 또는 태그를 사용하는 동안 TTS 오류가 수정되었습니다. 
- SDK가 일시 중단된 상태로 복구될 때 TTS 401 오류가 수정되었습니다.
- 자바 스크립트 : [euirim의](https://github.com/euirim)기여덕분에 오디오 데이터의 원형 가져오기를 수정했습니다. 
- JavaScript: 1.7에 추가된 대로 서비스 속성 설정에 대한 지원이 추가되었습니다.
- JavaScript: 연결 오류로 인해 웹소켓이 계속 실패하여 다시 연결시도가 발생할 수 있는 문제를 수정했습니다.

**샘플**

- [여기에](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)안드로이드에 대한 키워드 인식 샘플을 추가했습니다.
- 여기에 서버 시나리오에 대한 TTS 샘플을 [추가했습니다.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)
- [여기에](quickstarts/multi-device-conversation.md)C # 및 C ++에 대한 다중 장치 대화 빠른 시작이 추가되었습니다.

**기타 변경 사항**

- 안드로이드에 최적화 된 SDK 코어 라이브러리 크기.
- 1.9.0 이후의 SDK는 대화 `int` `string` 전사에 대한 음성 서명 버전 필드의 형식과 유형을 모두 지원합니다.

## <a name="speech-sdk-180-2019-november-release"></a>음성 SDK 1.8.0: 2019-11월 릴리스

**새로운 기능**

- 온프레미 컨테이너 및 주권 클라우드와 함께 쉽게 사용할 수 있도록 `FromHost()` API를 추가했습니다.
- 음성 인식을 위한 자동 소스 언어 감지 추가(Java 및 C++)
- Java `SourceLanguageConfig` 및 C++에서 예상되는 소스 언어를 지정하는 데 사용되는 음성 인식용 개체가 추가되었습니다.
- NuGet 및 Unity 패키지를 통해 Windows(UWP), Android 및 iOS에 대한 지원이 추가되었습니다. `KeywordRecognizer`
- 비동기 일괄 처리에서 대화 전사를 수행 하기 위해 원격 대화 자바 API를 추가 했습니다.

**호환성이 손상되는 변경**

- 대화 전사 기능은 네임스페이스로 `Microsoft.CognitiveServices.Speech.Transcription`이동했습니다.
- 대화 전사 메서드의 일부가 새 `Conversation` 클래스로 이동됩니다.
- 32비트(ARMv7 및 x86) iOS용 지원 끊기

**버그 수정**

- 유효한 음성 서비스 `KeywordRecognizer` 구독 키 없이 로컬을 사용 하는 경우 충돌에 대 한 수정

**샘플**

- 자마린 샘플 용`KeywordRecognizer`
- 유니티 샘플`KeywordRecognizer`
- 자동 소스 언어 검색을 위한 C++ 및 Java 샘플.

## <a name="speech-sdk-170-2019-september-release"></a>음성 SDK 1.7.0: 2019-9월 릴리스

**새로운 기능**

- 유니버설 윈도우 플랫폼 (UWP), 안드로이드, 아이폰 OS에 Xamarin에 대한 베타 지원을 추가
- 유니티에 대한 iOS 지원이 추가되었습니다.
- 안드로이드, 아이폰 OS 및 리눅스에 ALaw, Mulaw, FLAC에 대한 입력 지원을 추가 `Compressed`
- 서비스에 `SendMessageAsync` `Connection` 메시지를 보내기 위해 클래스에 추가되었습니다.
- `SetMessageProperty` 메시지의 `Connection` 속성을 설정하기 위해 클래스에 추가되었습니다.
- TTS는 자바 (Jre와 안드로이드), 파이썬, 스위프트, 목표 -C에 대한 바인딩을 추가
- TTS는 맥 OS, iOS 및 안드로이드에 대한 재생 지원을 추가했습니다.
- TTS에 대한 "단어 경계" 정보가 추가되었습니다.

**버그 수정**

- 안 드 로이드에 대 한 유니티 2019에 IL2CPP 빌드 문제를 수정
- wav 파일 입력에서 잘못된 헤더가 잘못 처리되는 문제를 수정했습니다.
- 일부 연결 속성에서 UUD가 고유하지 않하되 문제가 수정되었습니다.
- Swift 바인딩에서 nullability 지정자에 대한 몇 가지 경고가 수정되었습니다(작은 코드 변경이 필요할 수 있음).
- 네트워크 부하로 웹소켓 연결이 정상적으로 닫히지 않는 버그가 수정되었습니다.
- Android에서 사용 하 여 중복 된 노출 아이디를 발생 하는 문제를 해결 했습니다.`DialogServiceConnector`
- 멀티 턴 상호 작용 전반에 걸친 연결 안정성 개선 및 `Canceled` 이벤트가 발생할 때 실패 보고(이벤트를 통한) 보고`DialogServiceConnector`
- `DialogServiceConnector`세션 시작은 이제 활성 상태 `ListenOnceAsync()` 중에 호출할 때를 포함하여 이벤트를 올바르게 제공합니다.`StartKeywordRecognitionAsync()`
- 수신되는 활동과 `DialogServiceConnector` 관련된 충돌 해결

**샘플**

- 자마린의 빠른 시작
- 리눅스 ARM64 정보로 업데이트 된 CPP 퀵 스타트
- iOS 정보로 유니티 퀵스타트 업데이트

## <a name="speech-sdk-160-2019-june-release"></a>음성 SDK 1.6.0: 2019-6월 릴리스

**샘플**

- UWP 및 Unity에서 텍스트 음성 변환을 위한 빠른 시작 샘플
- iOS에서 스위프트에 대한 빠른 시작 샘플
- 음성 & 의도 인식 및 번역을 위한 Unity 샘플
- 에 대한 업데이트된 빠른 시작 샘플`DialogServiceConnector`

**향상된 기능/변경 사항**

- 대화 상자 네임스페이스:
  - `SpeechBotConnector`는 `DialogServiceConnector`로 이름이 변경되었습니다.
  - `BotConfig`는 `DialogServiceConfig`로 이름이 변경되었습니다.
  - `BotConfig::FromChannelSecret()`로 리시핑되었습니다.`DialogServiceConfig::FromBotSecret()`
  - 이름 바꾸기 후에도 모든 기존 직접 회선 음성 클라이언트가 계속 지원됩니다.
- 프록시, 영구 연결을 지원하도록 TTS REST 어댑터 업데이트
- 잘못된 영역이 전달될 때 오류 메시지 개선
- 신속한/목표-C:
  - 향상된 오류 보고: 오류를 발생시킬 수 있는 메서드는 이제 오류 처리를 `NSError` 위해 개체를 노출하는 메서드와 예외를 발생시키는 두 가지 버전으로 표시됩니다. 전자는 스위프트에 노출됩니다. 이 변경은 기존 Swift 코드를 적용해야 합니다.
  - 향상된 이벤트 처리

**버그 수정**

- TTS 수정: `SpeakTextAsync` 오디오 렌더링이 완료될 때까지 기다리지 않고 미래가 반환되는 위치
- 전체 언어 지원을 활성화하려면 C#에서 문자열 마샬링 수정
- 샘플에서 net461 대상 프레임워크를 사용하여 코어 라이브러리를 로드하는 .NET 핵심 앱 문제 수정
- 샘플의 출력 폴더에 네이티브 라이브러리를 배포하는 간헐적인 문제 해결
- 웹 소켓이 안정적으로 닫히는 수정 사항
- 리눅스에서 매우 무거운 부하에서 연결을 여는 동안 가능한 충돌수정
- macOS용 프레임워크 번들에서 누락된 메타데이터 수정
- `pip install --user` Windows에서 문제가 해결되었습니다.

## <a name="speech-sdk-151"></a>음성 SDK 1.5.1

이 릴리스는 버그 수정 릴리스이며 네이티브/관리되는 SDK에만 영향을 미칩니다. SDK의 자바스크립트 버전에는 영향을 주지 않습니다.

**버그 수정**

- 대화 녹음과 함께 사용할 때 구독에서 수정합니다.
- 음성 비서에 대한 키워드 스포팅에서 버그를 수정합니다.

## <a name="speech-sdk-150-2019-may-release"></a>음성 SDK 1.5.0: 2019-5월 릴리스

**새로운 기능**

- 키워드 스포팅(KWS)은 이제 Windows 및 Linux에서 사용할 수 있습니다. KWS 기능은 모든 마이크 유형, 공식 KWS 지원에서 작동할 수 있지만 현재 Azure Kinect DK 하드웨어 또는 음성 장치 SDK에 있는 마이크 어레이로 제한됩니다.
- 구문 힌트 기능은 SDK를 통해 사용할 수 있습니다. 자세한 내용은 [여기](how-to-phrase-lists.md)를 참조하세요.
- 대화 전사 기능은 SDK를 통해 사용할 수 있습니다. [여기를](conversation-transcription-service.md)참조하십시오.
- 직접 회선 음성 채널을 사용하여 음성 도우미에 대한 지원을 추가합니다.

**샘플**

- SDK에서 지원하는 새 기능 또는 새 서비스에 대한 샘플을 추가했습니다.

**향상된 기능/변경 사항**

- 서비스 동작 또는 서비스 결과(예: 욕설 마스킹 등)를 조정하기 위해 다양한 인식기 속성을 추가했습니다.
- 이제 인식기 를 만든 경우에도 표준 구성 속성을 통해 인식기 `FromEndpoint`구성을 구성할 수 있습니다.
- Objective-C: `OutputFormat` 속성이 `SPXSpeechConfiguration`에 추가되었습니다.
- SDK는 이제 데비안 9를 Linux 배포판으로 지원합니다.

**버그 수정**

- 텍스트 음성 변환에서 스피커 리소스가 너무 일찍 소멸되던 문제를 수정했습니다.

## <a name="speech-sdk-142"></a>음성 SDK 1.4.2

이 릴리스는 버그 수정 릴리스이며 네이티브/관리되는 SDK에만 영향을 미칩니다. SDK의 자바스크립트 버전에는 영향을 주지 않습니다.

## <a name="speech-sdk-141"></a>음성 SDK 1.4.1

JavaScript 전용 릴리스입니다. 추가한 기능은 없습니다. 다음과 같이 수정했습니다.

- 웹 팩이 https-프록시 에이전트를 로드하지 못하도록 합니다.

## <a name="speech-sdk-140-2019-april-release"></a>음성 SDK 1.4.0: 2019-4월 릴리스

**새로운 기능**

- 이제 SDK는 텍스트 음성 변환 서비스를 베타 버전으로 지원합니다. 그것은 C ++ 및 C #에서 윈도우와 리눅스 데스크톱에서 지원됩니다. 자세한 내용은 텍스트 [음성 변환 개요를](text-to-speech.md#get-started)확인하십시오.
- SDK는 이제 스트림 입력 파일로 MP3 및 오푸스 / OGG 오디오 파일을 지원합니다. 이 기능은 C ++ 및 C #에서 리눅스에서만 사용할 수 있으며 현재 베타 버전입니다 (자세한 내용은 [여기).](how-to-use-codec-compressed-audio-input-streams.md)
- 자바, .NET 코어, C ++ 및 목표-C에 대한 음성 SDK는 macOS 지원을 얻고있다. macOS에 대한 Objective-C 지원은 현재 베타 버전입니다.
- iOS: iOS용 스피치 SDK(Objective-C)도 CocoaPod으로 게시되었습니다.
- 자바 스크립트: 입력 장치로 비 기본 마이크에 대 한 지원.
- 자바 스크립트 : Node.js에 대한 프록시 지원.

**샘플**

- C++와 macOS에서 Objective-C를 사용하는 음성 SDK를 사용하는 샘플이 추가되었습니다.
- 텍스트 음성 변환 서비스의 사용을 보여 주는 샘플이 추가되었습니다.

**향상된 기능/변경 사항**

- 파이썬: 인식 결과의 추가 속성이 `properties` 이제 속성을 통해 노출됩니다.
- 추가 개발 및 디버그 지원을 위해 SDK 로깅 및 진단 정보를 로그 파일로 리디렉션할 수 있습니다(자세한 내용은 [여기를 참조).](how-to-use-logging.md)
- 자바 스크립트 : 오디오 처리 성능을 향상시킵니다.

**버그 수정**

- Mac/iOS: 음성 서비스에 대한 연결을 설정하지 못할 때 오랜 대기로 이어진 버그가 수정되었습니다.
- 파이썬: 파이썬 콜백의 인수에 대한 오류 처리를 개선합니다.
- 자바 스크립트: 음성에 대한 잘못된 상태 보고가 RequestSession에서 종료되었습니다.

## <a name="speech-sdk-131-2019-february-refresh"></a>음성 SDK 1.3.1: 2019-2월 새로 고침

이 릴리스는 버그 수정 릴리스이며 네이티브/관리되는 SDK에만 영향을 미칩니다. SDK의 자바스크립트 버전에는 영향을 주지 않습니다.

**버그 수정**

- 마이크 입력을 사용할 때 메모리 누수가 수정되었습니다. 스트림 기반 또는 파일 입력은 영향을 받지 않습니다.

## <a name="speech-sdk-130-2019-february-release"></a>음성 SDK 1.3.0: 2019-2월 릴리스

**새로운 기능**

- 음성 SDK는 클래스를 통해 입력 `AudioConfig` 마이크의 선택을 지원합니다. 이렇게 하면 기본이 아닌 마이크에서 음성 서비스로 오디오 데이터를 스트리밍할 수 있습니다. 자세한 내용은 [오디오 입력 장치 선택을](how-to-select-audio-input-devices.md)설명하는 설명서를 참조하십시오. 이 기능은 자바 스크립트에서 아직 사용할 수 없습니다.
- 이제 Speech SDK는 베타 버전의 Unity를 지원합니다. [GitHub 샘플 리포지토리의](https://aka.ms/csspeech/samples)문제 섹션을 통해 피드백을 제공합니다. 이 릴리스에서는 Windows x86 및 x64(데스크톱 또는 유니버설 Windows 플랫폼 애플리케이션)와 Android(ARM32/64, x86)의 Unity를 지원합니다. 자세한 내용은 [Unity 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)을 참조하세요.
- 이전 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 릴리스에서 제공된 파일은 더 이상 필요하지 않습니다. 이제 이 기능이 핵심 SDK에 통합되었습니다.

**샘플**

다음 새 콘텐츠는 [샘플 리포지토리](https://aka.ms/csspeech/samples)에서 사용할 수 있습니다.

- 에 대한 `AudioConfig.FromMicrophoneInput`추가 샘플입니다.
- 의도 인식 및 변환에 대한 추가 Python 샘플
- iOS에서 개체를 `Connection` 사용하기 위한 추가 샘플입니다.
- 오디오 출력을 사용한 변환에 대한 추가 Java 예제
- [일괄 처리 기록 REST API](batch-transcription.md)

**향상된 기능/변경 사항**

- Python
  - 에서 매개 변수 확인 `SpeechConfig`및 오류 메시지가 개선되었습니다.
  - 개체에 대한 `Connection` 지원을 추가합니다.
  - Windows에서 32비트 Python(x86)을 지원합니다.
  - Python용 Speech SDK가 베타를 벗어났습니다.
- iOS
  - SDK는 이제 iOS SDK 버전 12.1용으로 빌드되었습니다.
  - SDK는 이제 iOS 버전 9.2 이상을 지원합니다.
  - 참조 설명서를 개선하고 몇 가지 속성 이름을 수정합니다.
- JavaScript
  - 개체에 대한 `Connection` 지원을 추가합니다.
  - 번들로 묶은 JavaScript의 형식 정의 파일을 추가했습니다.
  - 구문 힌트의 초기 지원 및 구현이 제공됩니다.
  - 인식을 위해 서비스 JSON을 사용하는 속성 컬렉션을 반환합니다.
- 이제 Windows DLL이 버전 리소스를 포함합니다.
- 인식기를 `FromEndpoint` 만드는 경우 끝점 URL에 직접 매개 변수를 추가할 수 있습니다. `FromEndpoint` 표준 구성 속성을 통해 인식기는 구성할 수 없습니다.

**버그 수정**

- 빈 프록시 이름과 프록시 암호가 올바르게 처리되지 않았습니다. 이 릴리스에서는 프록시 이름과 프록시 암호를 빈 문자열로 설정하면 프록시에 연결될 때 제출되지 않습니다.
- SDK에서 만든 SessionId가 항상 일부 언어&nbsp;/ 환경에서 무작위인 것은 아니었습니다. 이 문제를 해결하기 위해 임의 생성기 초기화를 추가했습니다.
- 권한 부여 토큰 처리를 개선했습니다. 권한 부여 토큰을 사용하려면 구독 `SpeechConfig` 키를 지정하고 구독 키를 비워 둡니다. 그런 다음, 평소처럼 인식기를 만듭니다.
- 경우에 따라 `Connection` 개체가 올바르게 해제되지 않았습니다. 이 문제가 해결되었습니다.
- Safari에서도 오디오 출력의 변환 합성을 지원하도록 JavaScript 샘플을 수정했습니다.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

JavaScript 전용 릴리스입니다. 추가한 기능은 없습니다. 다음과 같이 수정했습니다.

- speech.end가 아닌 turn.end에서 스트림 끝을 실행합니다.
- 현재 전송이 실패한 경우 다음 전송을 예약하지 않는 오디오 펌프의 버그를 수정했습니다.
- 인증 토큰을 사용한 연속 인식을 수정했습니다.
- 다른 인식기/엔드포인트의 버그를 수정했습니다.
- 설명서 향상된 기능입니다.

## <a name="speech-sdk-120-2018-december-release"></a>음성 SDK 1.2.0: 2018-12월 릴리스

**새로운 기능**

- Python
  - 이 릴리스에서는 베타 버전의 Python(3.5 이상)이 지원됩니다. 자세한 내용은 여기를 참조하십시오(빠른 시작-python.md).
- JavaScript
  - JavaScript용 Speech SDK가 오픈 소스로 제공됩니다. 소스 코드는 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)에서 사용할 수 있습니다.
  - 이제 Node.js를 지원합니다. 자세한 정보는 [여기](quickstart-js-node.md)에서 확인할 수 있습니다.
  - 오디오 세션에 대한 길이 제한이 제거되었으므로 백그라운드에서 자동으로 다시 연결됩니다.
- `Connection` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다.
  - `Recognizer`에서 개체에 `Connection` 액세스할 수 있습니다. 이 개체를 사용하면 명시적으로 서비스 연결을 시작하고 연결 및 연결 끊기 이벤트를 구독할 수 있습니다.
    (이 기능은 자바 스크립트와 파이썬에서 아직 사용할 수 없습니다.)
- Ubuntu 18.04 지원
- Android
  - APK 생성 중에 ProGuard 지원이 설정되었습니다.

**개선**

- 스레드, 잠금, 뮤텍스 수를 줄이면서 내부 스레드 사용이 개선되었습니다.
- 오류 보고/정보가 개선되었습니다. 경우에 따라 오류 메시지가 전파되지 않았습니다.
- 최신 모듈을 사용하도록 JavaScript의 개발 종속성을 업데이트했습니다.

**버그 수정**

- 에서 형식 불일치로 인해 메모리 `RecognizeAsync`누수 가 수정되었습니다.
- 어떤 경우에는 예외가 유출되었습니다.
- 번역 이벤트 인수에서 메모리 누수가 수정되었습니다.
- 장기 실행 세션에서 다시 연결 시 잠금 문제가 해결되었습니다.
- 실패한 번역에 대한 최종 결과가 누락될 수 있는 문제를 수정했습니다.
- C#: 주 `async` 스레드에서 작업이 대기되지 않은 경우 비동기 작업이 완료되기 전에 인식기(인식기)를 삭제할 수 있었습니다.
- Java: Java VM이 충돌하는 문제를 수정했습니다.
- 목표-C: 고정 열거형 매핑; 인식된 Intent 대신 `RecognizingIntent`에 반환되었습니다.
- 자바 스크립트 : 에서 기본 출력 `SpeechConfig`형식을 '단순'으로 설정합니다.
- 자바 스크립트: 자바 스크립트및 기타 언어의 구성 객체의 속성 간의 불일치를 제거합니다.

**샘플**

- 여러 샘플(예: 번역용 출력 음성 등)을 업데이트하고 수정했습니다.
- Node.js 샘플을 [샘플 리포지토리](https://aka.ms/csspeech/samples)에 추가했습니다.

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**새로운 기능**

- Android x86/x64를 지원합니다.
- 프록시 지원: `SpeechConfig` 이제 개체에서 함수를 호출하여 프록시 정보(호스트 이름, 포트, 사용자 이름 및 암호)를 설정할 수 있습니다. iOS에서는 아직 이 기능을 사용할 수 없습니다.
- 향상된 오류 코드 및 메시지입니다. 인식이 오류를 반환한 경우 이미 `Reason`(취소된 이벤트의) 또는 `CancellationDetails`(인식 결과의) `Error`로 설정되어 있습니다. 취소 이벤트에는 이제 두 개의 추가 멤버 `ErrorCode` 및 `ErrorDetails`가 포함됩니다. 서버에서 보고된 오류를 사용하여 추가 오류 정보를 반환하는 경우 해당 서버를 새 멤버에서 사용할 수 있습니다.

**개선**

- 인식기 구성에서 추가 확인을 추가하고, 추가 오류 메시지를 추가했습니다.
- 오디오 파일의 중간에 긴 무음 시간 처리가 향상되었습니다.
- NuGet 패키지: .NET Framework의 경우 프로젝트는 AnyCPU 구성을 통한 빌드를 방지합니다.

**버그 수정**

- 인식기에서 발견된 몇 가지 예외를 수정했습니다. 또한 예외가 적발되어 이벤트로 `Canceled` 변환됩니다.
- 속성 관리에서 메모리 누수를 해결합니다.
- 오디오 입력 파일이 인식기 작동을 중지할 수 있는 버그가 수정되었습니다.
- 세션 중지 이벤트 후 이벤트를 수신할 수 있는 버그가 수정되었습니다.
- 스레딩에서 일부 경합 상태가 수정되었습니다.
- 결국 충돌을 일으킬 수 있는 iOS 호환성 문제가 해결되었습니다.
- Android 마이크 지원의 안정성 향상.
- JavaScript의 인식기가 인식 언어를 무시하는 버그가 수정되었습니다.
- 자바스크립트에서 (경우에 `EndpointId` 따라) 설정이 차단되는 버그를 수정했습니다.
- 자바스크립트의 애드니어드에서 매개 변수 `AddIntent` 순서가 변경되고 누락된 자바스크립트 서명이 추가되었습니다.

**샘플**

- [샘플 리포지토리에서](https://aka.ms/csspeech/samples)스트림 사용량을 끌어오기 및 푸시하기 위한 C++ 및 C# 샘플을 추가했습니다.

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

안정성 향상 및 버그 수정:

- 삭제 중인 인식기의 경합으로 인한 잠재적인 심각한 오류가 수정되었습니다.
- 속성이 설정되지 않은 경우의 잠재적인 심각한 오류가 수정되었습니다.
- 추가 오류 및 매개 변수 검사가 추가되었습니다.
- Objective-C: NSString에서 이름 재정의로 인한 잠재적인 심각한 오류가 수정되었습니다.
- Objectice-C: API의 표시 유형이 조정되었습니다.
- JavaScript: 관련 이벤트 및 해당 페이로드가 수정되었습니다.
- 설명서 향상된 기능입니다.

[샘플 리포지토리](https://aka.ms/csspeech/samples)에서 JavaScript에 대한 새로운 샘플이 추가되었습니다.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018년 9월 release

**새로운 기능**

- iOS에서 Objective-C를 지원합니다. [iOS용 Objective-C 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)을 확인하세요.
- 브라우저에서 JavaScript를 지원합니다. [JavaScript 빠른 시작](quickstart-js-browser.md)을 확인하세요.

**호환성이 손상되는 변경**

- 이 릴리스에서는 여러 가지 주요 변경 사항이 도입되었습니다.
  자세한 내용은 [이 페이지를](https://aka.ms/csspeech/breakingchanges_1_0_0) 참조하십시오.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018년 8월 릴리스

**새로운 기능**

- Speech SDK를 사용하여 빌드된 UWP 앱은 이제 WACK(Windows 앱 인증 키트)를 전달할 수 있습니다.
  [UWP 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)을 확인하세요.
- Linux(Ubuntu 16.04 x64)에서 .NET 표준 2.0을 지원합니다.
- 실험: Windows(64비트) 및 Linux(Ubuntu 16.04 x64)에서 Java 8을 지원합니다.
  [Java Runtime Environment 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)을 확인하세요.

**기능 변경**

- 연결 오류에 대한 추가 오류 세부 정보를 표시합니다.

**호환성이 손상되는 변경**

- Java(Android)에서, `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 함수는 더 이상 경로 매개 변수를 요구하지 않습니다. 이제 지원되는 모든 플랫폼에서 경로가 자동으로 검색됩니다.
- Java 및 C#에서 `EndpointUrl` 속성의 get-accessor가 제거되었습니다.

**버그 수정**

- Java에서, 번역 인식기의 오디오 합성 결과가 구현되었습니다.
- 비활성 스레드 및 사용되지 않는 열린 소켓 수가 증가하는 버그가 수정되었습니다.
- 오래 실행되는 인식이 전송 중에 종료될 수 있는 문제가 해결되었습니다.
- 인식기 종료에서 발생하는 경합 조건을 수정했습니다.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018년 7월 릴리스

**새로운 기능**

- Android 플랫폼(API 23: Android 6.0 Marshmallow 이상)을 지원합니다. [Android 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)을 확인하세요.
- Windows에서 .NET Standard 2.0을 지원합니다. [.NET Core 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)을 확인하세요.
- 실험: Windows(버전 1709 이상)에서 UWP를 지원합니다.
  - [UWP 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)을 확인하세요.
  - 참고: Speech SDK를 사용하여 빌드된 UWP 앱은 아직 WACK(Windows 앱 인증 키트)를 제공하지 않습니다.
- 자동 다시 연결을 통해 장기 실행 인식을 지원합니다.

**기능 변경 내용**

- `StartContinuousRecognitionAsync()`에서 장기 실행 인식을 지원합니다.
- 인식 결과에 더 많은 필드가 포함됩니다. 인식된 텍스트의 오디오 시작 및 지속 시간의 오프셋(두 가지 모두 틱 단위) 및 인식 상태를 나타내는 추가 값(예: `InitialSilenceTimeout`, `InitialBabbleTimeout`)입니다.
- 팩터리 인스턴스를 만들기 위한 AuthorizationToken을 지원합니다.

**호환성이 손상되는 변경**

- 인식 이벤트: `NoMatch` 이벤트 유형이 `Error` 이벤트에 병합되었습니다.
- C#의 SpeechOutputFormat이름이 C++와 정렬된 상태를 유지하도록 `OutputFormat` 이름이 바뀌었습니다.
- `AudioInputStream` 인터페이스의 일부 메서드 반환 형식이 다음과 같이 약간 변경되었습니다.
  - Java에서 `read` 메서드는 이제 `int` 대신 `long`을 반환합니다.
  - C#에서 `Read` 메서드는 이제 `int` 대신 `uint`을 반환합니다.
  - C++에서 `Read` 및 `GetFormat` 메서드는 이제 `int` 대신 `size_t`를 반환합니다.
- C++: 오디오 입력 스트림의 인스턴스가 이제 `shared_ptr`로만 전달될 수 있습니다.

**버그 수정**

- `RecognizeAsync()` 시간이 초과될 때 결과의 잘못된 반환 값이 수정되었습니다.
- Windows에서 미디어 파운데이션 라이브러리에 대한 종속성이 제거되었습니다. SDK에 이제 Core Audio API가 사용됩니다.
- 설명서 수정 사항: 지원되는 지역을 설명하는 [지역](regions.md) 페이지가 추가되었습니다.

**알려진 문제**

- Android용 Speech SDK에서는 번역에 대한 음성 합성 결과를 보고하지 않습니다. 이 문제는 다음 릴리스에서 해결될 예정입니다.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018년 6월 릴리스

**기능 변경 내용**

- AudioInputStream

  이제 인식기가 스트림을 오디오 원본으로 사용할 수 있습니다. 자세한 내용은 관련 [방법 가이드](how-to-use-audio-input-streams.md)를 참조하세요.

- 자세한 출력 형식

  `SpeechRecognizer`를 만들 때 `Detailed` 또는 `Simple` 출력 형식을 요청할 수 있습니다. `DetailedSpeechRecognitionResult`에는 신뢰도 점수, 인식된 텍스트, 원시 어휘 형식, 정규화된 형식 및 마스킹된 욕설이 포함된 정규화된 형식이 포함됩니다.

**주요 변경**

- C#의 `SpeechRecognitionResult.RecognizedText`가 `SpeechRecognitionResult.Text`로 변경되었습니다.

**버그 수정**

- 종료하는 동안 USP 계층에서 발생할 수 있는 콜백 문제가 해결되었습니다.
- 인식기가 오디오 입력 파일을 사용한 경우, 필요한 것보다 더 긴 파일 핸들을 사용하고 있었습니다.
- 메시지 펌프와 인식기 간에 여러 가지 교착 상태가 제거되었습니다.
- 서비스 응답 시간이 초과되면 `NoMatch` 결과를 실행합니다.
- Windows의 미디어 파운데이션 라이브러리는 지연 로드됩니다. 이 라이브러리는 마이크 입력에만 필요합니다.
- 오디오 데이터의 업로드 속도는 원본 오디오 속도의 두 배 정도로 제한됩니다.
- Windows에서 C# .NET 어셈블리에 이제 강력한 이름이 지정됩니다.
- 설명서 수정: `Region`은 인식기를 만드는 데 필요한 정보입니다.

추가 샘플이 추가되었고 지속적으로 업데이트되고 있습니다. 최신 샘플 집합은 [Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018년 5월 릴리스

이 릴리스는 Cognitive Services Speech SDK의 첫 번째 공개 미리 보기 릴리스입니다.
