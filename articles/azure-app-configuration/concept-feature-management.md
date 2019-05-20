---
title: Azure App Configuration 기능 관리 | Microsoft Docs
description: Azure App Configuration을 주문형으로 사용하여 애플리케이션 기능을 켜고 끄는 방법의 개요
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413778"
---
# <a name="feature-management"></a>기능 관리

일반적으로 새 애플리케이션 기능을 제공하려면 애플리케이션 자체를 다시 배포해야 합니다. 기능을 테스트하려면 기능이 표시되는 시간 또는 기능을 볼 수 있는 사람을 제어하기 위해 애플리케이션을 여러 차례 배포해야 하는 경우가 많습니다. 기능 관리는 코드 배포에서 기능 릴리스를 분리하여 요청이 있을 시 기능 가용성을 신속하게 변경할 수 있는 최신 소프트웨어 개발 방식입니다. 기능 관리는 *기능 플래그*(*기능 토글*, *기능 스위치* 등으로 부르기도 함)라는 기술을 사용하여 기능의 수명 주기를 동적으로 관리합니다. 기능 관리를 사용하면 개발자가 다음 문제를 처리할 수 있습니다.

* **코드 분기 관리**: 기능 플래그는 개발 중인 새 애플리케이션 기능을 래핑하는 데 자주 사용됩니다. 이러한 기능은 기본적으로 "숨겨져" 있습니다. 아직 개발이 완료되지 않았더라도 기능을 안전하게 제공할 수 있으며, 기능이 프로덕션 환경에서 유휴 상태로 유지됩니다. *다크 배포*라고 하는 이 방법을 사용하면 각 개발 주기가 끝날 때 모든 코드를 릴리스할 수 있습니다. 더 이상 기능을 완성할 때까지 여러 주기가 경과되어 코드 분기를 여러 주기에 걸쳐 유지 관리할 필요가 없습니다.
* **프로덕션에서 테스트**: 프로덕션 환경에서 기능 플래그를 사용하여 새 기능에 대한 초기 액세스 권한을 부여할 수 있습니다. 예를 들어 이 액세스 권한을 팀 동료 또는 내부 베타 테스터로 제한할 수 있습니다. 이러한 사용자는 테스트 환경에서 시뮬레이션된 환경 또는 부분 환경이 아닌 완전한 프로덕션 환경을 사용하게 됩니다.
* **플라이팅**: 기능 플래그를 사용하여 새 기능을 최종 사용자에게 증분 방식으로 롤아웃할 수도 있습니다. 처음에는 소수의 사용자를 대상으로 시작하고, 이후에 시간이 지나면서 구현에 대한 확신이 생기면 사용자 수를 점진적으로 늘릴 수 있습니다.
* **즉시 종료 스위치**: 기능 플래그는 새 기능을 릴리스하기 위한 기본 안전망을 제공합니다. 기능 플래그를 사용하면 애플리케이션 기능을 즉시 켤 수 있을 뿐 아니라 종료할 수도 있습니다. 애플리케이션을 다시 빌드하고 다시 배포할 필요 없이 필요할 때 신속하게 기능을 해제할 수 있습니다.
* **선택적 활성화**: 대부분의 기능 플래그는 연결된 기능이 성공적으로 릴리스될 때까지 존재하지만, 일부 기능 플래그는 오래 지속될 수 있습니다. 이러한 기능 플래그를 사용하여 사용자를 분할하고 각 그룹에 특정 기능 세트를 제공할 수 있습니다. 예를 들어 특정 웹 브라우저에서 작동하는 기능이 있다고 가정합시다. 이 브라우저의 사용자만 보고 사용할 수 있는 기능 플래그를 정의할 수 있습니다. 이 방식의 장점은 나중에 지원되는 브라우저 목록을 코드 변경 없이 쉽게 확장할 수 있다는 것입니다.

## <a name="basic-concepts"></a>기본 개념

다음은 기능 관리와 관련된 몇 가지 새로운 용어입니다.

* **기능 플래그**: 기능 플래그는 이진 상태가 *on* 또는 *off*인 변수이며 연결된 코드 블록이 있습니다. 기능 플래그의 상태에 따라 코드 블록의 실행 여부가 결정됩니다.
* **기능 관리자**: 기능 관리자는 애플리케이션에 있는 모든 기능 플래그의 수명 주기를 처리하는 애플리케이션 패키지입니다. 기능 관리자는 일반적으로 플래그 캐싱이나 상태 업데이트 같은 추가 기능을 제공합니다.
* **필터**: 필터는 기능 플래그의 상태를 평가하는 규칙입니다. 필터로 나타낼 수 있는 대표적인 예로 사용자 그룹, 디바이스 또는 브라우저 종류, 지리적 위치, 시간 범위를 들 수 있습니다.

기능 관리를 효과적으로 구현하려면 적어도 두 가지 구성 요소가 함께 작동해야 합니다. 하나는 기능 플래그를 사용하는 애플리케이션이고, 다른 하나는 기능 플래그 및 현재 상태를 저장하는 별도의 리포지토리입니다. 아래는 둘 사이의 상호 작용을 보여주는 그림입니다.

## <a name="feature-flag-usage-in-code"></a>코드에 기능 플래그 사용

애플리케이션에서 기능 플래그를 구현하는 기본 패턴은 간단합니다. 코드에서 `if` 조건문과 함께 사용되는 부울 상태 변수라고 생각하시면 됩니다.

```csharp
if (featureFlag) {
    // Run the following code
}
```

이 예에서는 `featureFlag`가 `True`로 설정되면 포함된 코드 블록이 실행되고, 그렇지 않으면 건너뜁니다. `featureFlag` 값은 다음과 같이 정적으로 설정할 수 있습니다.

```csharp
bool featureFlag = true;
```

다음과 같이 특정 규칙에 따라 평가할 수도 있습니다.

```csharp
bool featureFlag = isBetaUser();
```

약간 더 복잡한 기능 플래그 패턴으로 `else` 문이 있습니다.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

하지만 이 패턴을 기본 패턴으로 다시 작성할 수 있습니다. [ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)은 간단한 코드 패턴을 표준화할 때 얻을 수 있는 이점을 보여줍니다.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>기능 플래그 리포지토리

기능 플래그를 효과적으로 사용하려면 애플리케이션에 사용되는 모든 기능 플래그를 외부화해야 합니다. 이렇게 하면 애플리케이션 자체를 수정 및 재배포하지 않고도 기능 플래그 상태를 변경할 수 있습니다. Azure App Configuration은 기능 플래그의 중앙 집중식 리포지토리로 설계되었습니다. Azure App Configuration을 활용하여 다양한 종류의 기능 플래그를 정의하고 기능 플래그의 상태를 신속하고 자신 있게 조작할 수 있습니다. 그런 다음, 다양한 프로그래밍 언어 프레임워크를 위한 App Configuration 라이브러리를 사용하여 애플리케이션에서 이러한 기능 플래그에 쉽게 액세스할 수 있습니다. [ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)은 .NET Core App Configuration과 기능 관리 라이브러리를 완전한 솔루션으로 함께 사용하여 ASP.NET 웹 애플리케이션의 기능 플래그를 구현하는 방법을 보여줍니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱에 기능 플래그 추가](./quickstart-feature-flag-aspnet-core.md)  
