---
title: 기능 필터를 사용하여 조건부 기능 플래그 사용
titleSuffix: Azure App Configuration
description: 기능 필터를 사용하여 조건부 기능 플래그를 사용하는 방법 알아보기
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602282"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>기능 필터를 사용하여 조건부 기능 플래그 사용

기능 플래그를 사용하여 애플리케이션의 기능을 활성화하거나 비활성화할 수 있습니다. 간단한 기능 플래그는 설정 또는 해제입니다. 애플리케이션은 항상 동일한 방식으로 동작합니다. 예를 들어 기능 플래그 뒤에 새 기능을 롤아웃할 수 있습니다. 기능 플래그를 사용하도록 설정하면 모든 사용자에게 새 기능이 표시됩니다. 기능 플래그를 사용하지 않도록 설정하면 새 기능이 숨겨집니다.

반면 _조건부 기능 플래그_ 를 사용하면 기능 플래그를 동적으로 사용하거나 사용하지 않도록 설정할 수 있습니다. 애플리케이션은 기능 플래그 조건에 따라 다르게 작동할 수 있습니다. 처음에 사용자의 작은 하위 집합에 새 기능을 표시하려는 경우를 가정해 보겠습니다. 조건부 기능 플래그를 사용하면 일부 사용자에 대해서는 기능 플래그를 사용하도록 설정하고 다른 사용자에 대해서는 사용하지 않도록 설정할 수 있습니다. _기능 필터_ 는 평가될 때마다 기능 플래그의 상태를 결정합니다.

`Microsoft.FeatureManagement` 라이브러리는 다음과 같은 세 가지 기능 필터를 포함합니다.

- `PercentageFilter`는 백분율을 기준으로 기능 플래그를 사용하도록 설정합니다.
- `TimeWindowFilter`는 지정된 시간 동안 기능 플래그를 사용하도록 설정합니다.
- `TargetingFilter`는 지정된 사용자 및 그룹에 대해 기능 플래그를 사용하도록 설정합니다.

[Microsoft.FeatureManagement.IFeatureFilter 인터페이스](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)를 구현하는 고유한 기능 필터를 만들 수도 있습니다.

## <a name="registering-a-feature-filter"></a>기능 필터 등록

`AddFeatureFilter` 메서드를 호출하고 원하는 기능 필터의 형식 이름을 지정하여 기능 필터를 등록합니다. 예를 들어, 다음 코드는 `PercentageFilter`를 등록합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Azure App Configuration에서 기능 필터 구성

일부 기능 필터에는 추가 설정이 있습니다. 예를 들어 `PercentageFilter`는 백분율을 기반으로 기능을 활성화합니다. 사용할 백분율을 정의하는 설정이 있습니다.

Azure App Configuration에 정의된 기능 플래그에 대해 이러한 설정을 구성할 수 있습니다. 예를 들어 `PercentageFilter`를 사용하여 웹 앱에 대한 요청의 50%에 대해 기능 플래그를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [빠른 시작: ASP.NET Core 앱에 기능 플래그 추가](./quickstart-feature-flag-aspnet-core.md)의 지침에 따라 기능 플래그를 사용하여 웹 앱을 만듭니다.

1. Azure Portal에서 구성 저장소로 이동하여 **기능 관리자** 를 클릭합니다.

1. 빠른 시작에서 만든 *베타* 기능 플래그에 대한 상황에 맞는 메뉴를 클릭합니다. **편집** 을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![베타 기능 플래그 편집](./media/edit-beta-feature-flag.png)

1. **편집** 화면에서 **기능 플래그 사용** 확인란이 아직 사용하도록 설정되어 있지 않은 경우 확인란을 선택합니다. 그런 다음 **기능 필터 사용** 확인란을 선택하고 **사용자 지정** 을 선택합니다. 

1. **이름** 필드에서 *Microsoft.Percentage* 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![기능 필터 사용](./media/feature-flag-add-filter.png)

1. 기능 필터 이름 옆에 있는 상황에 맞는 메뉴를 클릭합니다. **필터 매개 변수 편집** 을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![기능 필터 매개 변수 편집](./media/feature-flags-edit-filter-parameters.png)

1. *값* 의 **이름과** **값** 50을 입력합니다. **값** 필드는 기능 필터를 사용할 요청의 백분율을 나타냅니다.

    > [!div class="mx-imgBorder"]
    > ![기능 필터 매개 변수 설정](./media/feature-flag-set-filter-parameters.png)

1. **적용** 을 클릭하여 **기능 플래그 편집** 화면으로 돌아갑니다. 그런 다음 **적용** 을 다시 클릭하여 기능 플래그 설정을 저장합니다.

1. **기능 관리자** 페이지의 기능 플래그는 이제 *사용자 지정* 의 **기능 필터** 값을 갖게 됩니다. 

    > [!div class="mx-imgBorder"]
    > ![기능 필터 값이 ‘사용자 지정’인 기능 플래그가 나열됨](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>작동 중인 기능 필터

이 기능 플래그의 효과를 확인하려면 애플리케이션을 시작하고 브라우저에서 **새로 고침** 단추를 여러 번 누릅니다. 도구 모음에 *베타* 항목이 약 50%의 시간 동안 표시되는 것을 볼 수 있습니다. `PercentageFilter`는 요청의 하위 집합에 대해 *베타* 기능을 비활성화하므로 나머지 시간에는 숨겨집니다. 다음 비디오는 이러한 동작을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![작동 중인 TargetingFilter](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대상 그룹에 대한 기능 단계적 롤아웃 사용](./howto-targetingfilter-aspnet-core.md)
