---
title: 음성 비서 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 소프트웨어 개발 키트(SDK)를 사용하는 음성 비서의 기능, 기능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369899"
---
# <a name="what-is-a-voice-assistant"></a>음성 도우미란?

음성 도우미는 음성 서비스를 사용하여 개발자가 응용 프로그램 및 환경을 위해 인간과 같은 자연스러운 대화형 인터페이스를 만들 수 있도록 지원합니다.

음성 도우미 서비스는 (1) 봇 프레임워크의 직접 회선 음성 채널 또는 (2) 작업 완료를 위한 통합 사용자 지정 명령(미리 보기) 서비스를 사용하는 장치와 보조 구현 간의 빠르고 안정적인 상호 작용을 제공합니다.

응용 프로그램은 음성 소프트웨어 개발 키트(SDK)를 사용하여 음성 도우미 서비스에 연결합니다.

   ![음성 보조 오케스트레이션 서비스 흐름의 개념 다이어그램](media/voice-assistants/overview.png "음성 도우미 흐름")

## <a name="choosing-an-assistant-solution"></a>보조 솔루션 선택

음성 도우미를 만드는 첫 번째 단계는 음성 도우미를 만드는 것입니다. 음성 서비스는 보조 상호 작용을 만들기 위한 여러 보완 솔루션을 제공합니다. Bot Framework의 [직접 라인 음성](direct-line-speech.md) 채널이 제공하는 유연성과 다기능성을 원하든, 간단한 시나리오에 대한 사용자 지정 [명령(미리 보기)의](custom-commands.md) 단순성을 원하든, 올바른 도구를 선택하면 시작할 수 있습니다.

| 원하는 경우... | 그런 다음 고려하십시오... | 예를 들어... |
|-------------------|------------------|----------------|
|강력한 기술 통합 및 전체 배포 제어를 통해 개방형 대화 | 봇 프레임워크의 [직접 라인 음성](direct-line-speech.md) 채널 | <ul><li>"시애틀에 가야 해요"</li><li>"어떤 종류의 피자를 주문할 수 있나요?"</li></ul>
|간소화된 저작 및 호스팅을 통해 명령 및 제어 또는 작업 지향 대화 | [사용자 지정 명령(미리 보기)](custom-commands.md) | <ul><li>"오버헤드 라이트 켜기"</li><li>"5도 더 따뜻하게"</ul>

어시스턴트가 무엇을 처리하길 원하는지 아직 확실하지 않은 경우 [직접 라인 스피치를](direct-line-speech.md) 최상의 기본 선택으로 선택하는 것이 좋습니다. [가상 도우미 솔루션 및 엔터프라이즈 템플릿](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) 및 [QnA Maker 서비스와](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) 같은 다양한 도구 및 제작 보조 도구와 통합하여 공통 패턴을 구축하고 기존 지식 소스를 사용합니다.

[사용자 지정 명령(미리 보기)은](custom-commands.md) 자연어 명령 및 제어 시나리오에 맞게 특별히 조정된 간소화된 작성 및 호스팅 환경을 제공합니다.

   ![보조 솔루션 비교](media/voice-assistants/assistant-solution-comparison.png "보조 솔루션 비교")

## <a name="core-features"></a>핵심 기능

[직접 회선 음성](direct-line-speech.md) 또는 [사용자 지정 명령(미리 보기)을](custom-commands.md) 선택하여 도우미 상호 작용을 만들때 다양한 사용자 지정 기능을 사용하여 어시스턴트를 브랜드, 제품 및 개성에 맞게 사용자 지정할 수 있습니다.

| Category | 기능 |
|----------|----------|
|[Custom 키워드](speech-devices-sdk-create-kws.md) | 사용자는 "Hey Contoso"와 같은 사용자 지정 키워드로 도우미와 대화를 시작할 수 있습니다. 앱은 여기서 생성할 수 있는 사용자 지정 키워드로 구성할 수 있는 Speech SDK의 사용자 지정 키워드 엔진으로 이 작업을 [수행합니다.](speech-devices-sdk-create-kws.md) 음성 도우미는 서비스 측 키워드 확인을 사용하여 키워드 활성화의 정확도를 개선할 수 있습니다(장치만 사용).
|[텍스트에 대한 음성](speech-to-text.md) | 음성 도우미는 음성 서비스에서 [음성-텍스트를](speech-to-text.md) 사용하여 실시간 오디오를 인식된 텍스트로 변환합니다. 이 텍스트는 전사된 대로 도우미 구현및 클라이언트 응용 프로그램에 모두 사용할 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 도우미의 텍스트 응답은 음성 서비스에서 [텍스트 음성 변환을](text-to-speech.md) 사용하여 합성됩니다. 이 합성은 클라이언트 응용 프로그램에서 오디오 스트림으로 사용할 수 있습니다. Microsoft는 귀사에 음성을 제공하는 사용자 지정 고품질 Neural TTS 음성을 빌드할 수 있는 기능을 제공합니다. 자세한 내용은 [당사에 문의하십시오.](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>음성 비서 시작

10분 이내에 코드를 실행할 수 있도록 설계된 퀵스타트를 제공합니다. 이 표에는 언어별로 구성된 음성 비서 빠른 시작 목록이 포함되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| C#, UWP | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [찾아보기](https://aka.ms/csspeech/javaref) |
| Java | Android | [찾아보기](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>예제 코드

음성 도우미를 만들기 위한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이 샘플은 여러 인기 있는 프로그래밍 언어로 도우미에 연결하기 위한 클라이언트 응용 프로그램을 다룹니다.

* [음성 도우미 샘플(SDK)](https://aka.ms/csspeech/samples)
* [튜토리얼 : 음성 음성 SDK, C와 도우미를 활성화 #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>자습서

[음성 SDK 및 직접 회선 음성 채널을 사용하여 도우미를 음성으로 활성화하는](tutorial-voice-enable-your-bot-speech-sdk.md)방법에 대한 자습서 .

## <a name="customization"></a>사용자 지정

음성 서비스를 사용하여 빌드된 음성 도우미는 [음성-텍스트,](speech-to-text.md) [텍스트 음성](text-to-speech.md)변환 및 사용자 지정 [키워드 선택에](speech-devices-sdk-create-kws.md)사용할 수 있는 모든 사용자 지정 옵션을 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 [다릅니다(지원되는 언어](supported-languages.md)참조).

## <a name="reference-docs"></a>참조 문서

* [음성 SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [사용자 지정 명령에 대해 자세히 알아보기(미리 보기)](custom-commands.md)
* [직접 회선 음성에 대해 자세히 알아보기](direct-line-speech.md)
