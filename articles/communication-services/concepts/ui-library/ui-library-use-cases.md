---
title: UI 라이브러리 사용 사례
titleSuffix: An Azure Communication Services concept document
description: UI 라이브러리 및 통신 환경을 빌드하는 데 도움이 되는 방법에 대해 알아봅니다.
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 05/11/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 143cdce2cd177c73fb4da76dca3be71f2730bbd9
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468766"
---
# <a name="ui-library-use-cases"></a>UI 라이브러리 사용 사례

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> UI 라이브러리에 대한 자세한 문서는 [UI 라이브러리 스토리 북](https://azure.github.io/communication-ui-library)을 참조하세요. 여기에서 추가 개념 설명서, 빠른 시작 및 예를 찾을 수 있습니다.


UI 라이브러리는 통화 및 채팅 환경에서 많은 사례를 지원합니다.
이러한 기능은 UI 구성 요소 및 복합 구성 요소를 통해 사용할 수 있습니다.
복합 구성 요소의 경우 이러한 기능은 복합 구성 요소가 애플리케이션에 통합될 때에서 직접 빌드되고 노출됩니다.
UI 구성 요소에 대해 이러한 기능은 UI 기능과 기본 상태 저장 라이브러리의 조합을 통해 노출됩니다.
이러한 기능을 완벽하게 활용하려면 상태 저장 통화 및 채팅 클라이언트 라이브러리와 함께 UI 구성 요소를 사용하는 것이 좋습니다.

## <a name="calling-use-cases"></a>통화 사용 사례

| Area                | 사용 사례                                              |
| ------------------- | ------------------------------------------------------ |
| 호출 유형          | Teams 모임 참가                                     |
|                     | 그룹 ID를 사용하여 Azure Communication Services 통화에 참가   |
| [Teams 상호 운용성](../teams-interop.md)      | 통화 로비                                             |
|                     | 전사 및 기록 경고 배너               |
| 통화 제어       | 통화 음소거/음소거 해제                                       |
|                     | 통화 시 비디오 켜기/끄기                                   |
|                     | 화면 공유                                         |
|                     | 통화 종료                                               |
| 참가자 갤러리 | 원격 참가자가 표에 표시됩니다.              |
|                     | 로컬 사용자에 대한 통화에서 사용할 수 있는 비디오 미리 보기 |
|                     | 비디오를 끌 때 사용할 수 있는 기본 아바타            |
|                     | 참가자 갤러리에 표시되는 공유 화면 콘텐츠 |
| 통화 구성  | 마이크 디바이스 관리                           |
|                     | 카메라 디바이스 관리                               |
|                     | 스피커 디바이스 관리                              |
|                     | 사용자가 비디오를 확인하는 데 사용할 수 있는 로컬 미리 보기        |
| 참가자        | 참가자 명단                                     |

## <a name="chat-use-cases"></a>채팅 사용 사례

| Area         | 사용 사례                                        |
| ------------ | ------------------------------------------------ |
| 채팅 유형   | Teams 모임 채팅 참가                        |
|              | Azure Communication Services 채팅 스레드에 참가 |
| 채팅 작업 | 채팅 메시지 보내기                                |
|              | 채팅 메시지 받기                             |
| 채팅 이벤트  | 입력 표시기                                |
|              | 읽음 확인                                     |
|              | 참가자 추가/제거                        |
|              | 채팅 제목이 변경됨                               |
| 참가자 | 참가자 명단                               |

## <a name="supported-identities"></a>지원되는 ID

Azure Communication Services ID는 상태 저장 클라이언트 라이브러리를 초기화하고 서비스를 인증하는 데 필요합니다.
인증에 대한 자세한 내용은 [인증](../authentication.md) 및 [액세스 토큰](../../quickstarts/access-tokens.md?pivots=programming-language-javascript)을 참조하세요.

## <a name="teams-interop-use-case"></a>Teams Interop 사용 사례

[Teams Interop](../teams-interop.md) 시나리오의 경우 개발자는 UI 라이브러리 구성 요소를 사용하여 Azure Communication Services를 통해 Teams 모임에 참가할 수 있습니다.
Teams Interop을 사용하도록 설정하기 위해 개발자는 통화 및 채팅 복합 구성 요소를 직접 사용하거나 UI 구성 요소를 사용하여 사용자 지정 환경을 빌드할 수 있습니다.
통화 및 채팅을 모두 사용하여 애플리케이션을 사용하도록 설정하는 경우 참가자가 통화에 응답할 때까지 채팅 클라이언트를 초기화할 수 없다는 점을 기억해야 합니다.
완료되면 채팅 클라이언트를 초기화하여 모임 채팅 스레드에 조인할 수 있습니다.
지침은 아래 다이어그램을 참조하세요.

:::image type="content" source="../media/teams-interop-pattern.png" alt-text="통화 및 채팅을 위한 Team Interop 패턴":::

UI 구성 요소를 사용하여 Teams Interop 환경을 제공하는 경우 UI 라이브러리는 환경의 주요 부분에 대한 예를 제공합니다.
예를 들면 다음과 같습니다.
- [로비 예](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--lobby): 참가자가 통화가 허용되기를 기다리는 샘플 로비입니다.
- [규정 준수 배너](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--compliance-banner): 통화가 녹음되고 있는지 여부를 사용자에게 보여 주는 샘플 배너입니다.
- [Teams 테마](https://azure.github.io/communication-ui-library/?path=/story/examples-themes--teams): UI 라이브러리를 Microsoft Teams처럼 보이게 만드는 샘플 테마입니다.


## <a name="customization"></a>사용자 지정

UI 라이브러리는 개발자가 애플리케이션의 모양과 느낌에 맞게 구성 요소를 수정하는 패턴을 노출합니다.
이러한 기능은 복합 구성 요소와 UI 구성 요소를 구분하는 주요 영역입니다. 여기서 복합 구성 요소는 더 간단한 통합 환경을 위해 사용자 지정 옵션을 덜 제공합니다.

| 사용 사례                                            | 복합 | UI 구성 요소 |
| --------------------------------------------------- | ---------- | ------------- |
| Fluent 기반 테마                                | X          | X             |
| 환경 레이아웃 구성 가능                     |            | X             |
| CSS 스타일은 스타일 속성을 수정하는 데 사용 가능  |            | X             |
| 아이콘 교체 가능                               |            | X             |
| 참가자 갤러리 레이아웃 수정 가능          |            | X             |
| 통화 컨트롤 레이아웃 수정 가능                 | X          | X             |
| 데이터 모델을 삽입하여 사용자 메타데이터 수정 가능 | X          | X             |

## <a name="observability"></a>가시성

UI 라이브러리의 분리된 상태 관리 아키텍처의 일부로 개발자는 상태 저장 통화 및 채팅 클라이언트에 직접 액세스할 수 있습니다.

개발자는 상태 저장 클라이언트에 연결하여 상태를 읽고, 이벤트를 처리하고, 동작을 재정의하여 UI 구성 요소에 전달할 수 있습니다.

| 사용 사례                                  | 복합 | UI 구성 요소 |
| ----------------------------------------- | ---------- | ------------- |
| 통화/채팅 클라이언트 상태에 액세스할 수 있습니다.    | X          | X             |
| 클라이언트 이벤트에 액세스하고 처리할 수 있습니다. | X          | X             |
| UI 이벤트에 액세스하고 처리할 수 있습니다.     | X          | X             |

## <a name="recommended-architecture"></a>권장 아키텍처

:::image type="content" source="../media/ui-library-architecture.png" alt-text="UI 라이브러리 권장 클라이언트-서버 아키텍처":::

복합 및 기본 구성 요소는 Azure Communication Services 액세스 토큰을 사용하여 초기화됩니다. 액세스 토큰은 사용자가 관리하는 신뢰할 수 있는 서비스를 통해 Azure Communication Services에서 확보해야 합니다. 자세한 내용은 [빠른 시작: 액세스 토큰 만들기](../../quickstarts/access-tokens.md?pivots=programming-language-javascript) 및 [신뢰할 수 있는 서비스 자습서](../../tutorials/trusted-service-tutorial.md)를 참조하세요.

또한 이러한 클라이언트 라이브러리에는 참가할 통화 또는 채팅에 대한 컨텍스트가 필요합니다. 사용자 액세스 토큰과 마찬가지로 이 컨텍스트는 사용자 고유의 신뢰할 수 있는 서비스를 통해 클라이언트에 배포해야 합니다. 아래 목록에는 운영하는 데 필요한 초기화 및 리소스 관리 기능이 요약되어 있습니다.

| Contoso 책임                                 | UI 라이브러리 책임                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Azure에서 액세스 토큰 제공                          | 지정된 액세스 토큰을 통과하여 구성 요소 초기화        |
| 새로 고침 함수 제공                                 | 개발자 제공 함수를 사용하여 액세스 토큰 새로 고침          |
| 통화 또는 채팅을 위한 참가 정보 검색/전달          | 통화 및 채팅 정보를 전달하여 구성 요소 초기화 |
| 사용자 지정 데이터 모델에 대한 사용자 정보 검색/전달 | 렌더링할 구성 요소에 사용자 지정 데이터 모델 전달          |

## <a name="platform-support"></a>플랫폼 지원

| SDK)    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
| ------ | ------------------ | -------------------- | -------- | -------- | -------- | ---------- |
| UI SDK | Chrome\*, 새 Edge | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*이전 두 릴리스 외에도 최신 버전의 Chrome이 지원됩니다.

\*\*Safari 버전 13.1 이상이 지원됩니다. Safari macOS에 대한 보내는 비디오는 아직 지원되지 않지만 iOS에서 지원됩니다. 보내는 화면 공유는 데스크톱 iOS에서만 지원됩니다.

## <a name="accessibility"></a>액세스 가능성

설계상의 접근성은 Microsoft 제품에 대한 원칙입니다.
UI 라이브러리는 모든 UI 구성 요소가 완전히 액세스할 수 있도록 하기 위해 이 원칙을 따릅니다.
공개 미리 보기 중에 UI 라이브러리는 UI 구성 요소에 접근성 기능을 계속 개선하고 추가합니다.
일반 공급되는 UI 라이브러리의 접근성에 대한 자세한 내용을 추가해야 합니다.

## <a name="localization"></a>지역화

지역화는 전 세계적으로 다른 언어를 사용하는 사람들이 사용할 수 있는 제품을 만드는 핵심입니다.
UI 라이브러리는 RTL과 같은 일부 언어 및 기능에 대한 상자 지원을 제공합니다.
개발자는 UI 라이브러리에 사용할 자체 지역화 파일을 제공할 수 있습니다.
이러한 지역화 기능은 일반 공급보다 앞서 추가될 예정입니다.

> [!div class="nextstepaction"]
> [UI 라이브러리 스토리북 방문](https://azure.github.io/communication-ui-library)
