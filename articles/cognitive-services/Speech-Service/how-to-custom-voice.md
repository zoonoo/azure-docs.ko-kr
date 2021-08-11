---
title: Custom Voice - Speech Service를 사용하여 합성 개선
titleSuffix: Azure Cognitive Services
description: Custom Voice는 사용자의 브랜드에 대해 인식할 수 있는 독특한 음성을 만들 수 있는 온라인 도구 집합입니다. 몇 가지 오디오 파일과 관련 음성 텍스트만 있으면 시작할 수 있습니다. 사용자 지정 음성 텍스트 변환 환경 만들기를 시작하려면 아래 링크를 따라가세요.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577464"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice 시작

[Custom Voice](https://aka.ms/customvoice)는 사용자의 브랜드에 대해 인식할 수 있는 독특한 음성을 만들 수 있는 온라인 도구 집합입니다. 몇 가지 오디오 파일과 관련 음성 텍스트만 있으면 시작할 수 있습니다. 사용자 지정 음성 텍스트 변환 환경 만들기를 시작하려면 아래 링크를 따라가세요.

## <a name="whats-in-custom-voice"></a>Custom Voice의 기능

Custom Voice를 시작하려면 Azure 계정 및 Speech Service 구독이 필요합니다. 계정을 만들면 데이터를 준비하고, 모델을 학습 및 테스트하고, 음성 품질을 평가하고, 궁극적으로 사용자 지정 음성 모델을 배포할 수 있습니다.

아래 다이어그램에는 [Custom Voice 포털](https://aka.ms/customvoice)을 사용하여 사용자 지정 음성 모델을 만드는 단계가 요약되어 있습니다. 자세한 내용을 보려면 링크를 사용하세요.

![Custom Voice 아키텍처 다이어그램](media/custom-voice/custom-voice-diagram.png)

1. [프로젝트 구독 및 만들기](#set-up-your-azure-account) - Azure 계정을 만들고Speech Service 구독을 만듭니다. 이 통합 구독은 음성 텍스트, 텍스트 음성 변환, 음성 번역 및 Custom Voice 포털에 대한 액세스를 제공합니다. 그런 다음, Speech Service 구독을 사용하여 첫 번째 Custom Voice 프로젝트를 만듭니다.

2. [데이터 업로드](how-to-custom-voice-create-voice.md#upload-your-datasets) - Custom Voice 포털 또는 Custom Voice API를 사용하여 데이터(오디오 및 텍스트)를 업로드합니다. 포털에서 발음 점수와 신호 대 잡음 비율을 조사하고 평가할 수 있습니다. 자세한 내용은 [Custom Voice용 데이터를 준비하는 방법](how-to-custom-voice-prepare-data.md)을 참조하세요.

3. [모델 학습](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – 데이터를 사용하여 사용자 지정 텍스트 음성 변환 음성 모델을 만듭니다. 모델을 여러 언어로 학습시킬 수 있습니다. 학습 후 모델을 테스트하고, 결과에 만족하면 모델을 배포할 수 있습니다.

4. [모델 배포](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 텍스트 음성 변환 음성 모델에 대한 사용자 지정 엔드포인트를 만들고 제품, 도구 및 애플리케이션의 음성 합성에 사용합니다.

## <a name="custom-neural-voices"></a>사용자 지정 신경망 음성

Custom Voice는 현재 표준 계층과 신경망 계층을 모두 지원합니다. 사용자 지정 신경망 음성은 사용자가 더 적은 데이터로 더 고품질의 음성 모델을 빌드할 수 있도록 지원하고, AI를 책임감 있게 배포할 수 있는 방법을 제공합니다 더 자연스러운 대화형 인터페이스를 위해 보다 사실적인 음성을 개발하고, 고객과 최종 사용자가 책임감 있게 최신 텍스트 음성 변환 기술을 활용할 수 있게 하려면 사용자 지정 신경망 음성을 사용하는 것이 좋습니다. [사용자 지정 신경망 음성에 대해 자세히 알아보세요](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> Microsoft는 책임감 있는 AI 설계를 위한 노력의 일환으로 사용자 지정 신경망 음성의 사용을 제한했습니다. 애플리케이션을 검토하고 이를 책임감 있는 AI 원칙에 따라 사용하도록 약속해야만 이 기술에 대한 액세스 권한을 얻을 수 있습니다. [제한 액세스 정책](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)에 대해 자세히 알아보고 [여기서 신청](https://aka.ms/customneural)하세요. 표준 및 신경망 버전의 Custom Voice에 지원되는 [언어](language-support.md#customization)와 [지역](regions.md#custom-voices)은 서로 다릅니다. 시작하기 전에 세부 정보를 확인하세요.  

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

Custom Speech 포털을 사용하여 사용자 지정 모델을 만들려면 Speech Service 구독이 필요합니다. Azure에서 Speech Service 구독을 만들려면 다음 지침을 따르세요. Azure 계정이 없으면 새 계정에 등록하면 됩니다.  

Azure 계정과 Speech Service 구독을 만든 후에는 Custom Voice 포털에 로그인하여 구독을 연결해야 합니다.

1. Azure Portal에서 Speech Service 구독 키를 가져옵니다.
2. [Custom Voice 포털](https://aka.ms/custom-voice)에 로그인합니다.
3. 구독을 선택하고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환하려면 상단 탐색 메뉴에 있는 톱니 모양 아이콘을 사용합니다.

> [!NOTE]
> 서비스를 사용하려면 Azure에서 F0 또는 S0 Speech Service 키를 만들어야 합니다. 사용자 지정 신경망 음성은 S0 계층만 지원합니다. 

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 엔드포인트와 같은 콘텐츠는 Voice 포털에서 **프로젝트** 로 구성됩니다. 각 프로젝트는 만들려는 음성의 국가/언어 및 성별에 한정됩니다. 예를 들어 미국('en-US')에서 영어를 사용하는 콜 센터의 채팅 봇을 위한 여성 음성 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **텍스트 음성 변환/Custom Voice** 탭을 선택한 다음, **새 프로젝트** 를 클릭합니다. 마법사에서 제공하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터**, **학습**, **테스트** 및 **배포** 라는 네 가지 탭이 표시됩니다. [다음 단계](#next-steps)에 제공된 링크를 사용하여 각 탭을 사용하는 방법을 알아보세요.

> [!IMPORTANT]
> [Custom Voice 포털](https://aka.ms/custom-voice)이 최근에 업데이트되었습니다. CRIS.ai 포털에서 또는 API를 사용하여 이전 데이터, 모델, 테스트 및 게시된 엔드포인트를 만든 경우, 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결해야 합니다.

## <a name="how-to-migrate-to-custom-neural-voice"></a>사용자 지정 신경망 음성으로 마이그레이션하는 방법

Custom Voice의 표준/비신경망 학습 계층(적응형, 통계 파라메트릭, 연결)은 더 이상 사용되지 않습니다. 2021/2/28 이전에는 모든 기존 음성 구독으로 공지가 전송되었습니다. 사용 중단 기간(2021/3/1~2024/2/29) 동안 기존 표준 계층 사용자는 생성된 비신경망 모델을 계속 사용할 수 있습니다. 모든 새 사용자/새 음성 리소스는 신경망 계층/사용자 지정 신경망 음성으로 이동해야 합니다. 2024/2/29 이후에는 모든 표준/비신경망 사용자 지정 음성이 더 이상 지원되지 않습니다. 

비신경망/표준 사용자 지정 음성을 사용하는 경우 아래 단계를 즉시 수행하여 사용자 지정 신경망 음성으로 마이그레이션하세요. 사용자 지정 신경망 음성으로 이전하면 훨씬 더 자연스러운 대화형 인터페이스를 위해 보다 사실적인 음성을 개발하고, 고객과 최종 사용자가 책임감 있게 최신 텍스트 음성 변환 기술을 활용하도록 할 수 있습니다. 

1. [제한 액세스 정책](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)에 대해 자세히 알아보고 [여기서 신청](https://aka.ms/customneural)하세요. 사용자 지정 신경망 음성 서비스에 대한 액세스 권한은 Microsoft가 적격 기준에 따라 단독 재량으로 결정합니다. 고객은 애플리케이션을 검토하고 [책임감 있는 AI 원칙](https://microsoft.com/ai/responsible-ai) 및 [사용 규정](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)에 따라 사용하도록 약속한 후에만 이 기술에 대한 액세스 권한을 얻을 수 있습니다. 
2. 애플리케이션이 승인되면 "신경망" 학습 기능에 대한 액세스 권한이 제공됩니다. 애플리케이션에서 제공하는 것과 동일한 Azure 구독을 사용하여 [Custom Voice 포털](https://speech.microsoft.com/customvoice)에 로그인해야 합니다. 
    > [!IMPORTANT]
    > Microsoft에서는 성우를 보호하고, 무단 녹음을 사용하거나 성우의 허가를 받지 않은 음성 모델 학습을 방지하기 위해 고객에게 성우의 녹음된 동의 발언을 업로드할 것을 요구합니다. 녹음 스크립트를 준비할 때는 이 문장을 포함해야 합니다. “나[성 및 이름]는 내 음성의 합성 버전을 만들고 사용하기 위해 [회사 이름]에서 내 음성 녹음이 사용된다는 것을 알고 있습니다.”
    > 이 문장은 **성우** 탭에 구두 동의 파일로 업로드해야 합니다. 이는 학습 데이터 세트가 동의한 사람에 의해 녹음되었는지 확인하는 데 사용됩니다.
3. 사용자 지정 신경망 음성 모델이 생성되면 새 엔드포인트에 음성 모델을 배포합니다. 신경망 음성 모델을 사용하여 새로운 사용자 지정 음성 엔드포인트를 만들려면 **텍스트 음성 변환 > Custom Voice > 배포** 로 이동합니다. **모델 배포** 를 선택하고 사용자 지정 엔드포인트의 **이름** 및 **설명** 을 입력합니다. 그런 다음, 이 엔드포인트와 연결하려는 사용자 지정 신경망 음성 모델을 선택하고 배포를 확인합니다.  
4. 새 모델을 사용하여 새 엔드포인트를 만든 경우 앱에서 코드를 업데이트합니다. 

## <a name="next-steps"></a>다음 단계

- [Custom Voice 데이터 준비](how-to-custom-voice-prepare-data.md)
- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [가이드: 음성 샘플 기록](record-custom-voice-samples.md)
