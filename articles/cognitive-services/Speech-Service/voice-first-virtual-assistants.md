---
title: 사용자 지정 음성-우선 가상 도우미 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: Bot Framework 및 Cognitive Services 음성 소프트웨어 개발 키트(SDK)의 Direct Line Speech 채널을 사용하는 사용자 지정 음성-우선 가상 도우미의 기능, 능력 및 제한에 대한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 1c5712fa8bbdb158992127f8f48d810a0a9b6f79
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603471"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>사용자 지정 음성-우선 가상 도우미 미리 보기 정보

Azure Speech Services를 사용하는 사용자 지정 가상 도우미는 개발자가 자신의 애플리케이션과 경험을 위해 자연스럽고 인간과 유사한 대화형 인터페이스를 만들 수 있도록 해줍니다. Bot Framework의 Direct Line Speech 채널은 짧은 대기 시간 및 높은 안정성으로 음성 입력, 음성 출력 상호 작용을 가능하게 하는 호환 가능 봇에 조정되고 조율된 진입점을 제공함으로써 이러한 능력을 향상시킵니다. 이러한 봇에 자연 언어 상호 작용을 위한 Microsoft의 LUIS(Language Understanding)를 사용할 수 있습니다. 장치는 음성 소프트웨어 개발 키트(SDK)를 사용하여 Direct Line Speech에 액세스합니다.

   ![직접 회선 음성 오케스트레이션 서비스 흐름의 개념도](media/voice-first-virtual-assistants/overview.png "The 음성 채널 흐름")


Direct Line Speech 및 사용자 지정 음성-우선 가상 도우미에 대한 관련된 기능은 [가상 도우미 솔루션 및 엔터프라이즈 템플릿](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)에 대한 이상적인 보완 사항입니다. Direct Line Speech는 호환 가능한 봇과 함께 작동할 수 있지만,  이러한 리소스는 고품질 대화형 환경 뿐만 아니라 빠른 시작을 위한 일반적인 지원 기술과 모델을 위한 재사용 가능한 기준선을 제공합니다.


## <a name="core-features"></a>핵심 기능

| Category | 기능 |
|----------|----------|
|[사용자 지정 절전 모드 해제 단어](speech-devices-sdk-create-kws.md) | "Hey Contoso."와 같은 사용자 지정 키워드를 사용 하 여 봇을 사용 하 여 대화를 시작 하려면 사용자가 설정할 수 있습니다. 이 작업은 사용자 지정 절전 모드 해제 단어로 구성 될 수 있는 음성 SDK에서 사용자 지정 절전 모드 해제 단어 엔진을 사용 하 여 수행 됩니다 [여기에서 생성할 수 있는](speech-devices-sdk-create-kws.md)합니다. 직접 줄 음성 채널 및 단독으로 장치 절전 모드 해제 word 활성화의 정확도 개선 하는 서비스 쪽 절전 모드 해제 word 확인을 포함 합니다.
|[음성을 텍스트로 변환](speech-to-text.md) | 인식 된 텍스트를 사용 하 여 실시간으로 기록 오디오를 포함 하는 직접 줄 음성 채널 [음성-텍스트](speech-to-text.md) Azure 음성 서비스에서입니다. 이 텍스트는 감정과 대로 봇을 클라이언트 응용 프로그램에 사용할 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 사용 하 여 봇의 응답을 텍스트를 합성 됩니다 [text to speech](text-to-speech.md) Azure 음성 서비스에서입니다. 이 합성 한 후 제공 될 예정 오디오 스트림으로 클라이언트 응용 프로그램입니다. Microsoft는 음성으로 귀사 브랜드에 자세히 알아보려면 제공 하는 사용자 고유의 사용자 지정, 고품질 신경망 TTS 음성을 구축 하는 기능을 제공 [문의처](mailto:mstts@microsoft.com)합니다.
|[직접 회선 음성](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Bot Framework 내에서 채널로 직접 줄 통해 클라이언트 응용 프로그램, 호환 봇의 경우와 Azure 음성 서비스의 기능 간에 원활 하 게 연결 합니다. 직접 줄 음성 채널을 사용 하면 봇이 마치를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Bot Framework 설명서에서 해당 페이지](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)합니다.

## <a name="sample-code"></a>샘플 코드

음성-우선 가상 도우미를 만들기 위한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플에서는 몇 가지 인기 있는 프로그래밍 언어로 봇을 연결하기 위한 클라이언트 응용 프로그램을 다룹니다.

* [음성-우선 가상 도우미 샘플 (SDK)](https://aka.ms/csspeech/samples)
* [빠른 시작: 음성-우선 가상 도우미 (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [빠른 시작: 음성-우선 가상 도우미 (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>사용자 지정

Azure Speech Services를 사용하여 빌드된 음성-우선 가상 도우미는 [음성-텍스트](speech-to-text.md), [텍스트-음성](text-to-speech.md) 및 [사용자 지정 키워드 선택](speech-devices-sdk-create-kws.md)에 사용할 수 있는 모든 범위의 사용자 지정 옵션을 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다([지원되는 언어](supported-languages.md)를 참조합니다).

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [기본 봇 생성 및 배포](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [가상 도우미 솔루션 및 엔터프라이즈 템플릿 가져오기](https://github.com/Microsoft/AI)
