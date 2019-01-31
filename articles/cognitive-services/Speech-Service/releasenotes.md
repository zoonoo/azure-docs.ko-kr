---
title: 릴리스 정보 - Speech Services
titlesuffix: Azure Cognitive Services
description: Azure 음성 서비스의 기능 릴리스, 향상된 기능, 버그 수정 및 알려진 문제에 대한 실행 로그를 참조하세요.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 80cf0d9970eb7052eeb19fb297176453864202cf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205698"
---
# <a name="release-notes"></a>릴리스 정보

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018년 12월 릴리스

**새로운 기능**

* Python
  * 이 릴리스에서는 베타 버전의 Python(3.5 이상)이 지원됩니다. 자세한 내용은 [여기를 참조](quickstart-python.md)하세요.
* JavaScript
  * JavaScript용 Speech SDK가 오픈 소스로 제공됩니다. 소스 코드는 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)에서 사용할 수 있습니다.
  * 이제 Node.js를 지원합니다. 자세한 정보는 [여기](quickstart-js-node.md)에서 확인할 수 있습니다.
  * 오디오 세션에 대한 길이 제한이 제거되었으므로 백그라운드에서 자동으로 다시 연결됩니다.
* 연결 개체
  * 인식기에서 연결 개체에 액세스할 수 있습니다. 이 개체를 사용하면 명시적으로 서비스 연결을 시작하고 연결 및 연결 끊기 이벤트를 구독할 수 있습니다.
    (아직 JavaScript 및 Python에서는 사용 불가)
* Ubuntu 18.04 지원
* Android
  * APK 생성 중에 ProGuard 지원이 설정되었습니다.

**향상된 기능**

* 스레드, 잠금, 뮤텍스 수를 줄이면서 내부 스레드 사용이 개선되었습니다.
* 오류 보고/정보가 개선되었습니다. 여러 사례에서 오류 메시지가 완전히 전파되지 않았습니다.
* 최신 모듈을 사용하도록 JavaScript의 개발 종속성을 업데이트했습니다.

**버그 수정**

* RecognizeAsync의 형식 불일치로 인한 메모리 누수가 수정되었습니다.
* 어떤 경우에는 예외가 유출되었습니다.
* 번역 이벤트 인수에서 메모리 누수가 수정되었습니다.
* 장기 실행 세션에서 다시 연결 시 잠금 문제가 해결되었습니다.
* 번역 실패로 인해 최종 결과가 누락될 수 있는 문제가 해결되었습니다.
* C#: 주 스레드에서 비동기 작업을 기다리지 않는 경우 비동기 작업이 완료되기 전에 인식기가 삭제될 수 있었습니다.
* Java: Java VM 충돌 문제가 해결되었습니다.
* Objective-C: 고정 열거형 매핑. RecognizingIntent 대신 RecognizedIntent가 반환되었습니다.
* JavaScript: SpeechConfig에서 기본 출력 형식을 'simple'로 설정합니다.
* JavaScript: JavaScript 및 다른 언어에서 구성 개체 속성 간 불일치를 제거합니다.

**샘플**

* 여러 샘플을 업데이트하고 수정했습니다(예: 번역을 위한 출력 음성 등).
* Node.js 샘플을 [샘플 리포지토리](https://aka.ms/csspeech/samples)에 추가했습니다.

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**새로운 기능**

* Android x86/x64를 지원합니다.
* 프록시 지원: 이제 SpeechConfig 개체에서 함수를 호출하여 프록시 정보(호스트 이름, 포트, 사용자 이름 및 암호)를 설정할 수 있습니다. iOS에서는 아직 이 기능을 사용할 수 없습니다.
* 향상된 오류 코드 및 메시지입니다. 인식이 오류를 반환한 경우 이미 `Reason`(취소된 이벤트의) 또는 `CancellationDetails`(인식 결과의) `Error`로 설정되어 있습니다. 취소 이벤트에는 이제 두 개의 추가 멤버 `ErrorCode` 및 `ErrorDetails`가 포함됩니다. 서버에서 보고된 오류를 사용하여 추가 오류 정보를 반환하는 경우 해당 서버를 새 멤버에서 사용할 수 있습니다.

**향상된 기능**

* 인식기 구성에서 추가 확인을 추가하고, 추가 오류 메시지를 추가했습니다.
* 오디오 파일의 중간에 긴 무음 시간 처리가 향상되었습니다.
* NuGet 패키지: .NET Framework의 경우 프로젝트는 AnyCPU 구성을 통한 빌드를 방지합니다.

**버그 수정**

* 인식기에서 발견된 몇 가지 예외를 수정했습니다. 또한 예외가 catch되고 취소된 이벤트로 변환됩니다.
* 속성 관리에서 메모리 누수를 해결합니다.
* 오디오 입력 파일이 인식기 작동을 중지할 수 있는 버그가 수정되었습니다.
* 세션 중지 이벤트 후 이벤트를 수신할 수 있는 버그가 수정되었습니다.
* 스레딩에서 일부 경합 상태가 수정되었습니다.
* 결국 충돌을 일으킬 수 있는 iOS 호환성 문제가 해결되었습니다.
* Android 마이크 지원의 안정성 향상.
* JavaScript의 인식기가 인식 언어를 무시하는 버그가 수정되었습니다.
* Javascript에서 EndpointId(일부 경우) 설정을 방해하는 버그가 수정되었습니다.
* JavaScript의 AddIntent에서 매개 변수 순서를 변경하고, 누락된 AddIntent JavaScript 서명을 추가했습니다.

**샘플**

* [샘플 리포지토리](https://aka.ms/csspeech/samples)에 끌어오기 및 밀어넣기 스트리밍 사용에 대한 C++ 및 C# 샘플을 추가했습니다.

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

안정성 향상 및 버그 수정:

* 삭제 중인 인식기의 경합으로 인한 잠재적인 심각한 오류가 수정되었습니다.
* 속성이 설정되지 않은 경우의 잠재적인 심각한 오류가 수정되었습니다.
* 추가 오류 및 매개 변수 검사가 추가되었습니다.
* Objective-C: NSString에서 이름 재정의로 인한 잠재적인 심각한 오류가 수정되었습니다.
* Objective-C: API의 표시 유형이 조정되었습니다.
* JavaScript: 관련 이벤트 및 해당 페이로드가 수정되었습니다.
* 설명서 향상된 기능입니다.

[샘플 리포지토리](https://aka.ms/csspeech/samples)에서 JavaScript에 대한 새로운 샘플이 추가되었습니다.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018-9월 릴리스

**새로운 기능**

* iOS에서 Objective-C를 지원합니다. [iOS용 Objective-C 빠른 시작](quickstart-objectivec-ios.md)을 확인하세요.
* 브라우저에서 JavaScript를 지원합니다. [JavaScript 빠른 시작](quickstart-js-browser.md)을 확인하세요.

**주요 변경 내용**

* 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다.
  자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-8월 릴리스

**새로운 기능**

* Speech SDK를 사용하여 빌드된 UWP 앱은 이제 WACK(Windows 앱 인증 키트)를 전달할 수 있습니다.
  [UWP 빠른 시작](quickstart-csharp-uwp.md)을 확인하세요.
* Linux(Ubuntu 16.04 x64)에서 .NET 표준 2.0을 지원합니다.
* 실험: Windows(64비트) 및 Linux(Ubuntu 16.04 x64)에서 Java 8을 지원합니다.
  [Java Runtime Environment 빠른 시작](quickstart-java-jre.md)을 확인하세요.

**기능 변경**

* 연결 오류에 대한 추가 오류 세부 정보를 표시합니다.

**주요 변경 내용**

* Java(Android)에서, `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 함수는 더 이상 경로 매개 변수를 요구하지 않습니다. 이제 지원되는 모든 플랫폼에서 경로가 자동으로 검색됩니다.
* Java 및 C#에서 `EndpointUrl` 속성의 get-accessor가 제거되었습니다.

**버그 수정**

* Java에서, 번역 인식기의 오디오 합성 결과가 구현되었습니다.
* 비활성 스레드 및 사용되지 않는 열린 소켓 수가 증가하는 버그가 수정되었습니다.
* 오래 실행되는 인식이 전송 중에 종료될 수 있는 문제가 해결되었습니다.
* 인식기 종료에서 발생하는 경합 조건을 수정했습니다.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-7월 릴리스

**새로운 기능**

* Android 플랫폼 지원(API 23: Android 6.0 Marshmallow 이상). [Android 빠른 시작](quickstart-java-android.md)을 확인하세요.
* Windows에서 .NET Standard 2.0을 지원합니다. [.NET Core 빠른 시작](quickstart-csharp-dotnetcore-windows.md)을 확인하세요.
* 실험: Windows(버전 1709 이상)에서 UWP를 지원합니다.
  * [UWP 빠른 시작](quickstart-csharp-uwp.md)을 확인하세요.
  * 참고: Speech SDK를 사용하여 빌드된 UWP 앱은 아직 WACK(Windows 앱 인증 키트)를 제공하지 않습니다.
* 자동 다시 연결을 통해 장기 실행 인식을 지원합니다.

**기능 변경 내용**

* `StartContinuousRecognitionAsync()`에서 장기 실행 인식을 지원합니다.
* 인식 결과에 더 많은 필드가 포함됩니다. 인식된 텍스트의 오디오 시작 및 지속 시간의 오프셋(두 가지 모두 틱 단위) 및 인식 상태를 나타내는 추가 값(예: `InitialSilenceTimeout`, `InitialBabbleTimeout`)입니다.
* 팩터리 인스턴스를 만들기 위한 AuthorizationToken을 지원합니다.

**주요 변경 내용**

* 인식 이벤트: NoMatch 이벤트 유형이 Error 이벤트에 병합되었습니다.
* C#의 SpeechOutputFormat이 C++에 맞게 OutputFormat으로 이름이 바뀌었습니다.
* `AudioInputStream` 인터페이스의 일부 메서드 반환 형식이 다음과 같이 약간 변경되었습니다.
   * Java에서 `read` 메서드는 이제 `int` 대신 `long`을 반환합니다.
   * C#에서 `Read` 메서드는 이제 `int` 대신 `uint`을 반환합니다.
   * C++에서 `Read` 및 `GetFormat` 메서드는 이제 `int` 대신 `size_t`를 반환합니다.
* C++: 오디오 입력 스트림의 인스턴스가 이제 `shared_ptr`로만 전달될 수 있습니다.

**버그 수정**

* `RecognizeAsync()` 시간이 초과될 때 결과의 잘못된 반환 값이 수정되었습니다.
* Windows에서 미디어 파운데이션 라이브러리에 대한 종속성이 제거되었습니다. SDK에 이제 Core Audio API가 사용됩니다.
* 설명서 수정 사항: 지원되는 지역을 설명하는 [지역](regions.md) 페이지가 추가되었습니다.

**알려진 문제**

* Android용 Speech SDK에서는 번역에 대한 음성 합성 결과를 보고하지 않습니다. 이 문제는 다음 릴리스에서 해결될 예정입니다.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-6월 릴리스

**기능 변경 내용**

- AudioInputStream

  이제 인식기가 스트림을 오디오 원본으로 사용할 수 있습니다. 자세한 내용은 관련 [방법 가이드](how-to-use-audio-input-streams.md)를 참조하세요.

- 자세한 출력 형식

  `SpeechRecognizer`를 만들 때 `Detailed` 또는 `Simple` 출력 형식을 요청할 수 있습니다. `DetailedSpeechRecognitionResult`에는 신뢰도 점수, 인식된 텍스트, 원시 어휘 형식, 정규화된 형식 및 마스킹된 욕설이 포함된 정규화된 형식이 포함됩니다.

**호환성이 손상되는 변경**

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-5월 릴리스

이 릴리스는 Cognitive Services Speech SDK의 첫 번째 공개 미리 보기 릴리스입니다.
