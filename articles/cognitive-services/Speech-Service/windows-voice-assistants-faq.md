---
title: Windows의 음성 도우미-FAQ
titleSuffix: Azure Cognitive Services
description: Windows 음성 에이전트를 개발 하는 동안 자주 발생 하는 일반적인 질문입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: aa71057d3819acb335153ee5b4b65960320405be
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997759"
---
# <a name="samples-and-faqs"></a>샘플 및 Faq

## <a name="the-uwp-voice-assistant-sample"></a>UWP Voice Assistant 샘플

UWP Voice Assistant 샘플은 다음에 사용 되는 Windows의 음성 도우미입니다.

- Windows ConversationalAgent Api의 사용 방법을 보여 줍니다.
- Windows에서 음성 에이전트에 대 한 모범 사례 표시
- MVP 에이전트 응용 프로그램을 위한 융통성 있는 앱 및 재사용 가능한 구성 요소를 제공 합니다.
- 종단간 음성 에이전트 환경을 위한 Windows ConversationalAgent Api와 함께 봇 프레임 워크 또는 사용자 지정 명령과 함께 직접 줄 음성을 사용할 수 있는 방법을 보여 줍니다.

샘플 앱과 함께 제공 되는 설명서는 시작의 필수 구성 요소에서 적절 한 정리를 통해 음성 활성화 및 에이전트 관리의 코드 경로를 안내 합니다.

> [!div class="nextstepaction"]
> [UWP 샘플에 대 한 Github 리포지토리를 방문 하세요.](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>제한 된 액세스 기능 토큰 및 키워드 모델 파일과 같은 리소스는 Microsoft에 문의 어떻게 할까요??

이러한 winvoiceassistants@microsoft.com 리소스를 요청 하려면에 문의 하세요.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>앱을 음성으로 활성화 하면 앱이 작은 창에 표시 됩니다. Compact 보기에서 전체 응용 프로그램 창으로 전환 하려면 어떻게 해야 하나요?

응용 프로그램을 먼저 음성으로 정품 인증 하는 경우에는 압축 된 보기에서 시작 됩니다. Windows의 음성 도우미에 대 한 다양 한 보기 및 전환에 대 한 지침은 [음성 활성화 미리 보기에 대 한 디자인 지침](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) 을 참조 하세요.

Compact 보기에서 전체 앱 보기로 전환 하려면 appView API `TryEnterViewModeAsync`를 사용 합니다.

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Windows에서 음성 도우미 기능이 UWP 응용 프로그램에만 사용 되는 이유는 무엇 인가요?

음성 활성화와 같은 기능과 관련 된 개인 정보 위험에 따라 UWP 플랫폼의 기능을 사용 하 여 Windows의 음성 도우미 기능을 충분히 안전 하 게 보호할 수 있습니다.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>UWP Voice Assistant 샘플에서는 직접 줄 음성을 사용 합니다. Windows에서 음성 도우미에 직접 라인 음성을 사용 해야 하나요?

UWP 샘플 응용 프로그램은 Windows 대화형 에이전트 기능으로 대화 상자 서비스를 사용 하는 방법에 대 한 데모로 직접 줄 음성 및 음성 서비스 SDK를 사용 하 여 개발 되었습니다. 그러나 로컬 및 클라우드 키워드 확인, 음성 텍스트 변환, 봇 대화 상자 및 텍스트 음성 변환에 모든 서비스를 사용할 수 있습니다.