---
title: 직접 줄 음성-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK (소프트웨어 개발 키트)와 직접 줄 음성을 사용 하는 음성 도우미의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6dc125b68af8f9a07731aaae9d858c19c51f934f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402385"
---
# <a name="what-is-direct-line-speech"></a>Direct Line Speech란?

**직접 라인 음성** 은 유연 하 고 확장 가능한 음성 도우미를 만들기 위한 강력 하 고 종단 간 솔루션입니다. 봇 프레임 워크와 봇과의 음성 출력 상호 작용에 최적화 된 봇 프레임 워크 및 직접 라인 음성 채널을 기반으로 합니다.

[음성 도우미](voice-assistants.md) 는 사용자를 수신 하 고 응답으로 작업을 수행 하 여 자주 말하는 경우를 말합니다. [음성 텍스트](speech-to-text.md) 를 사용 하 여 사용자의 음성을 높여줄 다음 텍스트에 대 한 자연어 이해에 대 한 작업을 수행 합니다. 이 작업에는 [텍스트를 음성](text-to-speech.md)으로 생성 하 여 생성 된 비서의 음성 출력이 포함 되는 경우가 많습니다.

직접 라인 음성은 음성 도우미에 대해 가장 높은 수준의 사용자 지정 및 복잡성을 제공 합니다. 작업 완료 또는 명령 및 제어를 사용 하는 두 가지 기능을 사용 하 여 열린 종료, 자연 또는 hybrids 대화형 시나리오용으로 설계 되었습니다. 이러한 높은 수준의 유연성은 더 복잡 해지고, 자연어 입력을 사용 하 여 잘 정의 된 작업으로 범위가 지정 된 시나리오에서는 간소화 된 솔루션 환경을 위한 [사용자 지정 명령 (미리 보기)](custom-commands.md) 을 고려해 야 할 수 있습니다.

## <a name="getting-started-with-direct-line-speech"></a>직접 선 음성 시작

직접 줄 음성을 사용 하 여 음성 도우미를 만드는 첫 번째 단계는 [음성 구독 키를 가져오고](get-started.md)해당 구독과 연결 된 새 봇을 만든 다음 직접 선 음성 채널에 봇을 연결 하는 것입니다.

   ![직접 라인 음성 오케스트레이션 서비스 흐름의 개념적 다이어그램](media/voice-assistants/overview-directlinespeech.png "음성 채널 흐름")

직접 라인 음성을 사용 하 여 간단한 음성 도우미를 만드는 방법에 대 한 전체 단계별 가이드는 음성 SDK를 사용 하 여 [봇 사용에 대 한 자습서 및 직접 선 음성 채널](tutorial-voice-enable-your-bot-speech-sdk.md)을 참조 하세요.

또한 코드를 실행 하 고 Api를 빠르게 학습할 수 있도록 설계 된 퀵 스타트를 제공 합니다. 이 표에는 언어 및 플랫폼 별로 구성 된 음성 도우미 빠른 시작 목록이 포함 되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| C #, UWP | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [찾아보기](https://aka.ms/csspeech/javaref) |
| Java | Android | [찾아보기](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>예제 코드

음성 도우미를 만드는 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 몇 가지 인기 있는 프로그래밍 언어로 도우미에 연결 하기 위한 클라이언트 응용 프로그램을 포함 합니다.

* [음성 도우미 샘플(SDK)](https://aka.ms/csspeech/samples)
* [자습서: 음성 SDK를 사용 하 여 길잡이 활성화, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>사용자 지정

Speech service를 사용 하 여 빌드된 음성 도우미는 [음성 텍스트](speech-to-text.md), [텍스트 음성 변환](text-to-speech.md)및 [사용자 지정 키워드 선택](speech-devices-sdk-create-kws.md)에 사용할 수 있는 모든 사용자 지정 옵션을 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다 ( [지원 되는 언어](supported-languages.md)참조).

음성 도우미에 대 한 직접 줄 음성 및 관련 기능은 [가상 길잡이 솔루션 및 엔터프라이즈 템플릿에](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)적합 합니다. 직접 라인 음성은 호환 봇에서 작동할 수 있지만 이러한 리소스는 고품질의 대화형 환경 뿐만 아니라 빠르게 시작할 수 있는 일반적인 지원 기술 및 모델에 재사용 가능한 기준을 제공 합니다.

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [기본 봇 만들기 및 배포](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [가상 길잡이 솔루션 및 엔터프라이즈 템플릿 가져오기](https://github.com/Microsoft/AI)
