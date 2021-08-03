---
title: UI 라이브러리 개요
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services UI 라이브러리에 대해 알아봅니다.
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 05/11/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4176bdf61cafdf50aef71e76b832a0e669f1535c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468807"
---
# <a name="ui-library-overview"></a>UI 라이브러리 개요

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> UI 라이브러리에 대한 자세한 문서는 [UI 라이브러리 스토리 북](https://azure.github.io/communication-ui-library)을 참조하세요. 여기에서 추가 개념 설명서, 빠른 시작 및 예를 찾을 수 있습니다.

Azure Communication Services - UI 라이브러리를 사용하면 Azure Communication Services를 사용하여 최신 통신 사용자 환경을 쉽게 빌드할 수 있습니다. 애플리케이션에 배치할 수 있는 프로덕션 준비 UI 구성 요소의 라이브러리를 제공합니다.

- **복합** - 이러한 구성 요소는 일반적인 통신 시나리오를 구현하는 턴키 솔루션입니다.
  화상 통화 또는 채팅 환경을 애플리케이션에 빠르게 추가할 수 있습니다.
  복합 구성 요소는 UI 구성 요소를 사용하여 빌드된 오픈 소스 상위 순서 구성 요소입니다.

- **UI 구성 요소** - 이러한 구성 요소는 사용자 지정 통신 환경을 구축하는 데 사용할 수 있는 오픈 소스 구성 요소입니다.
  구성 요소는 환경을 구축하기 위해 결합할 수 있는 통화 및 채팅 기능 모두에 제공됩니다.

이러한 UI 클라이언트 라이브러리는 모두 [Microsoft의 Fluent 디자인 언어](https://developer.microsoft.com/fluentui/) 및 자산을 사용합니다. Fluent UI는 UI 라이브러리에 대한 기본 계층을 제공하며 Microsoft 제품에서 적극적으로 사용됩니다.

UI 구성 요소와 함께 UI 라이브러리는 통화 및 채팅을 위한 상태 저장 클라이언트 라이브러리를 노출합니다.
이 클라이언트는 특정 상태 관리 프레임워크에 독립적이며 Redux 또는 React 컨텍스트와 같은 일반 상태 관리자와 통합할 수 있습니다.
이 상태 저장 클라이언트 라이브러리는 UI 구성 요소와 함께 사용하여 UI 구성 요소가 데이터를 렌더링하는 데 필요한 속성 및 메서드를 전달할 수 있습니다. 자세한 내용은 [상태 저장 클라이언트 개요](https://azure.github.io/communication-ui-library/?path=/story/stateful-client-what-is-stateful--page)를 참조하세요.

## <a name="installing-ui-library"></a>UI 라이브러리 설치

상태 저장 클라이언트는 `@azure/communication-react` 패키지의 일부로 발견됩니다. 

```bash
npm i --save @azure/communication-react
```

## <a name="composites-overview"></a>복합 구성 요소 개요

복합 구성 요소는 Azure Communication Services를 사용하여 일반적인 통신 시나리오에 대한 턴키 솔루션을 제공하는 UI 구성 요소로 구성된 상위 수준 구성 요소입니다.
개발자는 Azure Communication Services 액세스 토큰 및 통화 또는 채팅에 필요한 구성을 사용하여 복합 구성 요소를 쉽게 인스턴스화할 수 있습니다.

| 복합    | 사용 사례  | 
| ------------ | ---------- |
| [CallComposite](https://azure.github.io/communication-ui-library/?path=/docs/composites-callcomposite--basic-example) | 사용자가 통화를 시작하거나 참가할 수 있는 통화 환경입니다. 환경 내에서 사용자가 디바이스를 구성하고, 비디오를 사용하여 통화에 참여하고, 비디오를 켜는 참가자를 포함하여 다른 참가자를 볼 수 있습니다. Teams Interop의 경우 사용자가 잠깐 대기할 수 있는 기능이 포함되어 있습니다. |
| [ChatComposite](https://azure.github.io/communication-ui-library/?path=/docs/composites-chatcomposite--basic-example)    | 사용자가 메시지를 보내고 받을 수 있는 채팅 환경입니다. 입력, 읽기, 들어오고 나가는 참가자와 같은 스레드 이벤트는 채팅 스레드의 일부로 사용자에게 표시됩니다.                                                                                                                          |

## <a name="ui-component-overview"></a>UI 구성 요소 개요

비디오 타일을 그리드에 붙여 원격 참가자를 소개하는 것부터 애플리케이션 사양에 맞게 구성 요소를 구성하는 것까지 개발자가 통신 환경을 구성하는 데 사용할 수 있는 순수 UI 구성 요소입니다.
UI 구성 요소는 사용자 지정을 지원하여 구성 요소에 적절한 느낌을 제공하고 애플리케이션 브랜딩 및 스타일과 일치하는지 확인합니다.

| Area    | 구성 요소    | Description       |
| ------- | ------------ | ----------------- |
| 호출 | [그리드 레이아웃](https://azure.github.io/communication-ui-library/?path=/story/ui-components-gridlayout--grid-layout-component)                | 비디오 타일을 NxN 그리드로 구성하는 그리드 구성 요소                                            |
|         | [비디오 타일](https://azure.github.io/communication-ui-library/?path=/story/ui-components-videotile--video-tile-component)                   | 사용 가능한 경우 비디오 스트리밍을 표시하는 구성 요소와 그렇지 않은 경우 기본 정적 구성 요소        |
|         | [컨트롤 막대](https://azure.github.io/communication-ui-library/?path=/story/ui-components-controlbar--control-bar-component)                | 음소거 또는 공유 화면과 같은 특정 통화 작업에 연결하는 DefaultButtons를 구성하는 컨테이너 |
|         | [VideoGallery](https://azure.github.io/communication-ui-library/?path=/story/ui-components-video-gallery--video-gallery)                                           | 참가자가 추가되면 동적으로 변경되는 턴키 비디오 갤러리 구성 요소               |
| 채팅    | [메시지 스레드](https://azure.github.io/communication-ui-library/?path=/story/ui-components-messagethread--message-thread-component)       | 채팅 메시지, 시스템 메시지 및 사용자 지정 메시지를 렌더링하는 컨테이너                          |
|         | [보내기 상자](https://azure.github.io/communication-ui-library/?path=/story/ui-components-sendbox--send-box-component)                         | 불연속 송신 단추가 있는 텍스트 입력 구성 요소                                                   |
|         | [메시지 상태 표시기](https://azure.github.io/communication-ui-library/?path=/story/ui-components-message-status-indicator--message-status-indicator)        | 보낸 메시지의 상태를 표시하는 다중 상태 읽기 수신 구성 요소                                   |
|         | [입력 표시기](https://azure.github.io/communication-ui-library/?path=/story/ui-components-typingindicator--typing-indicator-component) | 스레드에 적극적으로 입력하는 참가자를 렌더링하는 텍스트 구성 요소                      |
| 일반  | [참가자 항목](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participantitem--participant-item-component) | 아바타와 표시 이름을 포함하여 통화 또는 채팅 참가자를 렌더링하기 위한 공통 구성 요소            |
|         | [참가자 목록](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participant-list--participant-list)                                 | 아바타 및 표시 이름을 포함하여 통화 또는 채팅 참가자 목록을 렌더링하는 공통 구성 요소       |


## <a name="what-ui-artifact-is-best-for-my-project"></a>내 프로젝트에 가장 적합한 UI 아티팩트

다음 요구 사항을 이해하면 올바른 클라이언트 라이브러리를 선택하는 데 도움이 됩니다.

- **원하는 사용자 지정이 얼마나 많은가요?** Azure Communication Services 핵심 클라이언트 라이브러리에는 UX가 없으며, 원하는 UX를 구축할 수 있도록 설계되었습니다. UI 라이브러리 구성 요소는 축소된 사용자 지정 비용으로 UI 자산을 제공합니다.
- **대상 플랫폼은 무엇인가요?** 플랫폼마다 기능이 다릅니다.

UI Library의 기능 가용성에 대한 자세한 내용은 [여기서 제공](https://azure.github.io/communication-ui-library/?path=/story/use-cases--page)되지만, 주요 장단점은 아래에 요약되어 있습니다.

| 클라이언트 라이브러리/SDK  | 구현 복잡성 | 사용자 지정 기능 | 호출 | 채팅 | [Teams 상호 운용성](../teams-interop.md) |
| --------------------- | ------------------------- | --------------------- | ------- | ---- | ----------------------------------------------------------------------------------------------------- |
| 복합 구성 요소  | 낮음                       | 낮음                   | ✔       | ✔    | ✔                                                                                                     |
| 기본 구성 요소       | 중간                    | 중간                | ✔       | ✔    | ✔                                                                                                     |
| 핵심 클라이언트 라이브러리 | 높음                      | 높음                  | ✔       | ✔    | ✔                                                                                                     |

> [!div class="nextstepaction"]
> [UI 라이브러리 스토리북 방문](https://azure.github.io/communication-ui-library)
