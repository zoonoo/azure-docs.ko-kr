---
title: Speech Service SDK 설명서
titlesuffix: Azure Cognitive Services
description: 릴리스 정보 - 가장 최근 릴리스에서 변경된 내용
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: b5e4d239121b2449a45dfce826c99765f1c3f4be
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471141"
---
# <a name="release-notes"></a>릴리스 정보

## <a name="speech-service-sdk-101"></a>Speech Service SDK 1.0.1

안정성 향상 및 버그 수정:

* 삭제 중인 인식기의 경합으로 인한 잠재적인 심각한 오류가 수정되었습니다.
* 속성이 설정되지 않은 경우의 잠재적인 심각한 오류가 수정되었습니다.
* 추가 오류 및 매개 변수 검사가 추가되었습니다.
* Objective-C: NSString에서 이름 재정의로 인한 잠재적인 심각한 오류가 수정되었습니다.
* Objectice-C: API의 표시 유형이 조정되었습니다.
* JavaScript: 관련 이벤트 및 해당 페이로드가 수정되었습니다.
* 설명서 향상된 기능입니다.

[샘플 리포지토리](https://aka.ms/csspeech/samples)에서 JavaScript에 대한 새로운 샘플이 추가되었습니다.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018년 9월 release

**새로운 기능**

* iOS에서 Objective-C를 지원합니다. [iOS용 Objective-C 빠른 시작](quickstart-objectivec-ios.md)을 확인하세요.
* 브라우저에서 JavaScript를 지원합니다. [JavaScript 빠른 시작](quickstart-js-browser.md)을 확인하세요.

**주요 변경 내용**

* 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다.
  자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018년 8월 릴리스

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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018년 7월 릴리스

**새로운 기능**

* Android 플랫폼(API 23: Android 6.0 Marshmallow 이상)을 지원합니다. [Android 빠른 시작](quickstart-java-android.md)을 확인하세요.
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

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018년 6월 릴리스

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018년 5월 릴리스

이 릴리스는 Cognitive Services Speech SDK의 첫 번째 공개 미리 보기 릴리스입니다.
