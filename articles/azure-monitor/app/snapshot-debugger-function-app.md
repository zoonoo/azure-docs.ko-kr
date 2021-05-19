---
title: Azure Functions에서 .NET 및 .NET Core 앱용 스냅샷 디버거 사용 | Microsoft Docs
description: Azure Functions에서 .NET 및 .NET Core 앱용 스냅샷 디버거 사용
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 48eb3cf81384446a07fea69572ac16e0b80cee38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025631"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Azure Functions에서 .NET 및 .NET Core 앱용 스냅샷 디버거 사용

현재 스냅샷 디버거는 Windows 서비스 계획의 Azure Functions에서 실행되는 ASP.NET 및 ASP.NET Core 앱에 대해 작동합니다.

스냅샷 디버거를 사용할 때 기본 서비스 계층 이상에서 애플리케이션을 실행하는 것이 좋습니다.

대부분의 애플리케이션의 경우 무료 및 공유 서비스 계층에는 스냅샷을 저장할 수 있는 충분한 메모리나 디스크 공간이 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [함수 앱에서 Application Insights 모니터링 사용](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>스냅샷 디버거 사용

다른 유형의 Azure 서비스를 실행하는 경우 지원되는 다른 플랫폼에서 스냅샷 디버거를 사용하도록 설정하는 지침은 다음과 같습니다.
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 머신 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

함수 앱에서 스냅샷 디버거를 사용하도록 설정 하려면 아래에 정의된 대로 `snapshotConfiguration` 속성을 추가하여 `host.json` 파일을 업데이트하고 함수를 다시 배포해야 합니다.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

스냅샷 디버거는 Azure Functions 런타임의 일부로 미리 설치되며 기본값으로 사용하지 않도록 설정되어 있습니다.

스냅샷 디버거가 Azure Functions 런타임에 포함되므로 NuGet 패키지 또는 애플리케이션 설정을 더 추가할 필요가 없습니다.

참고로 간단한 함수 앱(.NET Core)의 경우 스냅샷 디버거를 사용하도록 설정한 후 `.csproj`, `{Your}Function.cs` 및 `host.json`이 어떻게 표시되는지 아래에 나와 있습니다.

프로젝트 csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function 클래스

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

호스트 파일

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>다른 클라우드에 대한 스냅샷 디버거 사용

현재 엔드포인트 수정이 필요한 유일한 지역은 [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights)와 [Azure 중국](/azure/china/resources-developer-guide)입니다.

다음은 미국 정부 클라우드 에이전트 엔드포인트로 업데이트된 `host.json`의 예입니다.
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

다음은 스냅샷 디버거 에이전트 엔드포인트의 지원되는 재정의입니다.

|속성    | 미국 정부 클라우드 | 중국 클라우드 |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>스냅샷 디버거 사용 안 함

함수 앱에서 스냅샷 디버거를 사용하지 않도록 설정하려면 `false`를 `snapshotConfiguration.isEnabled` 속성으로 설정하여 `host.json` 파일을 업데이트하기만 하면 됩니다.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

애플리케이션 예외를 쉽게 진단하기 위해 모든 앱에서 스냅샷 디버거를 사용하도록 설정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 예외를 트리거할 수 있는 애플리케이션에 대한 트래픽을 생성합니다. 그런 다음 스냅샷이 Application Insights 인스턴스로 전송될 때까지 10~15분 정도 기다립니다.
- Azure Portal에서 [스냅샷을 봅니다](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal).
- 함수 앱에서 사용 사례에 따라 스냅샷 디버거 구성을 사용자 지정합니다. 자세한 내용은 [host.json의 스냅샷 구성](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration)을 참조하세요.
- 스냅샷 디버거 문제 해결에 도움이 필요한 경우 [스냅샷 디버거 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.