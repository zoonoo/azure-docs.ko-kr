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
ms.openlocfilehash: bd808c0c71e02483b4c4b06e612720c1802869a0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577972"
---
# <a name="about-voice-assistants"></a>음성 도우미 정보

Azure Speech Services를 사용 하는 음성 도우미는 개발자가 응용 프로그램 및 환경에 대 한 자연 스러운 대화형 인터페이스를 만들 수 있도록 합니다.

음성 도우미 서비스는 (1) 봇 Framework의 Direct Line Speech channel 또는 (2) 작업 완료를 위한 통합 사용자 지정 명령 (미리 보기) 서비스를 사용 하는 장치 및 비서 구현 간에 빠르고 안정적인 상호 작용을 제공 합니다.

응용 프로그램은 음성 SDK (소프트웨어 개발 키트)를 사용 하 여 음성 도우미 서비스에 연결 합니다.

   ![음성 도우미 오케스트레이션 서비스 흐름의 개념적 다이어그램](media/voice-assistants/overview.png "음성 도우미 흐름")

## <a name="choosing-an-assistant-solution"></a>길잡이 솔루션 선택

음성 도우미를 만드는 첫 번째 단계는 수행할 작업을 결정 하는 것입니다. Azure Speech Services는 길잡이 상호 작용을 작성 하기 위한 여러 보충 솔루션을 제공 합니다. Bot Framework의 [Direct Line Speech](direct-line-speech.md) channel에서 제공 하는 유연성과 유연성을 원하는 지 여부에 관계 없이 간단한 시나리오에 대 한 [사용자 지정 명령 (미리 보기)](custom-commands.md) 을 선택 하면 적절 한 도구를 시작 하는 데 도움이 됩니다.

| 수행하려는 작업 | 다음을 고려 하십시오. | 예를 들어... |
|-------------------|------------------|----------------|
|강력한 기술 통합 및 전체 배포 제어를 포함 하는 오픈 대화 종료 | Bot Framework의 [직접 선 음성](direct-line-speech.md) 채널 | <ul><li>"시애틀로 이동 해야 합니다."</li><li>"주문할 수 있는 피자 종류는 무엇 인가요?"</li></ul>
|간단 하 게 작성 하 고 호스팅하는 명령 및 제어 또는 작업 기반 대화 | [사용자 지정 명령 (미리 보기)](custom-commands.md) | <ul><li>"오버 헤드 조명 켜기"</li><li>"핫을 5도로 설정"</ul>

길잡이에서 처리할 항목을 아직 모를 경우에는 [줄 음성을](direct-line-speech.md) 가장 적합 한 기본 선택으로 선택 하는 것이 좋습니다. 또한 다양 한 도구 집합 및 [가상 길잡이 솔루션, 엔터프라이즈 템플릿](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) 및 QnA Maker 서비스 (예: 일반 패턴을 기반으로 구축 하 고 기존 기술 자료를 사용 하는 [서비스](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) )와의 통합을 제공 합니다.

[사용자 지정 명령 (미리 보기)](custom-commands.md) 은 자연어 명령 및 제어 시나리오에 맞게 특별히 조정 된 효율적인 제작 및 호스팅 환경을 제공 합니다.
   ![길잡이 솔루션 비교](media/voice-assistants/assistant-solution-comparison.png "Comparison of assistant solutions ")

## <a name="core-features"></a>핵심 기능

[직접 줄 음성](direct-line-speech.md) 또는 [사용자 지정 명령 (미리 보기)](custom-commands.md) 을 선택 하 여 길잡이 상호 작용을 만들려면 다양 한 사용자 지정 기능 집합을 사용 하 여 사용자의 브랜드, 제품 및 개성에 맞게 길잡이를 사용자 지정할 수 있습니다.

| Category | 기능 |
|----------|----------|
|[사용자 지정 키워드](speech-devices-sdk-create-kws.md) | 사용자는 "안녕하세요 Contoso"와 같은 사용자 지정 키워드를 사용 하 여 도우미와 대화를 시작할 수 있습니다. 앱은 [여기에서 생성할 수 있는](speech-devices-sdk-create-kws.md)사용자 지정 키워드를 사용 하 여 구성할 수 있는 Speech SDK의 사용자 지정 키워드 엔진을 사용 하 여이를 수행 합니다. 음성 도우미는 서비스 측 키워드 확인을 사용 하 여 키워드 활성화의 정확도를 향상 시킬 수 있습니다 (장치에 대해서만).
|[음성 텍스트](speech-to-text.md) | 음성 도우미는 Azure Speech Services의 [음성 텍스트](speech-to-text.md) 를 사용 하 여 실시간 오디오를 인식 된 텍스트로 변환 합니다. 이 텍스트는 길잡이 구현 및 클라이언트 응용 프로그램 모두에 transcribed 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 길잡이의 텍스트 응답은 Azure Speech Services에서 [텍스트 음성 변환](text-to-speech.md) 을 사용 하 여 합성 됩니다. 이러한 합성은 클라이언트 응용 프로그램에서 오디오 스트림으로 사용할 수 있게 됩니다. Microsoft는 브랜드에 음성을 제공 하는 사용자 지정 고품질 신경망을 빌드하는 기능을 제공 합니다. 자세히 알아보려면 [microsoft에 문의](mailto:mstts@microsoft.com)하세요.

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