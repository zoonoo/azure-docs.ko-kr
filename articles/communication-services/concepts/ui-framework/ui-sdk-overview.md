---
title: Azure Communication Services UI 프레임워크 개요
titleSuffix: An Azure Communication Services conceptual document
description: Azure Communication Services UI 프레임워크에 대해 알아봅니다.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b6f9987c21ecdcf0e1b5358486312dceb26c8b82
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008556"
---
# <a name="azure-communication-services-ui-framework"></a>Azure Communication Services UI 프레임워크

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="미리 빌드된 복합 구성 요소 및 사용자 지정 복합 구성 요소의 비교":::

Azure Communication Services UI 프레임워크를 사용하면 최신 통신 사용자 환경을 쉽게 구축할 수 있습니다. 애플리케이션에 배치할 수 있는 프로덕션 준비 UI 구성 요소의 라이브러리를 제공합니다.

- **복합 구성 요소** - 이러한 구성 요소는 일반적인 통신 시나리오를 구현하는 턴키 솔루션입니다. 화상 통화 또는 채팅 환경을 애플리케이션에 빠르게 추가 할 수 있습니다. 복합 구성 요소는 기본 구성 요소를 사용하여 빌드된 오픈 소스 구성 요소입니다.
- **기본 구성 요소** - 이러한 구성 요소는 사용자 지정 통신 환경을 구축하는 데 사용할 수 있는 오픈 소스 구성 요소입니다. 구성 요소는 환경을 구축하기 위해 결합할 수 있는 통화 및 채팅 기능 모두에 제공됩니다. 

이러한 UI 클라이언트 라이브러리는 모두 [Microsoft의 Fluent 디자인 언어](https://developer.microsoft.com/fluentui/) 및 자산을 사용합니다. Fluent UI는 Microsoft 제품 전체에서 전투 테스트를 거친 UI 프레임워크에 대한 기본 계층을 제공합니다.

## <a name="differentiating-components-and-composites"></a>**구성 요소 및 복합 구성 요소 차별화**

**기본 구성 요소** 는 핵심 Azure Communication Services 클라이언트 라이브러리를 기반으로 하며, 핵심 클라이언트 라이브러리 초기화, 비디오 렌더링, 음소거, 비디오 켜기/끄기 등에 대한 사용자 컨트롤 제공과 같은 기본 작업을 구현합니다. 이러한 **기본 구성 요소** 를 사용하여 미리 빌드된 프로덕션 준비 통신 구성 요소를 사용하여 사용자 고유의 사용자 지정 레이아웃 환경을 구축할 수 있습니다.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="UI 프레임워크 구성 요소에 대한 개요":::

**복합 구성 요소** 는 여러 **기본 구성 요소** 를 결합하여 더 완벽한 통신 환경을 만듭니다. 이러한 상위 수준 구성 요소를 기존 앱에 쉽게 통합하여 처음부터 구축하지 않고도 완전한 기능을 갖춘 최신 통신 환경이 될 수 있습니다. 개발자는 주변 환경을 구축하고 원하는 대로 앱으로 이동하는 데 집중할 수 있으며, 복합 구성 요소에 대한 통신 복잡성을 그대로 유지할 수 있습니다.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="UI 프레임워크 복합 구성 요소에 대한 개요":::

## <a name="what-ui-framework-is-best-for-my-project"></a>프로젝트에 가장 적합한 UI 프레임워크는 무엇인가요?

다음 요구 사항을 이해하면 올바른 클라이언트 라이브러리를 선택하는 데 도움이 됩니다.

- **원하는 사용자 지정이 얼마나 많은가요?** Azure Communication Services 핵심 클라이언트 라이브러리에는 UX가 없으며, 원하는 UX를 구축할 수 있도록 설계되었습니다. UI 프레임워크 구성 요소는 축소된 사용자 지정 비용으로 UI 자산을 제공합니다.
- **모임 기능이 필요한가요?** 모임 시스템에는 현재 흐릿한 배경 및 올린 손과 같은 핵심 Azure Communication Services 클라이언트 라이브러리에서 사용할 수 없는 몇 가지 고유한 기능이 있습니다.
- **대상 플랫폼은 무엇인가요?** 플랫폼마다 기능이 다릅니다.

다양한 UI SDK의 기능 가용성에 대한 자세한 내용은 [여기서 제공](ui-sdk-features.md)되지만, 주요 장단점은 아래에 요약되어 있습니다.

|클라이언트 라이브러리/SDK|구현 복잡성|    사용자 지정 기능|  호출| 채팅| [Teams 상호 운용성](./../teams-interop.md)
|---|---|---|---|---|---|---|
|복합 구성 요소|낮음|낮음|✔|✔|✕
|기본 구성 요소|중간|중간|✔|✔|✕
|핵심 클라이언트 라이브러리|높음|높음|✔|✔ |✔

## <a name="cost"></a>비용

Azure UI 프레임워크를 사용하는 경우 추가 Azure 비용 또는 계량이 없습니다. 기본 서비스의 사용량에 대해서만 비용을 지불하며, 동일한 통화, 채팅 및 PSTN 미터를 사용합니다.

## <a name="supported-use-cases"></a>지원되는 사용 사례

통화:

- 그룹 ID를 사용하여 Azure Communication Services 통화에 참가

채팅:

- 스레드 ID를 사용하여 Azure Communication Services 채팅에 참가

## <a name="supported-identities"></a>지원되는 ID:

Azure Communication Services ID는 UI 프레임워크를 초기화하고 서비스를 인증하는 데 필요합니다. 인증에 대한 자세한 내용은 [인증](../authentication.md) 및 [액세스 토큰](../../quickstarts/access-tokens.md)을 참조하세요.


## <a name="recommended-architecture"></a>권장 아키텍처 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="클라이언트-서버 아키텍처가 포함된 UI 프레임워크 권장 아키텍처":::

복합 및 기본 구성 요소는 Azure Communication Services 액세스 토큰을 사용하여 초기화됩니다. 액세스 토큰은 사용자가 관리하는 신뢰할 수 있는 서비스를 통해 Azure Communication Services에서 확보해야 합니다. 자세한 내용은 [빠른 시작: 액세스 토큰 만들기](../../quickstarts/access-tokens.md) 및 [신뢰할 수 있는 서비스 자습서](../../tutorials/trusted-service-tutorial.md)를 참조하세요.

또한 이러한 클라이언트 라이브러리에는 참가할 통화 또는 채팅에 대한 컨텍스트가 필요합니다. 사용자 액세스 토큰과 마찬가지로 이 컨텍스트는 사용자 고유의 신뢰할 수 있는 서비스를 통해 클라이언트에 배포해야 합니다. 아래 목록에는 운영하는 데 필요한 초기화 및 리소스 관리 기능이 요약되어 있습니다.

| Contoso 책임                                 | UI 프레임워크 책임                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Azure에서 액세스 토큰 제공                    | 지정된 액세스 토큰을 통과하여 구성 요소 초기화        |
| 새로 고침 함수 제공                                 | 개발자 제공 함수를 사용하여 액세스 토큰 새로 고침          |
| 통화 또는 채팅을 위한 참가 정보 검색/전달          | 통화 및 채팅 정보를 전달하여 구성 요소 초기화 |
| 사용자 지정 데이터 모델에 대한 사용자 정보 검색/전달 | 렌더링할 구성 요소에 사용자 지정 데이터 모델 전달          |
