---
title: 기능 필터를 사용하여 사용자 하위 집합에 대한 피쳐 사용
titleSuffix: Azure App Configuration
description: 기능 필터를 사용하여 사용자 하위 집합에 대한 기능을 활성화하는 방법 알아보기
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057010"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>기능 필터를 사용하여 사용자 하위 집합에 대한 피쳐 사용

기능 플래그를 사용하면 응용 프로그램의 기능을 활성화하거나 비활성화할 수 있습니다. 간단한 피쳐 플래그가 켜지거나 꺼져 있습니다. 응용 프로그램은 항상 동일한 방식으로 행동합니다. 예를 들어 피처 플래그 뒤에 새 피쳐를 롤아웃할 수 있습니다. 피쳐 플래그를 사용하도록 설정하면 모든 사용자에게 새 기능이 표시됩니다. 피쳐 플래그를 사용하지 않도록 설정하지 않도록 설정되면 새 피쳐가 숨겨집니다.

반대로 _조건부 피쳐 플래그를_ 사용하면 피쳐 플래그를 동적으로 활성화하거나 사용하지 않도록 설정할 수 있습니다. 기능 플래그 기준에 따라 응용 프로그램이 다르게 행동할 수 있습니다. 처음에 새 기능을 작은 사용자 하위 집합에 표시한다고 가정합니다. 조건부 피쳐 플래그를 사용하면 일부 사용자에 대해 기능 플래그를 사용하도록 설정하고 다른 사용자에 대해 사용하지 않도록 설정할 수 있습니다. _피쳐 필터는_ 평가할 때마다 피쳐 플래그의 상태를 결정합니다.

라이브러리에는 `Microsoft.FeatureManagement` 두 가지 피쳐 필터가 포함되어 있습니다.

- `PercentageFilter`을 사용하면 백분율에 따라 피쳐 플래그가 활성화됩니다.
- `TimeWindowFilter`을 사용하면 지정된 기간 동안 피쳐 플래그가 활성화됩니다.

[Microsoft.FeatureManagement.IFeatureFilter 인터페이스를](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)구현하는 고유한 기능 필터를 만들 수도 있습니다.

## <a name="registering-a-feature-filter"></a>피쳐 필터 등록

피쳐 필터의 이름을 `AddFeatureFilter` 지정하여 메서드를 호출하여 피쳐 필터를 등록합니다. 예를 들어 다음 코드는 `PercentageFilter`다음과 같은 코드 레지스터를 등록합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Azure 앱 구성에서 기능 필터 구성

일부 기능 필터에는 추가 설정이 있습니다. 예를 들어 `PercentageFilter` 백분율에 따라 피쳐를 활성화합니다. 사용할 백분율을 정의하는 설정이 있습니다.

Azure 앱 구성에 정의된 기능 플래그에 대해 이러한 설정을 구성할 수 있습니다. 예를 들어 다음 단계를 `PercentageFilter` 수행하여 웹 앱에 대한 요청의 50%에 대한 기능 플래그를 사용하도록 설정합니다.

1. 빠른 시작의 지침에 [따라: 기능 플래그를 사용하여 ASP.NET 핵심 앱에 기능 플래그를 추가하여](./quickstart-feature-flag-aspnet-core.md) 기능 플래그가 있는 웹 앱을 만듭니다.

1. Azure 포털에서 구성 저장소로 이동하여 **기능 관리자**를 클릭합니다.

1. 빠른 시작에서 만든 *베타* 기능 플래그의 컨텍스트 메뉴를 클릭합니다. **편집**을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![베타 기능 플래그 편집](./media/edit-beta-feature-flag.png)

1. **편집** 화면에서 아직 선택되지 않은 경우 라디오 **켜기** 단추를 선택합니다. 그런 다음 **필터 추가** 단추를 클릭합니다. (라디오 **On** 켜기 버튼의 레이블이 **조건부**읽기로 변경됩니다.)

1. **키** 필드에 *Microsoft.Percentage을*입력합니다.

    > [!div class="mx-imgBorder"]
    > ![피쳐 필터 추가](./media/feature-flag-add-filter.png)

1. 피처 필터 키 옆의 컨텍스트 메뉴를 클릭합니다. **매개변수 편집을 클릭합니다.**

    > [!div class="mx-imgBorder"]
    > ![피쳐 필터 매개변수 편집](./media/feature-flag-edit-filter-parameters.png)

1. 텍스트 상자가 표에 표시되도록 **이름** 헤더 아래에 마우스를 가져갑니다. *값* **이름과** 값 **50을** 입력합니다. **값** 필드는 기능 필터를 사용하도록 설정하는 요청의 백분율을 나타냅니다.

    > [!div class="mx-imgBorder"]
    > ![피쳐 필터 매개변수 설정](./media/feature-flag-set-filter-parameters.png)

1. **적용을** 클릭하여 **편집 기능 플래그** 화면으로 돌아갑니다. 그런 다음 다시 **적용을** 클릭하여 피처 플래그 설정을 저장합니다.

1. 이제 피쳐 플래그의 **상태가** *조건부로*나타납니다. 이 상태는 기능 필터에서 적용한 조건에 따라 요청별로 기능 플래그를 활성화하거나 사용하지 않도록 설정됨을 나타냅니다.

    > [!div class="mx-imgBorder"]
    > ![조건부 피쳐 플래그](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>기능 필터 가 작동하는 경우

이 기능 플래그의 효과를 보려면 응용 프로그램을 실행하고 브라우저에서 **새로 고침** 버튼을 여러 번 누릅니다. *베타* 아이템이 도구 모음에 약 50% 표시되는 것을 볼 수 있습니다. 요청의 하위 집합에 대한 *베타* `PercentageFilter` 기능을 비활성화하기 때문에 나머지 시간은 숨김이 됩니다. 다음 비디오에서는 이 동작이 작동하는 것을 보여 주며 이 동작을 보여 주며, 이 동작이 보여 주어도 됩니다.

> [!div class="mx-imgBorder"]
> ![백분율 작업 필터](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기능 관리 개요](./concept-feature-management.md)
