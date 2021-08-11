---
title: 대상 그룹에 대한 기능 단계적 롤아웃 사용
titleSuffix: Azure App Configuration
description: 대상 그룹에 대해 기능 단계적 롤아웃을 사용하는 방법 알아보기
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929687"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>대상 그룹에 대한 기능 단계적 롤아웃 사용

기능 플래그를 사용하면 애플리케이션의 기능을 동적으로 활성화하거나 비활성화할 수 있습니다. 기능 필터는 평가될 때마다 기능 플래그의 상태를 결정합니다. `Microsoft.FeatureManagement` 라이브러리에는 지정된 사용자 및 그룹 목록이나 지정된 사용자 백분율에 대해 기능 플래그를 사용하도록 설정하는 `TargetingFilter`가 포함됩니다. `TargetingFilter`는 “고정”입니다. 즉, 개별 사용자가 기능을 받고 나면 이후 모든 요청에서 해당 기능을 계속 볼 수 있습니다. `TargetingFilter`를 사용하여 데모 중에 특정 계정에 대해 기능을 사용하도록 설정하고, 다양한 그룹 또는 “링”의 사용자에게 새로운 기능을 점진적으로 배포할 수 있습니다.

이 문서에서는 Azure App Configuration과 함께 `TargetingFilter`를 사용하여 ASP.NET Core 웹 애플리케이션의 새로운 기능을 지정된 사용자와 그룹에게 배포하는 방법을 알아봅니다.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>기능 플래그 및 인증을 사용하여 웹 애플리케이션 만들기

사용자와 그룹을 기준으로 기능을 배포하려면 사용자가 로그인할 수 있는 웹 애플리케이션이 필요합니다.

1. 다음 명령을 사용하여 로컬 데이터베이스에 대해 인증하는 웹 애플리케이션을 만듭니다.

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. 빌드하고 실행한 다음, 오른쪽 위에서 **등록** 링크를 선택하여 새 사용자 계정을 만듭니다. `test@contoso.com`의 메일 주소를 사용합니다. **등록 확인** 화면에서 **여기를 클릭하여 계정을 확인합니다.** 를 선택합니다.

1. [빠른 시작: ASP.NET Core 앱에 기능 플래그 추가](./quickstart-feature-flag-aspnet-core.md)의 지침에 따라 새 웹 애플리케이션에 기능 플래그를 추가합니다.

1. App Configuration에서 기능 플래그를 토글합니다. 이 작업이 탐색 모음에서 **베타** 항목의 표시 여부를 제어하는지 유효성을 검사합니다.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>TargetingFilter를 사용하도록 웹 애플리케이션 코드 업데이트

이 지점에서 기능 플래그를 사용하여 모든 사용자에 대해 `Beta` 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. 일부 사용자에 대해 기능 플래그를 사용하도록 설정하지만 다른 사용자에 대해 사용하지 않도록 설정하려면 `TargetingFilter`를 사용하도록 코드를 업데이트합니다. 이 예제에서는 로그인한 사용자의 메일 주소를 사용자 ID로 사용하고 메일 주소의 도메인 이름 부분을 그룹으로 사용합니다. 사용자와 그룹을 `TargetingContext`에 추가합니다. `TargetingFilter`는 이 컨텍스트를 사용하여 각 요청에 대한 기능 플래그 상태를 확인합니다.

1. `Microsoft.FeatureManagement.AspNetCore` 패키지의 최신 버전으로 업데이트합니다.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. *TestTargetingContextAccessor.cs* 파일을 추가합니다.

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

1. *Startup.cs* 에서 *Microsoft.FeatureManagement.FeatureFilters* 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. `AddFeatureManagement()`를 호출한 후 *ConfigureServices* 메서드를 업데이트하여 `TargetingFilter`를 등록합니다.

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. *ConfigureServices* 메서드를 업데이트하여 이전 단계에서 생성된 `TestTargetingContextAccessor`를 서비스 컬렉션에 추가합니다. *TargetingFilter* 는 이를 사용하여 기능 플래그가 평가될 때마다 대상 컨텍스트를 결정합니다.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

전체 *ConfigureServices* 메서드는 다음과 같이 표시됩니다.

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

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>TargetingFilter를 사용하도록 기능 플래그 업데이트

1. Azure Portal에서 App Configuration 저장소로 이동하고 **기능 관리자** 를 선택합니다.

1. 빠른 시작에서 만든 ‘베타’ 기능 플래그의 상황에 맞는 메뉴를 선택합니다. **편집** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![베타 기능 플래그 편집](./media/edit-beta-feature-flag.png)

1. **편집** 화면에서 **기능 플래그 사용** 확인란을 선택합니다(선택되어 있지 않은 경우). 그런 다음, **기능 필터 사용** 확인란을 선택합니다.

1. **대상 지정** 라디오 단추를 선택합니다.

1. 다음 옵션을 선택합니다.

    - **기본 백분율**: 0
    - **그룹**: **이름** 으로 _contoso.com_ 을 입력하고 **백분율** 로 _50_ 입력
    - **사용자**: `test@contoso.com`

    기능 필터 화면이 다음과 같이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![조건부 기능 플래그](./media/feature-flag-filter-enabled.png)

    이 설정으로 인해 다음 동작이 발생합니다.

    - `test@contoso.com`이 ‘사용자’ 섹션에 나열되므로 사용자 `test@contoso.com`에 대해 기능 플래그가 항상 사용됩니다.
    - _contoso.com_ 이 ‘그룹’ 섹션에 나열되고 ‘백분율’이 _50_ 이므로 _contoso.com_ 그룹의 사용자 중 50%에 대해 기능 플래그가 사용됩니다. 
    - ‘기본 백분율’이 _0_ 으로 설정되므로 다른 모든 사용자에 대해 항상 이 기능이 사용되지 않습니다.

1. **적용** 을 선택하여 이 설정을 저장하고 **기능 관리자** 화면으로 돌아갑니다.

1. 이제 기능 플래그의 **기능 필터** 가 ‘대상 지정’으로 표시됩니다. 이 상태는 ‘대상 지정’ 기능 필터로 적용된 조건에 따라 요청별로 기능 플래그를 사용하거나 사용하지 않도록 설정됨을 나타냅니다.

## <a name="targetingfilter-in-action"></a>작동 중인 TargetingFilter

이 기능 플래그의 효과를 확인하려면 애플리케이션을 빌드하고 실행합니다. 처음에는 ‘기본 백분율’ 옵션이 0으로 설정되므로 ‘베타’ 항목이 도구 모음에 표시되지 않습니다.

이제 등록할 때 설정한 암호를 사용하여 `test@contoso.com`으로 로그인합니다. `test@contoso.com`이 대상 사용자로 지정되므로 이제 ‘베타’ 항목이 도구 모음에 표시됩니다.

다음 비디오는 이러한 동작을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![작동 중인 TargetingFilter](./media/feature-flags-targetingfilter.gif)

`@contoso.com` 메일 주소로 추가 사용자를 만들어 그룹 설정의 동작을 확인할 수 있습니다. 해당 사용자의 50%에게는 ‘베타’ 항목이 표시됩니다. 나머지 50%에게는 ‘베타’ 항목이 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기능 관리 개요](./concept-feature-management.md)