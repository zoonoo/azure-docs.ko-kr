---
title: "Azure Stack의 진단"
description: "Azure 스택에서 진단 로그 파일을 수집 하는 방법"
services: azure-stack
author: adshar
manager: byronr
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 10/2/2017
ms.author: adshar
ms.openlocfilehash: 9b1fbbf63ddd8bac2c1a76bbcd5daca69e2513f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Azure 스택 진단 도구

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*
 
Azure 스택은 큰 컬렉션 함께 작동 하 고 서로 상호 작용 하는 구성 요소입니다. 이러한 모든 구성이 요소가 자신의 고유한 로그가 생성 됩니다. 진단 문제 여러 결과 상호 작용 Azure 스택 구성 요소에서 발생 하는 오류에 대 한 특히 까다로운 작업 크기 수 있습니다. 

로그 수집 메커니즘은 쉽고 효율적으로 우리의 진단 도구 도움이 됩니다. 다음 다이어그램에 표시 된 Azure 스택 작업에서 수집 도구를 어떻게 로그:

![로그 수집 도구](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>추적 수집기
 
추적 수집기는 기본적으로 사용 됩니다. 지속적으로 백그라운드에서 실행 하 고 Azure 스택에 구성 요소 서비스에서 모든 이벤트가 추적에 대 한 ETW (Windows) 로그를 수집 하 고 일반적인 로컬 공유에 저장 합니다. 

다음은 추적 수집기에 대 한 알아야 할 중요 한 사항입니다.
 
* 추적 수집기 기본 크기 제한 계속 실행 됩니다. 최대 허용 크기 (200MB) 각 파일에 대 한 기본값은 **하지** 크기를 구분 합니다. 크기 검사가 주기적으로 나타나는 (현재 2 분 마다) 하 고 현재 파일 경우 > = 200mb로 저장 되 고 새 파일이 생성 됩니다. 이벤트 세션 별로 생성 된 총 파일 크기에도 8GB (구성 가능)로 제한이 됩니다. 이 한도 도달 하면 가장 오래 된 파일은 새로 만들어지는 삭제 됩니다.
* 로그에 5 일의 보존 기간 제한이 있습니다. 이 제한을 구성할 수 이기도합니다. 
* 각 구성 요소는 JSON 파일을 통해 추적 구성 속성을 정의합니다. JSON 파일에 저장 됩니다 `C:\TraceCollector\Configuration`합니다. 필요에 따라 수집 된 로그의 보존 기간 및 크기 제한을 변경 하려면 이러한 파일을 편집할 수 있습니다. 이러한 파일에 변경 내용을 다시 시작 해야는 *Microsoft Azure 스택 추적 수집기* 변경 내용을 적용 하려면에 대 한 서비스입니다.
* 다음 예제는 XRP VM에서 FabricRingServices 작업에 대 한 추적 구성 JSON 파일: 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    이 매개 변수는 유지 하는 파일의 보존 기간을 제어 합니다. 이전 로그 파일이 삭제 됩니다.
* **MaxSizeInMB**

    이 매개 변수는 단일 파일에 대 한 크기 임계값을 제어합니다. 크기에 도달 하면 새.etl 파일이 생성 됩니다.
* **TotalSizeInMB**

    이 매개 변수는 이벤트 세션에서 생성 된.etl 파일의 전체 크기를 제어 합니다. 총 파일 크기가이 매개 변수 값 보다 큰 경우에 오래 된 파일이 삭제 됩니다.
  
## <a name="log-collection-tool"></a>로그 수집 도구
 
PowerShell 명령 `Get-AzureStackLog` 는 Azure 스택 환경에서 모든 구성 요소에서 로그를 수집 하는 데 사용할 수 있습니다. 사용자 정의 위치에 있는 zip 파일에 저장 합니다. 기술 지원 팀에서 문제를 해결 하려면 로그를 필요한 경우이 도구를 실행 하도록 요청 받을 수 있습니다.

> [!CAUTION]
> 이러한 로그 파일에는 개인 식별이 가능한 정보 (PII) 포함할 수 있습니다. 모든 로그 파일을 공개적으로 게시 하기 전에이를 고려 합니다.
 
다음은 수집 하는 몇 가지 예제 로그 유형입니다.
*   **Azure 스택 배포 로그**
*   **Windows 이벤트 로그**
*   **Panther 로그**

   VM 만들기 문제 문제를 해결 하려면
*   **클러스터 로그**
*   **진단 로그 저장소**
*   **ETW 로그**

이러한 파일 추적 수집기에 의해 수집 되 고 어디에서 공유에 저장 된 `Get-AzureStackLog` 를 검색 합니다.
 
**Azure 스택 개발 키트 (ASDK) 시스템에서 AzureStackLog Get을 실행 하려면**
1.  호스트에서 AzureStack\AzureStackAdmin로 로그인 합니다.
2.  관리자 권한으로 PowerShell 창을 엽니다.
3.  `Get-AzureStackLog`을 실행합니다.  

    **예**

    - 모든 역할에 대 한 모든 로그를 수집 합니다.

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - 날짜가 지난 8 시간에 대 한 로그 파일에 대 한 필터링 VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)`

    - 8 시간 전 및 2 시간 전 사이 시간 간격에 대 한 로그 파일에 대 한 필터링 된 날짜와 VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.

      `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)`

**Azure 스택에서 AzureStackLog Get을 실행 하려면 시스템을 통합 합니다.**

통합된 된 시스템에서 로그 수집 도구를 실행 하려면 액세스 권한을 가진 끝점 (PEP)가 필요 합니다. 다음은 예제 스크립트는 PEP 통합된 된 시스템에 로그를 수집 하려면 사용 하 여 실행할 수 있습니다.

```
$ip = "<IP OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDREESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- PEP에서 로그를 수집 하는 경우 지정는 `OutputPath` 매개 변수를 HLH 컴퓨터에 있는 위치 여야 합니다. 또한 위치 암호화 되어 있는지 확인 합니다.
- 매개 변수 `OutputSharePath` 및 `OutputShareCredential` 선택 사항이 며 외부 공유 폴더에 로그를 업로드 하는 경우에 사용 됩니다. 이러한 매개 변수를 사용 하 여 *또한* 를 `OutputPath`합니다. 경우 `OutputPath` 을 지정 하지 않으면 로그 컬렉션 도구 PEP VM의 시스템 드라이브를 사용 하 여 저장소에 대 한 합니다. 드라이브 공간이 제한 되기 때문에 실패 하는 스크립트를 않을 수 있습니다.
- 이전 예에서 같이 `FromDate` 및 `ToDate` 특정 기간에 대 한 로그를 수집 하도록 매개 변수를 사용할 수 있습니다. 이 제공 될 수 있습니다 시나리오 같은 통합된 된 시스템에 업데이트 패키지를 적용 한 후 로그를 수집 하는 데 편리 합니다.

**ASDK와 통합 된 시스템에 대 한 매개 변수 고려 사항:**

- 경우는 `FromDate` 및 `ToDate` 매개 변수가 지정 되지 않은 경우, 기본적으로 지난 4 시간 동안 로그가 수집 됩니다.
- 사용할 수는 `TimeOutInMinutes` 매개 변수를 로그 컬렉션에 대 한 제한 시간을 설정 합니다. 기본적으로 150 (2.5 시간)에 설정 됩니다.

- 현재 사용할 수는 `FilterByRole` 매개 변수를 사용 하 여 다음과 같은 역할 필터 로그 컬렉션:

   |   |   |   |
   | - | - | - |
   | `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
   | `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
   | `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
   | `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
   | `BRP`                     | `CA`                     | `CPI`                |
   | `CRP`                     | `DeploymentMachine`      | `DHCP`               |
   |`Domain`                   | `ECE`                    | `ECESeedRing`        |        
   | `FabricRing`              | `FabricRingServices`     | `FRP`                |
   |` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
   | `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
   | `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
   | `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
   | `SeedRingServices`        | `SLB`                    | `SQL`                |     
   | `SRP`                     | `Storage`                | `StorageController`  |
   | `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
   | `WAS`                     | `WASPUBLIC`              | `WDS`                |


몇 가지 추가 가지 참고 사항:

* 이 명령은 다소 시간이 걸릴 수 로그를 수집 하는 어떤 역할에 따라 실행 합니다. 영향을 주는 요소는 로그 수집 및 Azure 스택 환경에 있는 노드의 숫자에 대해 지정 된 기간도 포함 됩니다.
* 로그 수집 완료 된 후 확인에서 만든 새 폴더는 `-OutputPath` 명령에 지정 된 매개 변수입니다.
* 각 역할에는 개별 zip 파일 내의 해당 로그에 있습니다. 수집 된 로그의 크기에 따라 역할에는 해당 로그를 여러 개의 zip 파일에 분할 수 있을 수 있습니다. 이러한 역할에 대 한 단일 폴더에 압축을 푼 모든 로그 파일을 원하는 경우 (예: 7zip) 대량에서 압축을 풀 수 있는 도구를 사용 합니다. 역할의 경우 압축 된 파일을 모두 선택 하 고 선택 **여기 추출**합니다. 이 압축을 풀고 단일 병합 된 폴더에 해당 역할에 대 한 모든 로그 파일입니다.
* 이라는 파일 `Get-AzureStackLog_Output.log` 압축 된 로그 파일이 포함 된 폴더에 만들어집니다. 이 파일은 로그 수집 하는 동안 문제 해결에 사용할 수 있는 명령 출력의 로그입니다.
* 특정 오류를 조사 하려면 둘 이상의 구성 요소에서 로그를 필요할 수 있습니다.
    -   시스템 및 인프라의 모든 Vm에 대 한 이벤트 로그에서 수집 되는 *VirtualMachines* 역할입니다.
    -   시스템 및 모든 호스트에 대 한 이벤트 로그에서 수집 되는 *BareMetal* 역할입니다.
    -   장애 조치 클러스터 및 Hyper-v 이벤트 로그에서 수집 되는 *저장소* 역할입니다.
    -   ACS 로그에서 수집 되는 *저장소* 및 *ACS* 역할입니다.

> [!NOTE]
> 크기 및 사용 기간 제한 로그 쇄도 얻도록 하지 않는 하기 위해 저장소 공간의 효율적인 사용률을 확인 하는 데 필수적 이므로 수집 된 로그에 적용 됩니다. 그러나 문제를 진단 하는 경우 로그는 이러한 제한 때문에 더 이상 존재 하지 않을 경우에 따라 필요 합니다. 즉, **매우 권장** (Azure의 저장소 계정, 추가 온-프레미스 저장 장치 등)는 외부 저장소 공간에 로그 오프 로드는 8-12 시간 마다 유지 있는 1 ~ 3 개월 동안에 따라 프로그램 요구 사항입니다. 또한이 저장 위치 암호화 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[Microsoft Azure Stack 문제 해결](azure-stack-troubleshooting.md)
