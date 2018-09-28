---
title: Azure Monitor 메트릭 저장소의 클래식 Cloud Service에 게스트 OS 메트릭 보내기
description: Azure Monitor 메트릭 저장소의 클래식 Cloud Service에 게스트 OS 메트릭 보내기
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986917"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Azure Monitor 메트릭 저장소의 클래식 Cloud Service에 게스트 OS 메트릭 보내기

Azure Monitor [MAD(Microsoft Azure 진단) 확장](azure-diagnostics.md)을 사용하면 Virtual Machine, Cloud Service 또는 Service Fabric 클러스터의 일부로 실행되는 게스트 운영 체제(guestOS)에서 메트릭과 로그를 수집할 수 있습니다.  이 확장은 이전에 연결된 문서에 나열된 여러 다른 위치에 원격 분석을 보낼 수 있습니다.  

이 문서에서는 Azure 클래식 Cloud Services에 대한 게스트 OS 성능 메트릭을 Azure Monitor 메트릭 저장소에 보내는 프로세스에 대해 설명합니다. MAD 버전 1.11부터 표준 플랫폼 메트릭이 이미 수집된 Azure Monitor 메트릭 저장소에 메트릭을 직접 기록할 수 있습니다. 이 위치에 저장하면 플랫폼 메트릭에 사용할 수 있는 동일한 작업에 액세스할 수 있습니다.  작업에는 실시간에 가까운 경고, 차트 작성, 라우팅, REST API에서 액세스 등이 포함됩니다.  과거에는 MAD 확장에서 Azure Monitor 데이터 저장소가 아니라 Azure 저장소에 기록했습니다.  

이 문서에서 설명하는 프로세스는 Azure Cloud Services의 성능 카운터에서만 작동합니다. 다른 사용자 지정 메트릭에서는 작동하지 않습니다. 
   

## <a name="pre-requisites"></a>필수 조건

- Azure 구독의 [서비스 관리자 또는 공동 관리자](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md)여야 합니다. 

- 구독은 [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1)에 등록해야 합니다. 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1)이 설치되어 있어야 하거나 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md)을 사용할 수 있습니다. 


## <a name="provision-cloud-service-and-storage-account"></a>Cloud Service 및 저장소 계정 프로비전 

1. 클래식 Cloud Service를 만들고 배포합니다. 클래식 Cloud Service 응용 프로그램 및 배포 샘플은 [여기](../cloud-services/cloud-services-dotnet-get-started.md)서 찾을 수 있습니다. 

2. 기존 저장소 계정을 사용하거나 새 저장소 계정을 배포할 수 있습니다. 방금 만든 클래식 Cloud Service와 동일한 지역에 저장소 계정이 있는 것이 가장 좋습니다. Azure Portal에서 [저장소 계정] 리소스 블레이드로 이동하여 **키**를 선택합니다. 저장소 계정 이름과 저장소 계정 키를 적어 둡니다. 나중의 단계에서 이러한 항목이 필요합니다.

   ![Storage 계정 키](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>서비스 주체 만들기 

../azure/azure-resource-manager/resource-group-create-service-principal-portal에 있는 지침을 사용하여 Azure Active Directory 테넌트에 서비스 주체를 만듭니다. 이 프로세스를 진행하는 동안 다음 사항에 유의하세요. 
  - 로그온 URL에 대한 URL에 넣을 수 있습니다.  
  - 이 앱에 대한 새 클라이언트 비밀을 만듭니다.  
  - 나중의 단계에서 사용할 키와 클라이언트 ID를 저장합니다.  

메트릭을 내보내려는 리소스에 대한 *모니터링 메트릭 게시자* 권한을 이전 단계에서 만든 앱에 부여합니다. 앱을 사용하여 여러 리소스에 대한 사용자 지정 메트릭을 내보내려는 경우 이러한 권한은 리소스 그룹 또는 구독 수준에서 부여할 수 있습니다.  

> [!NOTE]
> 진단 확장에서는 서비스 주체를 사용하여 Azure Monitor에 대해 인증하고 클라우드 서비스에 대한 메트릭을 내보냅니다. 

## <a name="author-diagnostics-extension-configuration"></a>진단 확장 구성 작성 

MAD 진단 확장 구성 파일을 준비합니다. 이 파일은 진단 확장에서 클라우드 서비스에 대해 수집해야 하는 로그와 성능 카운터를 나타냅니다. 진단 구성 파일 샘플은 다음과 같습니다.  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

진단 파일의 "SinksConfig" 섹션에서 새 Azure Monitor 싱크를 정의합니다. 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

수집할 성능 카운터 목록이 있는 구성 파일의 섹션에 Azure Monitor 싱크를 추가합니다. 이 항목은 지정된 모든 성능 카운터가 메트릭으로 Azure Monitor에 라우팅되도록 합니다. 필요에 따라 성능 카운터를 자유롭게 추가/제거합니다. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
마지막으로, 개인 구성에서 *Azure Monitor 계정* 섹션을 추가합니다. 이전 단계에서 만든 서비스 주체 클라이언트 ID와 비밀을 입력합니다. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
이 진단 파일을 로컬로 저장합니다.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Cloud Service에 진단 확장 배포 

PowerShell을 시작하고 Azure에 로그인합니다. 

```PowerShell
Login-AzureRmAccount 
```

다음 명령을 사용하여 이전 단계에서 만든 저장소 계정 세부 정보에 대한 세부 정보를 변수에 저장합니다. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
마찬가지로 아래 명령을 사용하여 진단 파일 경로를 변수로 설정합니다. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
아래 명령을 사용하여 구성된 Azure Monitor 싱크가 있는 진단 파일을 통해 진단 확장을 클라우드 서비스에 배포합니다. 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> 진단 확장 설치의 일부로 여전히 저장소 계정을 반드시 제공해야 합니다. 진단 구성 파일에 지정된 모든 로그 및/또는 성능 카운터는 지정된 저장소 계정에 기록됩니다.  

## <a name="plot-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭 구성 

Azure Portal로 이동합니다. 

 ![Azure Portal의 메트릭](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. 왼쪽 메뉴에서 [모니터]를 클릭합니다. 

1. [모니터] 블레이드에서 [메트릭 미리 보기] 탭을 클릭합니다. 

1. 리소스 드롭다운에서 클래식 Cloud Service를 선택합니다. 

1. 네임스페이스 드롭다운에서 **azure.vm.windows.guest**를 선택합니다. 

1. 메트릭 드롭다운에서 *Memory\Committed Bytes in Use*를 선택합니다. 

차원 필터링 및 분할 기능을 사용하여 특정 역할 및 각 역할 인스턴스에서 사용하는 총 메모리를 볼 수 있습니다. 

 ![Azure Portal의 메트릭](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](metrics-custom-overview.md)에 대해 자세히 알아봅니다.



