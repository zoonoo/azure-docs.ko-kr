---
title: 대상 그룹에 대 한 기능의 스테이징 된 롤아웃 사용
titleSuffix: Azure App Configuration
description: 대상 그룹에 대해 준비 된 기능 롤아웃을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: d1574b8a3f8cda3341c0aaf355911e2e93a7bcab
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557931"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>대상 그룹에 대 한 기능의 스테이징 된 롤아웃 사용

기능 플래그를 사용 하면 응용 프로그램의 기능을 동적으로 활성화 하거나 비활성화할 수 있습니다. 기능 필터는 평가할 때마다 기능 플래그의 상태를 결정 합니다. `Microsoft.FeatureManagement`라이브러리에는 `TargetingFilter` 지정 된 사용자 및 그룹 목록에 대 한 기능 플래그를 사용 하도록 설정 하거나 지정 된 사용자 비율을 나타내는가 포함 됩니다. `TargetingFilter` "고정" 됩니다. 즉, 개별 사용자가 기능을 받은 후에는 이후의 모든 요청에서 해당 기능을 계속 확인할 수 있습니다. `TargetingFilter`를 사용 하 여 데모 중에 특정 계정에 대해 기능을 사용 하도록 설정 하 여 다른 그룹 또는 "링" 등의 사용자에 게 새 기능을 점진적으로 롤아웃할 수 있습니다.

이 문서에서는 Azure 앱 구성으로를 사용 하 여 ASP.NET Core 웹 응용 프로그램에서 지정 된 사용자 및 그룹으로 새 기능을 배포 하는 방법을 알아봅니다 `TargetingFilter` .

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>기능 플래그 및 인증을 사용 하 여 웹 응용 프로그램 만들기

사용자 및 그룹을 기반으로 기능을 배포 하려면 사용자가 로그인 할 수 있도록 허용 하는 웹 응용 프로그램이 필요 합니다.

1. 다음 명령을 사용 하 여 로컬 데이터베이스에 대해 인증 하는 웹 응용 프로그램을 만듭니다.

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. 을 (를) 빌드하고 실행 한 다음, 오른쪽 위 모서리에서 **등록** 링크를 선택 하 여 새 사용자 계정을 만듭니다. 의 전자 메일 주소를 사용 `test@contoso.com` 합니다. **등록 확인** 화면에서 **계정을 확인 하려면 여기를 클릭** 하세요 .를 선택 합니다.

1. [빠른 시작: ASP.NET Core 앱에 기능 플래그 추가](./quickstart-feature-flag-aspnet-core.md) 의 지침에 따라 새 웹 응용 프로그램에 기능 플래그를 추가 합니다.

1. 앱 구성에서 기능 플래그를 설정/해제 합니다. 이 작업이 탐색 모음에서 **베타** 항목의 표시 여부를 제어 하는지 확인 합니다.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>TargetingFilter를 사용 하도록 웹 응용 프로그램 코드를 업데이트 합니다.

이 시점에서 기능 플래그를 사용 하 여 `Beta` 모든 사용자에 대해이 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 다른 사용자가 사용 하지 않도록 설정 하는 동안 일부 사용자에 대해 기능 플래그를 사용 하도록 설정 하려면를 사용 하도록 코드를 업데이트 `TargetingFilter` 합니다. 이 예제에서는 로그인 한 사용자의 전자 메일 주소를 사용자 ID로 사용 하 고 전자 메일 주소의 도메인 이름 부분을 그룹으로 사용 합니다. 사용자 및 그룹을에 추가 합니다 `TargetingContext` . 는 `TargetingFilter` 이 컨텍스트를 사용 하 여 각 요청에 대 한 기능 플래그의 상태를 확인 합니다.

1. 최신 버전의 패키지를 업데이트 `Microsoft.FeatureManagement.AspNetCore` 합니다.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. *TestTargetingContextAccessor.cs* 파일을 추가 합니다.

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. *Startup.cs* 에서 다음과 같이 참조를 추가 합니다 *.*

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. 에 대 한 호출에 따라 *ConfigureServices* 메서드를 업데이트 하 여를 등록 합니다 `TargetingFilter` `AddFeatureManagement()` .

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. *ConfigureServices* 메서드를 업데이트 하 여 `TestTargetingContextAccessor` 이전 단계에서 만든를 서비스 컬렉션에 추가 합니다. *Targetingfilter* 는이를 사용 하 여 기능 플래그가 평가 될 때마다 대상 컨텍스트를 결정 합니다.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

전체 *ConfigureServices* 메서드는 다음과 같습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>TargetingFilter를 사용 하도록 기능 플래그 업데이트

1. Azure Portal에서 앱 구성 저장소로 이동 하 고 **기능 관리자** 를 선택 합니다.

1. 빠른 시작에서 만든 *Beta* 기능 플래그에 대 한 상황에 맞는 메뉴를 선택 합니다. **편집** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![베타 기능 플래그 편집](./media/edit-beta-feature-flag.png)

1. **편집** 화면에서 **기능 플래그 사용** 확인란이 아직 선택 되어 있지 않은 경우 선택 합니다. 그런 다음 **기능 필터 사용** 확인란을 선택 합니다.

1. **대상 지정** 라디오 단추를 선택 합니다.

1. 다음 옵션을 선택합니다.

    - **기본 백분율** : 0
    - **그룹** : _contoso.com_ 의 **이름** 및 _50_ 의 **백분율** 을 입력 합니다.
    - **사용자** : `test@contoso.com`

    기능 필터 화면은 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![조건부 기능 플래그](./media/feature-flag-filter-enabled.png)

    이러한 설정으로 인해 다음과 같은 동작이 발생 합니다.

    - 사용자 `test@contoso.com` `test@contoso.com` 섹션에가 표시 _Users_ 되기 때문에 기능 플래그는 항상 사용자에 대해 사용 하도록 설정 됩니다.
    - _Contoso.com_ _50_ 가 _contoso.com_ 그룹에 있는 다른 사용자의 50%에 대해 기능 플래그를 사용 하도록 설정 되어 있습니다 _Groups_ . _Percentage_
    - _기본 백분율이_ _0_ 으로 설정 되어 있기 때문에 다른 모든 사용자에 대해서는이 기능을 항상 사용할 수 없습니다.

1. **적용** 을 선택 하 여 이러한 설정을 저장 하 고 **기능 관리자** 화면으로 돌아갑니다.

1. 이제 기능 플래그의 **기능 필터가** *대상* 으로 표시 됩니다. 이 상태는 *대상* 기능 필터로 적용 되는 조건에 따라 요청 별로 기능 플래그를 사용 하거나 사용 하지 않도록 설정 하는 것을 나타냅니다.

## <a name="targetingfilter-in-action"></a>TargetingFilter의 작동

이 기능 플래그의 효과를 확인 하려면 응용 프로그램을 빌드하고 실행 합니다. 처음에는 _기본 백분율_ 옵션이 0으로 설정 되어 있으므로 *베타* 항목이 도구 모음에 표시 되지 않습니다.

이제 `test@contoso.com` 등록할 때 설정한 암호를 사용 하 여로 로그인 합니다. *Beta* `test@contoso.com` 가 대상 사용자로 지정 되어 있으므로 이제 베타 항목이 도구 모음에 표시 됩니다.

다음 비디오에서는 이러한 동작을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![TargetingFilter의 작동](./media/feature-flags-targetingfilter.gif)

전자 메일 주소를 사용 하 여 추가 사용자를 만들어 `@contoso.com` 그룹 설정의 동작을 확인할 수 있습니다. 이러한 사용자 중 50%가 *베타* 항목을 볼 수 있습니다. 다른 50%는 *베타* 항목이 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기능 관리 개요](./concept-feature-management.md)