---
title: Windows의 음성 도우미-개요
titleSuffix: Azure Cognitive Services
description: 사용 가능한 기능 및 개발 리소스를 포함 하 여 Windows의 음성 도우미에 대 한 개요입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997765"
---
# <a name="voice-assistants-on-windows"></a>Windows의 음성 도우미

Windows 10 버전 2004 이상에서, 음성 도우미 응용 프로그램은 Windows ConversationalAgent Api를 활용 하 여 완벽 한 음성 사용 길잡이 환경을 구현할 수 있습니다.

## <a name="voice-assistant-features"></a>음성 도우미 기능

음성 에이전트 응용 프로그램은 음성으로 제공 되는 음성 기반 환경을 지원 하기 위해 음성으로 사용 될 수 있습니다. 음성 활성화는 응용 프로그램이 닫히고 화면이 잠길 때 작동 합니다.

또한 Windows는 사용자가 앱 별로 음성 활성화와 잠금 활성화를 제어할 수 있도록 하는 음성 활성화 개인 정보 설정 집합을 제공 합니다.

음성 활성화 후에는 Windows에서 여러 활성 에이전트를 제대로 관리 하 고 각 음성 길잡이가 중단 되거나 비활성화 된 경우이를 알립니다. 이를 통해 응용 프로그램은 중단 및 기타 에이전트 간 이벤트를 제대로 관리할 수 있습니다.

## <a name="how-does-voice-activation-work"></a>음성 활성화는 어떻게 작동 하나요?

AAR (에이전트 활성화 런타임)는 음성 키워드나 단추 누름에서 응용 프로그램 활성화를 관리 하는 Windows의 지속적인 프로세스입니다. 시스템에 등록 된 응용 프로그램이 시스템에 하나 이상 있는 경우 Windows에서 시작 합니다. 응용 프로그램은 Windows SDK의 ConversationalAgent Api를 통해 AAR와 상호 작용 합니다.

사용자가 키워드를 사용 하는 경우 시스템의 software 또는 하드웨어 키워드 관찰자는 키워드가 검색 되었음을 AAR에 게 알리고 키워드 ID를 제공 합니다. 그런 다음 AAR는 BackgroundService에 게 해당 응용 프로그램 ID로 응용 프로그램을 시작 하는 요청을 보냅니다.

### <a name="registration"></a>등록

음성 활성화 된 응용 프로그램이 처음으로 실행 될 때 ConversationalAgent Api를 통해 해당 앱 ID 및 키워드 정보를 등록 합니다. AAR는 응용 프로그램의 키워드를 검색할 수 있도록 시스템의 하드웨어 또는 소프트웨어 키워드인 관찰자를 사용 하 여 전역 매핑의 모든 구성을 등록 합니다. 응용 프로그램은 [백그라운드 서비스](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)에도 등록 됩니다.

즉, 응용 프로그램을 한 번 실행 하 여 등록을 완료할 수 있을 때까지 음성으로 활성화할 수 없습니다.

### <a name="receiving-an-activation"></a>활성화 받기

AAR에서 요청을 받으면 백그라운드 서비스가 응용 프로그램을 시작 합니다. 응용 프로그램은 `App.xaml.cs` 고유한 이벤트 인수를 사용 하 여의 OnBackgroundActivated 수명 주기 메서드를 통해 신호를 받습니다. 이 인수는 응용 프로그램이 AAR에 의해 활성화 되었으며 키워드 확인을 시작 하도록 응용 프로그램에 지시 합니다.

응용 프로그램에서 키워드를 성공적으로 확인 하는 경우 요청을 포그라운드에서 표시 하도록 할 수 있습니다. 이 요청이 성공 하면 응용 프로그램은 UI를 표시 하 고 사용자와의 상호 작용을 계속 합니다.

AAR는 키워드를 말한 경우에도 활성 응용 프로그램에 신호를 보냅니다. 그러나에서 수명 주기 메서드를 통해 신호를 전달 하는 대신 `App.xaml.cs` ConversationalAgent api의 이벤트를 통해 신호를 보냅니다.

### <a name="keyword-verification"></a>키워드 확인

응용 프로그램을 트리거하는 키워드 관찰자는 키워드 모델을 단순화 하 여 전력 소비량을 달성 했습니다. 이렇게 하면 관찰자 키워드는 높은 전력에 영향을 주지 않으면 서 "always on"이 될 수 있지만, 키워드를 사용 하지 않은 경우에도 키워드를 검색 하는 "false 허용" 이라는 키워드가 관찰자 키워드를 사용할 수 있습니다. 음성 활성화 시스템이 백그라운드에서 응용 프로그램을 시작 하는 이유입니다. 응용 프로그램에서 사용자의 현재 세션을 중단 하기 전에 키워드를 사용 했는지 확인할 수 있는 기회를 제공 합니다. AAR는 키워드가 발견 되기 몇 초 전에 오디오를 저장 하 고 응용 프로그램에서 액세스할 수 있도록 합니다. 응용 프로그램은이를 사용 하 여 동일한 오디오에서 보다 신뢰할 수 있는 관찰자 키워드를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- **디자인 지침을 검토 합니다.** Microsoft의 [디자인 지침은](windows-voice-assistants-best-practices.md) Windows 10에서 음성 정품 인증에 대 한 최상의 환경을 제공 하는 데 필요한 주요 작업을 레이아웃 합니다.
- **시작 페이지를 방문 하세요.** 구현 소개 가이드를 통해 개발 환경 설정에서 Windows에서 음성 도우미를 구현 하기 시작 하는 단계 [를 시작 합니다](how-to-windows-voice-assistants-get-started.md) .
- **샘플 앱**체험: 이러한 기능을 어떠한 체험 [UWP Voice Assistant 샘플](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) 페이지를 방문 하 여 샘플 클라이언트를 실행 하는 단계를 따르세요.
