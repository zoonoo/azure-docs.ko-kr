---
title: 환경별 구성 사용
titleSuffix: Azure App Configuration
description: 레이블을 사용하여 환경별 구성 값 제공
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056794"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>레이블을 사용하여 다양한 환경에 대해 서로 다른 구성을 활성화합니다.

많은 응용 프로그램은 서로 다른 환경에 대해 서로 다른 구성을 사용해야 합니다. 응용 프로그램에 백 엔드 데이터베이스에 사용할 연결 문자열을 정의하는 구성 값이 있다고 가정합니다. 응용 프로그램의 개발자는 프로덕션환경에서 사용되는 데이터베이스와 다른 데이터베이스를 사용합니다. 응용 프로그램에서 사용하는 데이터베이스 연결 문자열은 응용 프로그램이 개발에서 프로덕션으로 이동할 때 변경되어야 합니다.

Azure 앱 구성에서 *레이블을* 사용하여 동일한 키에 대해 서로 다른 값을 정의할 수 있습니다. 예를 들어 *개발* 및 프로덕션 에 대해 서로 다른 값을 가진 단일 키를 정의할 수 *있습니다.* 앱 구성에 연결할 때 로드할 레이블을 지정할 수 있습니다.

이 기능을 보여 주기 위해 [빠른 시작: Azure 앱 구성을 사용하여 ASP.NET 핵심 앱을 만들어](./quickstart-aspnet-core-app.md) 개발 및 프로덕션용에 대해 서로 다른 구성 설정을 사용하도록 만든 웹 앱을 수정합니다. 계속하기 전에 빠른 시작을 완료하십시오.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>구성 값을 추가할 때 레이블 지정

Azure 포털에서 구성 **탐색기로** 이동하여 빠른 시작에서 만든 *TestApp:Settings:FontColor* 키를 찾습니다. 컨텍스트 메뉴를 선택한 다음 **값 추가**를 클릭합니다.

> [!div class="mx-imgBorder"]
> ![값 메뉴 항목 추가](media/labels-add-value.png)

값 **추가** 화면에서 **빨간색** **값과** **개발** **레이블을** 입력합니다. **콘텐츠 유형을** 비워 둡니다. **적용**을 선택합니다.

## <a name="loading-configuration-values-with-a-specified-label"></a>지정된 레이블로 구성 값 로드

기본적으로 Azure 앱 구성은 레이블없이 구성 값만 로드합니다. 구성 값에 대한 레이블을 정의한 경우 앱 구성에 연결할 때 사용할 레이블을 지정해야 합니다.

마지막 *섹션에서는 개발* 환경에 대해 다른 구성 값을 만들었습니다. 변수를 `HostingEnvironment.EnvironmentName` 사용하여 앱이 현재 실행 중인 환경을 동적으로 결정합니다. 자세한 내용은 [ASP.NET Core에서 여러 환경 사용](/aspnet/core/fundamentals/environments)을 참조하세요.

`Select` 환경 이름을 메서드에 전달하여 현재 환경에 해당하는 레이블로 구성 값을 로드합니다.

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> 위의 코드 코드 조각은 응용 프로그램 구성 연결 `AppConfigConnectionString`문자열을 라는 환경 변수에서 로드 합니다. 이 환경 변수가 제대로 설정되어 있는지 확인합니다.

`Select` 메서드가 두 번 호출됩니다. 처음에는 레이블없이 구성 값을 로드합니다. 그런 다음 현재 환경에 해당하는 레이블로 구성 값을 로드합니다. 이러한 환경별 값은 레이블 없이 해당 값을 재정의합니다. 모든 키에 대해 환경별 값을 정의할 필요는 없습니다. 키에 현재 환경에 해당하는 레이블이 있는 값이 없는 경우 레이블이 없는 값이 사용됩니다.

## <a name="testing-in-different-environments"></a>다양한 환경에서의 테스트

다른 구성 값을 테스트하려면 `launchSettings.json` 디렉터리 `Properties` 에서 파일을 엽니다. `config` 아래에서 항목을 `profiles`찾습니다. `environmentVariables` 섹션에서 변수를 `ASPNETCORE_ENVIRONMENT` 로 설정합니다. `Production`

새 값을 설정하면 응용 프로그램을 빌드하고 실행합니다.

```dotnetcli
dotnet build
dotnet run
```

웹 브라우저를 사용하여 `http://localhost:5000`로 이동합니다. 글꼴 색상이 검은색임을 알 수 있습니다.

![프로덕션 구성으로 실행되는 웹 응용 프로그램](media/labels-website-prod.png)

이제 `launchSettings.json` 업데이트하여 `ASPNETCORE_ENVIRONMENT` 변수를 `Development`로 설정합니다. `dotnet run`을 다시 실행합니다. 이제 글꼴 색상이 빨간색으로 표시됩니다. 이는 응용 프로그램에 레이블이 있는 `TestApp:Settings:FontColor` 값을 `Development` 사용하기 때문입니다. 다른 모든 구성 값은 생산 값과 동일하게 유지됩니다.

![개발 구성으로 실행되는 웹 응용 프로그램](media/labels-website-dev.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core의 구성](/aspnet/core/fundamentals/configuration/)
