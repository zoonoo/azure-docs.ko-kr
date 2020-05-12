---
title: 환경 마다 구성 사용
titleSuffix: Azure App Configuration
description: 레이블을 사용 하 여 환경 당 구성 값을 제공 합니다.
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 465ae86c5732c9dd54ade1b7096fa8415dfca513
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118556"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>레이블을 사용 하 여 다양 한 환경에 대 한 구성 사용

많은 응용 프로그램은 환경 마다 다른 구성을 사용 해야 합니다. 응용 프로그램에 백 엔드 데이터베이스에 사용할 연결 문자열을 정의 하는 구성 값이 있다고 가정 합니다. 응용 프로그램 개발자는 프로덕션 환경에서 사용 하는 것과 다른 데이터베이스를 사용 합니다. 응용 프로그램이 개발 환경에서 프로덕션 환경으로 이동할 때 응용 프로그램에서 사용 하는 데이터베이스 연결 문자열을 변경 해야 합니다.

Azure 앱 구성에서 *레이블을* 사용 하 여 동일한 키에 대해 서로 다른 값을 정의할 수 있습니다. 예를 들어 개발 및 프로덕션에 대해 다른 값을 사용 하 여 단일 키를 정의할 수 있습니다. 앱 구성에 연결할 때 로드할 레이블을 지정할 수 있습니다.

이 기능을 설명 하기 위해 [빠른 시작: Azure 앱 구성을 사용 하 여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md) 에서 만든 웹 앱을 개발 및 프로덕션에 대 한 다른 구성 설정을 사용 하도록 수정 합니다. 계속 하기 전에 퀵 스타트를 완료 합니다.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>구성 값을 추가할 때 레이블 지정

Azure Portal에서 **구성 탐색기** 로 이동 하 여 빠른 시작에서 만든 *TestApp: Settings: 글꼴 색* 키를 찾습니다. 상황에 맞는 메뉴를 선택한 다음 **값 추가**를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![값 추가 메뉴 항목](media/labels-add-value.png)

**값 추가** 화면에서 **빨간색** 및 **개발**레이블 **값** 을 입력 합니다. **Label** **콘텐츠 형식을** 비워 둡니다. **적용**을 선택합니다.

## <a name="load-configuration-values-with-a-specified-label"></a>지정 된 레이블을 사용 하 여 구성 값 로드

기본적으로 Azure 앱 구성은 레이블 없이 구성 값만 로드 합니다. 구성 값에 대 한 레이블을 정의한 경우 앱 구성에 연결할 때 사용할 레이블을 지정 해야 합니다.

이전 섹션에서는 개발 환경에 대 한 다른 구성 값을 만들었습니다. 변수를 사용 하 여 `HostingEnvironment.EnvironmentName` 앱이 현재 실행 되는 환경을 동적으로 결정 합니다. 자세한 내용은 [ASP.NET Core에서 여러 환경 사용](/aspnet/core/fundamentals/environments)을 참조하세요.

환경 이름을 메서드에 전달 하 여 현재 환경에 해당 하는 레이블을 사용 하 여 구성 값을 로드 합니다 `Select` .

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
> 위의 코드 조각에서는 라는 환경 변수에서 앱 구성 연결 문자열을 로드 합니다 `AppConfigConnectionString` . 이 환경 변수를 올바르게 설정 해야 합니다.

`Select` 메서드가 두 번 호출됩니다. 처음에는 레이블이 없는 구성 값을 로드 합니다. 그런 다음 현재 환경에 해당 하는 레이블을 사용 하 여 구성 값을 로드 합니다. 이러한 환경 관련 값은 레이블 없이 모든 해당 값을 재정의 합니다. 모든 키에 대해 환경 특정 값을 정의할 필요는 없습니다. 키에 현재 환경에 해당 하는 레이블이 있는 값이 없는 경우에는 레이블이 없는 값이 사용 됩니다.

## <a name="test-in-different-environments"></a>다른 환경에서 테스트

`launchSettings.json`디렉터리 아래에서 파일을 엽니다 `Properties` . `config`에서 항목을 찾습니다 `profiles` . 섹션에서 `environmentVariables` 변수를로 설정 `ASPNETCORE_ENVIRONMENT` `Production` 합니다.

새 값이 설정 된 상태에서 응용 프로그램을 빌드하고 실행 합니다.

```dotnetcli
dotnet build
dotnet run
```

웹 브라우저를 사용 하 여로 이동 `http://localhost:5000` 합니다. 글꼴 색이 검정색 임을 알 수 있습니다.

![프로덕션 구성으로 실행 되는 웹 응용 프로그램](media/labels-website-prod.png)

변수를로 `launchSettings.json` 설정 하는를 업데이트 `ASPNETCORE_ENVIRONMENT` `Development` 합니다. `dotnet run`을 다시 실행합니다. 

이제 글꼴 색이 빨간색으로 표시 됩니다. 이는 응용 프로그램이 이제 레이블을 가진 값을 사용 하기 때문입니다 `TestApp:Settings:FontColor` `Development` . 다른 모든 구성 값은 프로덕션 값과 동일 하 게 유지 됩니다.

![개발 구성을 사용 하 여 실행 되는 웹 응용 프로그램](media/labels-website-dev.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core의 구성](/aspnet/core/fundamentals/configuration/)
