---
title: Azure Functions에서 .NET 및 .NET Core 앱에 대 한 스냅숏 디버거 사용 Microsoft Docs
description: Azure Functions에서 .NET 및 .NET Core 앱에 대 한 스냅숏 디버거 사용
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 3060bd6ea8d7fbc4a4bf005b84cd07d420987ab6
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696420"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Azure Functions에서 .NET 및 .NET Core 앱에 대 한 스냅숏 디버거 사용

스냅숏 디버거 현재 Windows 서비스 계획에서 Azure Functions 실행 되는 ASP.NET 및 ASP.NET Core 앱에 대해 작동 합니다.

스냅숏 디버거를 사용 하는 경우 기본 서비스 계층 이상에서 응용 프로그램을 실행 하는 것이 좋습니다.

대부분의 응용 프로그램의 경우 무료 및 공유 서비스 계층에는 스냅숏을 저장할 수 있는 충분 한 메모리 나 디스크 공간이 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [함수 앱에서 Application Insights 모니터링 사용](https://docs.microsoft.com/azure/azure-functions/configure-monitoring#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>스냅숏 디버거 사용

다른 유형의 Azure 서비스를 실행 하는 경우 지원 되는 다른 플랫폼에서 스냅숏 디버거를 사용 하도록 설정 하는 지침은 다음과 같습니다.
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

함수 앱에서 스냅숏 디버거를 사용 하도록 설정 하려면 `host.json` 아래에 정의 된 대로 속성을 추가 하 여 파일을 업데이트 하 `snapshotConfiguration` 고 함수를 다시 배포 해야 합니다.

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

스냅숏 디버거은 Azure Functions 런타임의 일부로 미리 설치 되며 기본적으로 사용 하지 않도록 설정 되어 있습니다.

스냅숏 디버거 Azure Functions 런타임에 포함 되므로 추가 NuGet 패키지 또는 응용 프로그램 설정을 추가할 필요가 없습니다.

간단한 함수 앱 (.NET Core)에 대 한 참조와 마찬가지로 `.csproj` ,를 `{Your}Function.cs` 사용 하도록 설정한 후에는, 및가 사용 된 후에 어떻게 표시 되는 `host.json` 스냅숏 디버거.

프로젝트 .csproj

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

## <a name="disable-snapshot-debugger"></a>스냅숏 디버거 사용 안 함

함수 앱에서 스냅숏 디버거를 사용 하지 않도록 설정 하려면를 `host.json` 속성으로 설정 하 여 파일을 업데이트 하기만 하면 `false` `snapshotConfiguration.isEnabled` 됩니다.

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

응용 프로그램 예외를 쉽게 진단 하기 위해 모든 앱에서 스냅숏 디버거 사용 하도록 설정 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 예외를 트리거할 수 있는 응용 프로그램에 대 한 트래픽을 생성 합니다. 그런 다음 스냅숏이 Application Insights 인스턴스로 전송 될 때까지 10 ~ 15 분 정도 기다립니다.
- Azure Portal에서 [스냅숏을 봅니다](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) .
- 함수 앱에서 사용 사례에 따라 스냅숏 디버거 구성을 사용자 지정 합니다. 자세한 내용은 [host.js의 스냅숏 구성](https://docs.microsoft.com/azure/azure-functions/functions-host-json#applicationinsightssnapshotconfiguration)을 참조 하세요.
- 스냅숏 디버거 문제를 해결 하는 데 도움이 필요 하면 [스냅숏 디버거 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)을 참조 하세요.
