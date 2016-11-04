---
title: PowerShell을 사용하여 Azure 진단을 Application Insights에 보내기 | Microsoft Docs
description: Application Insights에 대한 파이프에 Azure 진단 자동화 구성
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2015
ms.author: awills

---
# PowerShell을 사용하여 Azure 진단을 Application Insights에 보내기
[Microsoft Azure](https://azure.com)는 [Visual Studio Application Insights](app-insights-overview.md)에 [Azure 진단을 보내도록 구성](app-insights-azure-diagnostics.md)될 수 있습니다. 진단은 Azure 클라우드 서비스 및 Azure VM과 연관됩니다. 이들 항목은 Application Insights SDK를 사용하여 앱 내부에서 보내는 원격 분석을 보완합니다. Azure에서 새 리소스 생성 과정에 대한 자동화의 일환으로 PowerShell을 사용하여 진단을 구성할 수 있습니다.

## 클라우드 서비스 배포의 일부로 진단 확장을 사용하도록 설정
`New-AzureDeployment` cmdlet에는 `ExtensionConfiguration` 매개 변수가 있으며, 진단 구성의 배열을 사용합니다. 이것은 `New-AzureServiceDiagnosticsExtensionConfig` cmdlet을 사용하여 만들 수 있습니다. 예:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## 기존 클라우드 서비스에 진단 확장을 사용하도록 설정
기존 서비스에서 `Set-AzureServiceDiagnosticsExtension`을 사용합니다.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## 현재 진단 확장 구성 가져오기
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## 진단 확장 제거
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Role 매개 변수 없이 `Set-AzureServiceDiagnosticsExtension` 또는 `New-AzureServiceDiagnosticsExtensionConfig`를 사용하여 진단 확장을 사용하도록 설정했다면, Role 매개 변수 없이 `Remove-AzureServiceDiagnosticsExtension`을 사용하여 확장을 제거할 수 있습니다. Role 매개 변수가 확장을 사용하도록 설정할 때 사용되었으면, 확장을 제거할 때도 사용되어야 합니다.

각각의 개별 역할에서 진단 확장을 제거하려면:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## 참고 항목
* [Application Insights로 Azure 클라우드 서비스 앱 모니터링](app-insights-cloudservices.md)
* [Application Insights에 Azure 진단 보내기](app-insights-azure-diagnostics.md)
* [구성 경고 자동화](app-insights-powershell-alerts.md)

<!---HONumber=AcomDC_0128_2016-->