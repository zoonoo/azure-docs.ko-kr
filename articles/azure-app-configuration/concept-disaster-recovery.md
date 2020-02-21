---
title: Azure 앱 구성 복원 력 및 재해 복구
description: Azure 앱 구성을 사용 하 여 복원 력 및 재해 복구를 구현 하는 방법에 대해 설명 합니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523767"
---
# <a name="resiliency-and-disaster-recovery"></a>복원력 및 재해 복구

현재 Azure App Configuration은 지역 서비스입니다. 각 구성 저장소는 특정 Azure 지역에 만들어집니다. 지역별 가동 중단은 해당 지역의 모든 저장소에 영향을 줍니다. App Configuration은 다른 지역으로의 자동 장애 조치(failover)를 제공하지 않습니다. 이 문서에서는 Azure 지역 전체에서 여러 구성 저장소를 사용하여 애플리케이션의 지역 복원력을 향상시키는 방법에 대한 일반적인 지침을 제공합니다.

## <a name="high-availability-architecture"></a>고가용성 아키텍처

지역 간 중복성을 실현하려면 서로 다른 지역에 여러 개의 App Configuration 저장소를 만들어야 합니다. 이 설정을 사용하면 액세스할 수 없는 기본 저장소를 대체하는 하나 이상의 추가 구성 저장소가 애플리케이션에 있게 됩니다. 다음 다이어그램에서는 애플리케이션과 해당 기본 및 보조 구성 저장소 간의 토폴로지를 보여줍니다.

![지역 중복 저장소](./media/geo-redundant-app-configuration-stores.png)

애플리케이션은 기본 저장소와 보조 저장소 모두에서 해당 구성을 병렬로 로드합니다. 이렇게 하면 구성 데이터를 성공적으로 가져올 가능성이 높아집니다. 두 저장소의 데이터를 동기화 된 상태로 유지 하는 일을 담당 합니다. 다음 섹션에서는 응용 프로그램에 지역 복원 력을 구축 하는 방법을 설명 합니다.

## <a name="failover-between-configuration-stores"></a>구성 저장소 간 장애 조치

기술적으로는 애플리케이션에서 장애 조치(failover)를 실행하지 않습니다. 두 App Configuration 저장소에서 동일한 구성 데이터 세트를 동시에 검색하려고 합니다. 먼저 보조 저장소에서 로드한 다음, 기본 저장소에서 로드하도록 코드를 정렬합니다. 이 방법은 기본 저장소의 구성 데이터를 사용할 수 있을 때마다 이를 우선적으로 적용하도록 합니다. 다음 코드 조각에서는 .NET Core에서이 배열을 구현할 수 있는 방법을 보여 줍니다.

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

`optional` 매개 변수가 `AddAzureAppConfiguration` 함수에 전달되었음에 주의하세요. `true`로 설정되면 이 매개 변수는 함수에서 구성 데이터를 로드할 수 없는 경우 애플리케이션에서 계속 실패하지 않도록 방지합니다.

## <a name="synchronization-between-configuration-stores"></a>구성 저장소 간 동기화

지역 중복 구성 저장소의 데이터 세트가 모두 동일해야 합니다. App Configuration에서 **내보내기** 함수를 사용하여 요청에 따라 데이터를 기본 저장소에서 보조 저장소로 복사할 수 있습니다. 이 함수는 Azure Portal과 CLI 모두를 통해 사용할 수 있습니다.

Azure Portal에서 다음 단계를 수행하면 다른 구성 저장소에 대한 변경을 푸시할 수 있습니다.

1. **가져오기/내보내기** 탭으로 이동하고 **내보내기** > **App Configuration** > **대상** > **리소스 선택**을 선택합니다.

1. 열리는 새 블레이드에서 보조 저장소의 구독, 리소스 그룹 및 리소스 이름을 지정한 다음 **적용**을 선택 합니다.

1. UI가 업데이트되어 보조 저장소로 내보내려는 구성 데이터를 선택할 수 있습니다. 기본 시간 값을 그대로 유지한 채 **원본 레이블** 및 **레이블 지정**을 모두 동일한 값으로 설정할 수 있습니다. **적용**을 선택합니다.

1. 모든 구성 변경에 대해 위의 단계를 반복합니다.

이 내보내기 프로세스를 자동화하려면 Azure CLI를 사용합니다. 다음 명령에서는 단일 구성 변경을 기본 저장소에서 보조 저장소로 내보내는 방법을 보여줍니다.

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 App Configuration 런타임 동안 지역 복원력을 달성하기 위해 애플리케이션을 보강하는 방법에 대해 알아보았습니다. 또한 빌드하거나 배포할 때 App Configuration의 구성 데이터를 포함시킬 수 있습니다. 자세한 내용은 [CI/CD 파이프라인과 통합](./integrate-ci-cd-pipeline.md)을 참조하세요.
