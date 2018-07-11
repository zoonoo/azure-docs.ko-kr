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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35383430"
---
# <a name="release-notes"></a>릴리스 정보

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
