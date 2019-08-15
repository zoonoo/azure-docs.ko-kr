---
title: 사용자 지정 음성-첫 번째 가상 도우미 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: Bot Framework 및 Cognitive Services 음성 소프트웨어 개발 키트(SDK)의 Direct Line Speech 채널을 사용하는 사용자 지정 음성-우선 가상 도우미의 기능, 능력 및 제한에 대한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967600"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>사용자 지정 음성-우선 가상 도우미 미리 보기 정보

Azure Speech Services를 사용하는 사용자 지정 가상 도우미는 개발자가 자신의 애플리케이션과 경험을 위해 자연스럽고 인간과 유사한 대화형 인터페이스를 만들 수 있도록 해줍니다. Bot Framework의 Direct Line Speech 채널은 짧은 대기 시간 및 높은 안정성으로 음성 입력, 음성 출력 상호 작용을 가능하게 하는 호환 가능 봇에 조정되고 조율된 진입점을 제공함으로써 이러한 능력을 향상시킵니다. 이러한 봇에 자연 언어 상호 작용을 위한 Microsoft의 LUIS(Language Understanding)를 사용할 수 있습니다. 장치는 음성 소프트웨어 개발 키트(SDK)를 사용하여 Direct Line Speech에 액세스합니다.

   ![직접 라인 음성 오케스트레이션 서비스 흐름의 개념적 다이어그램](media/voice-first-virtual-assistants/overview.png "음성 채널 흐름")


Direct Line Speech 및 사용자 지정 음성-우선 가상 도우미에 대한 관련된 기능은 [가상 도우미 솔루션 및 엔터프라이즈 템플릿](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)에 대한 이상적인 보완 사항입니다. Direct Line Speech는 호환 가능한 봇과 함께 작동할 수 있지만,  이러한 리소스는 고품질 대화형 환경 뿐만 아니라 빠른 시작을 위한 일반적인 지원 기술과 모델을 위한 재사용 가능한 기준선을 제공합니다.


## <a name="core-features"></a>핵심 기능

| 범주 | 기능 |
|----------|----------|
|[사용자 지정 절전 모드 해제 단어](speech-devices-sdk-create-kws.md) | 사용자가 "안녕하세요 Contoso"와 같은 사용자 지정 키워드를 사용 하 여 봇으로 대화를 시작할 수 있도록 설정할 수 있습니다. 이 작업은 [여기에서 생성할 수 있는](speech-devices-sdk-create-kws.md)사용자 지정 절전 모드 해제 단어를 사용 하 여 구성할 수 있는 Speech SDK의 사용자 지정 절전 모드 해제 단어 엔진을 사용 하 여 수행 됩니다. 직접 선 음성 채널은 절전 모드 해제 단어 인증 및 장치 전용의 정확도를 개선 하는 서비스 쪽 절전 모드 해제 단어 확인을 포함 합니다.
|[음성 텍스트](speech-to-text.md) | 직접 선 음성 채널에는 Azure Speech Services의 [음성 텍스트](speech-to-text.md) 를 사용 하 여 인식 된 텍스트에 대 한 오디오의 실시간 기록을 포함 합니다. 이 텍스트는 transcribed 때 봇과 클라이언트 응용 프로그램에서 모두 사용할 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 봇의 텍스트 응답을 Azure 음성 서비스의 [텍스트-음성](text-to-speech.md)을 사용하여 합성합니다. 이 합성은 오디오 스트림으로 클라이언트 응용 프로그램으로 이용 가능하게 될 예정입니다. Microsoft는 귀사 브랜드에 음성으로 제공하는 사용자 고유의 사용자 지정, 고품질 신경망 TTS 음성을 구축하는 기능을 제공하며, 자세한 정보를 알아보려면 [문의](mailto:mstts@microsoft.com)하세요.
|[직접 줄 음성](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | 봇 프레임 워크 내에서 직접 라인 음성은 클라이언트 응용 프로그램, 호환 봇 및 Azure Speech Services의 기능 간에 원활한 원활한 연결을 가능 하 게 합니다. 직접 선 음성 채널을 사용 하도록 봇을 구성 하는 방법에 대 한 자세한 내용은 [Bot Framework 설명서의 해당 페이지](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)를 참조 하세요.

## <a name="get-started-with-virtual-assistants"></a>가상 도우미 시작 하기

10분 이내에 코드를 실행하도록 디자인된 빠른 시작을 제공합니다. 이 표에는 언어별 구성 된 음성 기반 빠른 시작 빠른 시작 목록이 포함 되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| C#, UWP | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [찾아보기](https://aka.ms/csspeech/javaref) |
| Java | Android | [찾아보기](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>샘플 코드

음성-우선 가상 도우미를 만들기 위한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플에서는 몇 가지 인기 있는 프로그래밍 언어로 봇을 연결하기 위한 클라이언트 응용 프로그램을 다룹니다.

* [음성-우선 가상 도우미 샘플 (SDK)](https://aka.ms/csspeech/samples)
* [자습서: 음성 SDK를 사용 하 여 봇 사용C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>자습서
[음성 SDK 및 직접 선 음성 채널을 사용 하 여 봇을 음성으로 사용 하도록 설정](tutorial-voice-enable-your-bot-speech-sdk.md)하는 방법에 대 한 자습서입니다.

## <a name="customization"></a>사용자 지정

Azure Speech Services를 사용하여 빌드된 음성-우선 가상 도우미는 [음성-텍스트](speech-to-text.md), [텍스트-음성](text-to-speech.md) 및 [사용자 지정 키워드 선택](speech-devices-sdk-create-kws.md)에 사용할 수 있는 모든 범위의 사용자 지정 옵션을 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다([지원되는 언어](supported-languages.md)를 참조합니다).

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [음성 SDK 가져오기](speech-sdk.md)
* [기본 봇 생성 및 배포](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [가상 길잡이 솔루션 및 엔터프라이즈 템플릿 가져오기](https://github.com/Microsoft/AI)
