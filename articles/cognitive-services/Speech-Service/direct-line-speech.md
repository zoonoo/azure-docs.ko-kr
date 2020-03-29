---
title: 직회 음성 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 소프트웨어 개발 키트(SDK)를 사용하여 직접 회선 음성을 사용하는 음성 비서의 기능, 기능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 714cdefa7100160a510d1922a2547ae53692d76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367808"
---
# <a name="what-is-direct-line-speech"></a>Direct Line Speech란?

**다이렉트 라인 스피치는** 유연하고 확장 가능한 음성 도우미를 만들기 위한 견고한 엔드 투 엔드 솔루션입니다. 봇 프레임워크와 직접 라인 음성 채널에 의해 구동되며, 봇과의 음성 변환, 음성 출력 상호 작용에 최적화되어 있습니다.

[음성 비서는](voice-assistants.md) 사용자의 말을 듣고 응답하여 행동을 취하며 종종 다시 말하기를 합니다. [음성-텍스트를](speech-to-text.md) 사용하여 사용자의 음성을 전사한 다음 텍스트의 자연어 이해에 대한 조치를 취합니다. 이 작업에는 [텍스트 음성 변환으로](text-to-speech.md)생성된 도우미의 음성 출력이 자주 포함됩니다.

다이렉트 라인 스피치는 음성 비서에 대한 최고 수준의 사용자 정의 및 정교함을 제공합니다. 작업 완료 또는 명령 및 제어 사용이 포함된 개방형, 자연스러운 또는 두 가지 의 하이브리드 대화 형 시나리오를 위해 설계되었습니다. 이러한 높은 수준의 유연성은 더 복잡하며 자연어 입력을 사용하여 잘 정의된 작업으로 범위가 지정되는 시나리오는 간소화된 솔루션 환경을 위해 [사용자 지정 명령(미리 보기)을](custom-commands.md) 고려할 수 있습니다.

## <a name="getting-started-with-direct-line-speech"></a>직접 회선 스피치 시작하기

직접 회선 음성을 사용하여 음성 도우미를 만드는 첫 번째 단계는 [음성 구독 키를 얻고](get-started.md)해당 구독과 연결된 새 봇을 만들고 봇을 직접 회선 음성 채널에 연결하는 것입니다.

   ![직접 회선 음성 오케스트레이션 서비스 흐름의 개념 다이어그램](media/voice-assistants/overview-directlinespeech.png "음성 채널 흐름")

다이렉트 라인 스피치를 사용하여 간단한 음성 도우미를 만드는 방법에 대한 완전한 단계별 가이드는 [음성 SDK 및 직접 회선 음성 채널을 사용하여 봇을 음성 으로 사용할 수 있는 자습서를](tutorial-voice-enable-your-bot-speech-sdk.md)참조하십시오.

또한 코드를 실행하고 API를 빠르게 학습할 수 있도록 설계된 퀵스타트도 제공합니다. 이 표에는 언어 및 플랫폼별로 구성된 음성 비서 빠른 시작 목록이 포함되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| C#, UWP | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [찾아보기](https://aka.ms/csspeech/javaref) |
| Java | Android | [찾아보기](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>예제 코드

음성 도우미를 만들기 위한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이 샘플은 여러 인기 있는 프로그래밍 언어로 도우미에 연결하기 위한 클라이언트 응용 프로그램을 다룹니다.

* [음성 도우미 샘플(SDK)](https://aka.ms/csspeech/samples)
* [튜토리얼 : 음성 음성 SDK, C와 도우미를 활성화 #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>사용자 지정

음성 서비스를 사용하여 빌드된 음성 도우미는 [음성-텍스트,](speech-to-text.md) [텍스트 음성](text-to-speech.md)변환 및 사용자 지정 [키워드 선택에](speech-devices-sdk-create-kws.md)사용할 수 있는 모든 사용자 지정 옵션을 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 [다릅니다(지원되는 언어](supported-languages.md)참조).

직접 회선 음성 및 음성 도우미에 대한 관련 기능은 [가상 도우미 솔루션 및 엔터프라이즈 템플릿에](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)대한 이상적인 보충자료입니다. Direct Line Speech는 호환되는 모든 봇과 함께 작동할 수 있지만 이러한 리소스는 고품질 대화 환경뿐만 아니라 빠르게 시작하기 위한 일반적인 지원 기술 및 모델에 대한 재사용 가능한 기준을 제공합니다.

## <a name="reference-docs"></a>참조 문서

* [음성 SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [기본 봇 만들기 및 배포](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [가상 도우미 솔루션 및 엔터프라이즈 템플릿 받기](https://github.com/Microsoft/AI)
