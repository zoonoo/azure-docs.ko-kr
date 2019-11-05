---
title: 음성 도우미-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK (Software Development Kit)를 사용 하는 음성 도우미의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501017"
---
# <a name="about-voice-assistants"></a>음성 도우미 정보

Azure Speech Services를 사용 하는 음성 도우미는 개발자가 응용 프로그램 및 환경에 대 한 자연 스러운 대화형 인터페이스를 만들 수 있도록 합니다.

음성 도우미 서비스는 (1) 봇 Framework의 Direct Line Speech channel 또는 (2) 작업 완료를 위한 통합 사용자 지정 명령 (미리 보기) 서비스를 사용 하는 장치 및 비서 구현 간에 빠르고 안정적인 상호 작용을 제공 합니다.

응용 프로그램은 음성 SDK (소프트웨어 개발 키트)를 사용 하 여 음성 도우미 서비스에 연결 합니다.

   ![음성 도우미 오케스트레이션 서비스 흐름의 개념적 다이어그램](media/voice-assistants/overview.png "음성 도우미 흐름")

## <a name="core-features"></a>핵심 기능

| Category | 기능 |
|----------|----------|
|[사용자 지정 키워드](speech-devices-sdk-create-kws.md) | 사용자는 "안녕하세요 Contoso"와 같은 사용자 지정 키워드를 사용 하 여 도우미와 대화를 시작할 수 있습니다. 앱은 [여기에서 생성할 수 있는](speech-devices-sdk-create-kws.md)사용자 지정 키워드를 사용 하 여 구성할 수 있는 Speech SDK의 사용자 지정 키워드 엔진을 사용 하 여이를 수행 합니다. 음성 도우미는 서비스 측 키워드 확인을 사용 하 여 키워드 활성화의 정확도를 향상 시킬 수 있습니다 (장치에 대해서만).
|[음성 텍스트](speech-to-text.md) | 음성 도우미는 Azure Speech Services의 [음성 텍스트](speech-to-text.md) 를 사용 하 여 실시간 오디오를 인식 된 텍스트로 변환 합니다. 이 텍스트는 길잡이 구현 및 클라이언트 응용 프로그램 모두에 transcribed 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 길잡이의 텍스트 응답은 Azure Speech Services에서 [텍스트 음성 변환](text-to-speech.md) 을 사용 하 여 합성 됩니다. 이러한 합성은 클라이언트 응용 프로그램에서 오디오 스트림으로 사용할 수 있게 됩니다. Microsoft는 브랜드에 음성을 제공 하는 사용자 지정 고품질 신경망을 빌드하는 기능을 제공 합니다. 자세히 알아보려면 [microsoft에 문의](mailto:mstts@microsoft.com)하세요.

## <a name="comparing-assistant-solutions"></a>길잡이 솔루션 비교

음성 도우미 서비스는 사용자의 장치 응용 프로그램을 고유한 길잡이 구현에 연결 합니다. 개발자는 (1) 봇 Framework의 [Direct Line Speech](direct-line-speech.md) channel 또는 (2) [사용자 지정 명령 (미리 보기)](custom-commands.md) 솔루션을 사용 하 여 음성 도우미를 작성 합니다.

   ![길잡이 솔루션 비교](media/voice-assistants/assistant-solution-comparison.png "길잡이 솔루션 비교")

| 해결 방법 | 기능 |
|----------|----------|
|[사용자 지정 명령 (미리 보기)](custom-commands.md) | 사용자 지정 명령 (미리 보기)은 음성 도우미를 위한 간소화 된 제작 및 호스팅 솔루션을 제공 합니다. 작업 완료 및 명령 및 제어 시나리오의 요구 사항에 맞게 조정 됩니다.
|[직접 줄 음성](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | 직접 줄 음성을 사용 하면 (1) 클라이언트 응용 프로그램, (2) 호환 봇, (3) Azure Speech Services의 기능 간에 원활한 원활한 연결을 사용할 수 있습니다. 직접 선 음성 채널을 사용 하도록 봇을 구성 하는 방법에 대 한 자세한 내용은 [Bot Framework 설명서의 해당 페이지](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)를 참조 하세요.

이러한 솔루션 중 하나를 사용 하 여 음성 도우미를 만들었으면 음성 SDK의 `DialogServiceConnector`를 사용 하 여 장치 응용 프로그램에 연결 합니다. 자세한 내용은 각 솔루션에 대 한 빠른 시작 및 샘플을 참조 하세요.

## <a name="getting-started-with-voice-assistants"></a>음성 도우미 시작

10 분 이내에 코드를 실행 하도록 설계 된 빠른 시작을 제공 합니다. 이 표에는 언어 별로 구성 된 음성 도우미 빠른 시작 목록이 포함 되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>샘플 코드

음성 도우미를 만드는 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 몇 가지 인기 있는 프로그래밍 언어로 도우미에 연결 하기 위한 클라이언트 응용 프로그램을 포함 합니다.

* [음성 도우미 샘플 (SDK)](https://aka.ms/csspeech/samples)
* [자습서: 음성 SDK를 통해 길잡이 활성화C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>자습서

[음성 SDK 및 직접 선 음성 채널을 사용 하 여 길잡이를 음성으로 활성화](tutorial-voice-enable-your-bot-speech-sdk.md)하는 방법에 대 한 자습서입니다.

## <a name="customization"></a>사용자 지정

Azure Speech Services를 사용 하 여 빌드된 음성 도우미는 [음성 텍스트](speech-to-text.md), [텍스트 음성 변환](text-to-speech.md)및 [사용자 지정 키워드 선택](speech-devices-sdk-create-kws.md)에 사용할 수 있는 사용자 지정 옵션의 전체 범위를 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다 ( [지원 되는 언어](supported-languages.md)참조).

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [음성 SDK 가져오기](speech-sdk.md)
* [사용자 지정 명령에 대 한 자세한 정보 (미리 보기)](custom-commands.md)
* [직접 라인 음성에 대 한 자세한 정보](direct-line-speech.md)