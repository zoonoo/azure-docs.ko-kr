---
title: 음성 장치 SDK-음성 서비스 정보
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK를 시작합니다. Speech Service는 다양한 디바이스 및 오디오 원본에서 작동합니다. Speech Devices SDK는 특별히 만들어진 마이크 배열 개발 키트와 쌍으로 연결된 미리 조정된 라이브러리입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 31d1a60cc6c1316dcfccb4f9ba2571708fddd991
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072390"
---
# <a name="about-the-speech-devices-sdk"></a>Speech Devices SDK 정보

[음성 서비스](overview.md) 는 다양 한 장치 및 오디오 소스에서 작동 합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. Speech Devices SDK는 특별히 만들어진 마이크 배열 개발 키트와 쌍으로 연결된 미리 조정된 라이브러리입니다.

Speech Devices SDK는 다음을 수행하는 데 유용합니다.

- 새 음성 시나리오를 신속하게 테스트합니다.
- 클라우드 기반 음성 서비스를 장치에 더 쉽게 통합 하세요.
- 고객을 위한 탁월한 사용자 환경을 만듭니다.

Speech Devices SDK는 [Speech SDK](speech-sdk.md)를 사용합니다. 음성 SDK를 사용 하 여 고급 오디오 처리 알고리즘을 통해 처리 되는 오디오를 장치의 마이크 배열에서 [음성 서비스로](overview.md)보냅니다. 다중 채널 오디오를 사용하여 잡음 제거, 반향 제거, 빔 형성 및 탈반향을 통해 더 정확한 원거리 [음성 인식](speech-to-text.md)을 제공합니다.

또한 음성 장치 SDK를 사용 하 여 사용자 고유의 사용자 [지정 키워드](speech-devices-sdk-create-kws.md) 를 포함 하는 주변 장치를 빌드할 수 있으므로 사용자 상호 작용을 시작 하는 큐는 사용자의 브랜드에 따라 고유 합니다.

음성 장치 SDK를 사용 하면 [음성 도우미](https://aka.ms/bots/speech/va), 드라이브 및 주문 시스템, [대화](conversation-transcription-service.md)기록, 스마트 스피커 등의 다양 한 음성 사용 시나리오를 활용할 수 있습니다. 예를 들어, 사용자에게 텍스트로 응답하고, 기본 또는 [사용자 지정 음성](how-to-customize-voice-font.md)으로 응답하고, 검색 결과를 제공하고, 다른 언어로 [번역](speech-translation.md)할 수 있습니다. 여러분이 무엇을 빌드할지 기대가 됩니다!

## <a name="get-the-speech-devices-sdk"></a>음성 디바이스 SDK 가져오기

### <a name="android"></a>Android

Android 장치의 경우 [Android Speech DEVICES SDK](https://aka.ms/sdsdk-download-android)의 최신 버전을 다운로드 합니다.

### <a name="windows"></a>Windows

Windows의 경우 샘플 응용 프로그램은 플랫폼 간 Java 응용 프로그램으로 제공 됩니다. 최신 버전의 [JRE Speech 장치 SDK](https://aka.ms/sdsdk-download-JRE)를 다운로드 합니다.
애플리케이션은 Speech SDK 패키지와 64비트 Windows 기반의 Eclipse Java IDE(v4)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

### <a name="linux"></a>Linux

Linux의 경우 샘플 응용 프로그램은 플랫폼 간 Java 응용 프로그램으로 제공 됩니다. 최신 버전의 [JRE Speech 장치 SDK](https://aka.ms/sdsdk-download-JRE)를 다운로드 합니다.
애플리케이션은 Speech SDK 패키지와 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9) 기반의 Eclipse Java IDE(v4)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 장치 선택](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [무료 음성 서비스 구독 키 받기](get-started.md)
