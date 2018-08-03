---
title: Cognitive Services Speech SDK 설명서 | Microsoft Docs
description: 릴리스 정보 - 가장 최근 릴리스에서 변경된 내용
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: wolfma
ms.openlocfilehash: 50a8c183bd7f2711847ce6d0acade4cb498ef2fc
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116098"
---
# <a name="release-notes"></a>릴리스 정보

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018년 7월 릴리스

**새로운 기능**

* Android 플랫폼(API 23: Android 6.0 Marshmallow 이상)을 지원합니다.
  [Android 빠른 시작](quickstart-java-android.md)을 확인하세요.
* Windows에서 .NET Standard 2.0을 지원합니다.
  [.NET Core 빠른 시작](quickstart-csharp-dotnetcore-windows.md)을 확인하세요.
* 실험 중: Windows에서 UWP(버전 1709 이상) 지원
  * [UWP 빠른 시작](quickstart-csharp-uwp.md)을 확인하세요.
  * 참고: Speech SDK를 사용하여 빌드된 UWP 앱은 아직 WACK(Windows 앱 인증 키트)를 제공하지 않습니다.
* 자동 다시 연결을 사용하여 장기 실행 인식을 지원합니다.

**기능 변경 내용**

* `StartContinuousRecognitionAsync()`는 장기 실행 인식을 지원합니다.
* 인식 결과에 인식된 텍스트의 오디오 시작에서의 오프셋과 기간(둘 다 틱 단위), 인식 상태(예: `InitialSilenceTimeout`, `InitialBabbleTimeout`)를 나타내는 추가 값 등의 추가 필드가 포함됩니다.
* 팩터리 인스턴스를 만들기 위한 AuthorizationToken을 지원합니다.

**주요 변경 내용**

* 인식 이벤트: NoMatch 이벤트 유형이 Error 이벤트에 병합됩니다.
* C#의 SpeechOutputFormat이 C++에 맞게 OutputFormat으로 바뀌었습니다.
* `AudioInputStream` 인터페이스의 일부 메서드 반환 형식이 다음과 같이 약간 변경되었습니다.
   * Java에서 `read` 메서드는 이제 `int` 대신 `long`을 반환합니다.
   * C#에서 `Read` 메서드는 이제 `int` 대신 `uint`을 반환합니다.
   * C++에서 `Read` 및 `GetFormat` 메서드는 이제 `int` 대신 `size_t`를 반환합니다.
* C++: 오디오 입력 스트림의 인스턴스가 이제 `shared_ptr`로서만 전달될 수 있습니다.

**버그 수정**

* `RecognizeAsync()` 시간이 초과될 때 결과에 잘못 표시되던 반환 값이 수정되었습니다.
* Windows에서 미디어 파운데이션 라이브러리에 대한 종속성이 제거되었습니다. 이제 SDK는 핵심 오디오 API를 사용합니다.
* 설명서 수정 사항: 지원되는 지역을 설명하는 지역 페이지가 추가되었습니다.

**알려진 문제**

* Android용 Speech SDK에서는 번역에 대한 음성 합성 결과를 보고하지 않습니다.
  이 문제는 다음 릴리스에서 해결될 것입니다.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018년 6월 릴리스

**기능 변경 내용**

- AudioInputStream

  이제 인식기가 오디오 원본으로 스트림을 사용할 수 있습니다. 자세한 내용은 관련 [방법 가이드](how-to-use-audio-input-streams.md)를 참조하세요.

- 자세한 출력 형식

  `SpeechRecognizer`를 만드는 동안 `Detailed` 또는 `Simple` 출력 형식을 요청할 수 있습니다. `DetailedSpeechRecognitionResult`에는 신뢰도 점수, 인식된 텍스트, 원시 어휘 형식, 정규화된 형식 및 마스킹된 욕설이 포함된 정규화된 형식이 포함됩니다.

**호환성이 손상되는 변경**

- C#에서는 `SpeechRecognitionResult.RecognizedText`에서 `SpeechRecognitionResult.Text`로 변경합니다.

**버그 수정**

- 종료하는 동안 USP 계층에서 가능한 콜백 문제를 수정합니다.

- 인식기가 오디오 입력 파일을 사용한 경우, 필요한 것보다 더 긴 파일 핸들을 사용하고 있었습니다.

- 메시지 펌프와 인식기 간에 여러 교착 상태를 제거했습니다.

- 서비스 응답 시간이 초과되면 `NoMatch` 결과를 실행합니다.

- Windows의 미디어 파운데이션 라이브러리는 지연 로드됩니다. 이 라이브러리는 마이크 입력에만 필요합니다.

- 오디오 데이터의 업로드 속도는 원본 오디오 속도의 두 배 정도로 제한됩니다.

- 이제 Windows에서 C# .NET 어셈블리에는 강력한 이름이 지정됩니다.

- 설명서 수정: `Region`은 인식기를 만드는 데 필요한 정보입니다.

추가 샘플이 추가되었고 지속적으로 업데이트되고 있습니다. 샘플의 최신 집합은 [Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018년 5월 릴리스

Cognitive Services Speech SDK의 첫 번째 공개 미리 보기 릴리스입니다.
