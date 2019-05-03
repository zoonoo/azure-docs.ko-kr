---
title: 사용자 지정 음성-첫 번째 가상 도우미 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 개요 기능, 기능 및 Cognitive Services 음성 소프트웨어 개발 키트 (SDK) Bot Framework에 직접 줄 음성 채널을 사용 하는 사용자 지정 음성-첫 번째 가상 도우미에 대 한 제한입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: f40e53c67f099fea739e45f6bdc6963ee1e235cb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026757"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>사용자 지정 음성-첫 번째 가상 도우미 미리 보기 정보

Azure 음성 서비스를 사용 하 여 사용자 지정 가상 도우미 하면 개발자가 자신의 응용 프로그램과 환경에 대 한 자연 스러운, 인간과 유사한 대화형 인터페이스를 만들 수 있습니다. 봇 프레임 워크의 직접 줄 음성 채널의, 짧은 대기 시간 및 높은 안정성으로 상호 작용 아웃 음성 음성 수 있도록 호환 수 조정, 오케스트레이션 된 진입점을 제공 하 여 이러한 기능을 향상 시킵니다. 이러한 봇에 자연 언어 상호 작용에 대 한 Microsoft의 LUIS (Language Understanding)를 사용할 수 있습니다. 직접 줄 음성 음성 서비스 소프트웨어 개발 키트 (SDK)를 사용 하 여 장치에 액세스 합니다.

   ![직접 회선 음성 오케스트레이션 서비스 흐름의 개념도](media/voice-first-virtual-assistants/overview.png "The 음성 채널 흐름")

직접 줄 음성 및 사용자 지정 음성-첫 번째 가상 도우미에 대 한 관련된 기능을 이상적인 보완은는 [가상 도우미 솔루션](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction) 하 고 [엔터프라이즈 템플릿](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)합니다. 모든 호환 봇과 직접 줄 음성 작동할 수 있지만 이러한 리소스는 고품질 대화형 환경 뿐만 아니라 일반적인 지원 기술 및 신속 하 게 시작 하는 것에 대 한 모델에 대 한 다시 사용할 수 있는 초기 계획을 제공 합니다.

## <a name="core-features"></a>핵심 기능

| Category | 기능 |
|----------|----------|
|[사용자 지정 절전 모드 해제 단어](speech-devices-sdk-create-kws.md) | "Hey Contoso."와 같은 사용자 지정 키워드를 사용 하 여 봇을 사용 하 여 대화를 시작 하려면 사용자가 설정할 수 있습니다. 이 작업은 사용자 지정 절전 모드 해제 단어로 구성 될 수 있는 음성 SDK에서 사용자 지정 절전 모드 해제 단어 엔진을 사용 하 여 수행 됩니다 [여기에서 생성할 수 있는](speech-devices-sdk-create-kws.md)합니다. 직접 줄 음성 채널 및 단독으로 장치 절전 모드 해제 word 활성화의 정확도 개선 하는 서비스 쪽 절전 모드 해제 word 확인을 포함 합니다.
|[음성을 텍스트로 변환](speech-to-text.md) | 인식 된 텍스트를 사용 하 여 실시간으로 기록 오디오를 포함 하는 직접 줄 음성 채널 [음성-텍스트](speech-to-text.md) Azure 음성 서비스에서입니다. 이 텍스트는 감정과 대로 봇을 클라이언트 응용 프로그램에 사용할 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 사용 하 여 봇의 응답을 텍스트를 합성 됩니다 [text to speech](text-to-speech.md) Azure 음성 서비스에서입니다. 이 합성 한 후 제공 될 예정 오디오 스트림으로 클라이언트 응용 프로그램입니다. Microsoft는 직접 구축 하는 기능을 제공 [사용자 지정, 고품질 신경망 TTS 음성](https://aka.ms/customneuraltts) 음성으로 브랜드를 제공 합니다.
|[직접 회선 음성](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md) | Bot Framework 내에서 채널로 직접 줄 통해 클라이언트 응용 프로그램, 호환 봇의 경우와 Azure 음성 서비스의 기능 간에 원활 하 게 연결 합니다. 직접 줄 음성 채널을 사용 하면 봇이 마치를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Bot Framework 설명서에서 해당 페이지](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md)합니다.

## <a name="sample-code"></a>샘플 코드

음성 우선 가상 도우미를 만들기 위한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플에 몇 가지 인기 있는 프로그래밍 언어에서 봇을 연결 하기 위한 클라이언트 응용 프로그램을 다룹니다.

* [음성 우선 가상 도우미 샘플 (SDK)](https://aka.ms/csspeech/samples)
* [빠른 시작: 음성 우선 가상 도우미 (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [빠른 시작: 음성 우선 가상 도우미 (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>사용자 지정

Azure 음성 서비스를 사용 하 여 빌드된 음성 우선 가상 도우미를 사용할 수 있는 사용자 지정 옵션의 전체 범위를 사용할 수 [음성-텍스트](speech-to-text.md)하십시오 [text to speech](text-to-speech.md), 및 [사용자 지정 키워드 선택](speech-devices-sdk-create-kws.md)합니다.

> [!NOTE]
> 언어/로캘에서 마다 다른 사용자 지정 옵션 (참조 [지원 되는 언어](supported-languages.md)).

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [기본 봇 생성 및 배포](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [가상 도우미 솔루션 및 엔터프라이즈 템플릿 가져오기](https://github.com/Microsoft/AI)
