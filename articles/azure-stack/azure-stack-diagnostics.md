---
title: Azure Stack의 진단
description: Azure Stack의 진단에 대 한 로그 파일을 수집 하는 방법
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: bd1994aca3dbbc23977b01d3511f87b5ec08b96d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251863"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack의 진단 도구

Azure Stack은 함께 작동 하 고 서로 상호 작용 하는 구성 요소의 큰 컬렉션. 이러한 모든 구성이 요소는 자신의 고유 로그가 생성 됩니다. 여러 개의 Azure Stack 구성 요소를 상호 작용에서 오는 오류에 대 한 특히 까다로운 작업을 진단할 문제를 만들 수 있습니다이. 

진단 도구 있도록 로그 수집 메커니즘 쉽고 효율적입니다. 다음 다이어그램은 Azure Stack 작업에서 수집 도구를 로그 하는 방법은:

![Azure Stack의 진단 도구](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>추적 수집기
 
추적 수집기는 기본적으로 사용 하 고 Azure Stack 구성 요소 서비스에서 모든 이벤트에 대 한 Windows (ETW 추적) 로그를 수집할 백그라운드로 지속적으로 실행 합니다. ETW 로그는 5 일 보존 기간을 사용 하 여 일반적인 로컬 공유에 저장 됩니다. 이 제한에 도달 새로 생성 될 때 가장 오래 된 파일 삭제 됩니다. 각 파일에 허용 되는 기본 최대 크기는 200mb입니다. 크기 확인을 2 분 마다 발생 경우 현재 파일 > = 200 MB 저장 되 고 새 파일이 생성 됩니다. 이벤트 세션 별로 생성 된 총 파일 크기에 또한는 8GB 제한이 됩니다. 

## <a name="log-collection-tool"></a>로그 수집 도구
 
PowerShell cmdlet **Get AzureStackLog** Azure Stack 환경에서 모든 구성 요소에서 로그를 수집할 수 있습니다. 사용자 정의 된 위치에 zip 파일에 저장 합니다. Azure Stack 기술 지원팀이 문제를 해결 하려면 로그에서 필요한 경우이 도구를 실행 하려면 요청 받을 수 있습니다.

> [!CAUTION]
> 이러한 로그는 개인 식별이 가능한 정보 (PII)를 포함할 수 있습니다. 모든 로그 파일을 공개적으로 게시 하기 전에이를 고려 합니다.
 
다음은 수집 되는 몇 가지 예제 로그 형식입니다.
*   **Azure Stack 배포 로그**
*   **Windows 이벤트 로그**
*   **Panther 로그**
*   **클러스터 로그**
*   **저장소 진단 로그**
*   **ETW 로그**

이러한 파일은 수집 되 고 추적 수집기에 의해 공유에 저장 합니다. 합니다 **Get AzureStackLog** PowerShell cmdlet을 사용 하 여 데이터를 수집 하는 데 필요한 경우 다음 있습니다.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Get-AzureStackLog on Azure Stack 통합 시스템 
통합된 시스템에서 로그 수집 도구를 실행 하려면 권한이 Privileged 끝점 (PEP) 해야 합니다. 통합된 시스템에서 로그를 수집 하 여 PEP를 사용 하 여 실행할 수 있습니다 하는 예제 스크립트는 다음과 같습니다.

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```



### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Azure Stack 개발 키트 ASDK () 시스템에서 Get-AzureStackLog를 실행 하려면
이러한 단계를 사용 하 여 Get AzureStackLog ASDK 호스트 컴퓨터에서 실행 합니다.

1. 으로 로그인 **AzureStack\CloudAdmin** ASDK 호스트 컴퓨터.
2. 관리자 권한으로 새 PowerShell 창을 엽니다.
3. 실행 합니다 **Get AzureStackLog** PowerShell cmdlet.

**예제:**

  모든 역할에 대 한 모든 로그를 수집 합니다.

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  VirtualMachines 및 BareMetal 역할에서 날짜 지난 8 시간 동안 로그 파일에 대 한 필터링을 사용 하 여 로그를 수집 합니다.
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  VirtualMachines 및 BareMetal 역할에서 날짜를 8 시간 전 및 2 시간 전 사이의 시간에 대 한 로그 파일에 대 한 필터링을 사용 하 여 로그를 수집 합니다.

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK와 통합된 시스템에 대 한 매개 변수 고려 사항

- 매개 변수 **OutputSharePath** 하 고 **OutputShareCredential** 사용자 지정 로그를 저장 하는 데 사용 된 위치.

- 합니다 **FromDate** 하 고 **ToDate** 특정 기간에 대 한 로그를 수집 하려면 매개 변수를 사용할 수 있습니다. 이러한 매개 변수를 지정 하지 않으면 로그는 기본적으로 지난 4 시간 동안 수집 됩니다.

- 사용 된 **FilterByNode** 컴퓨터 이름으로 로그를 필터링 하려면 매개 변수입니다. 예: 

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```
- 사용 된 **FilterByLogType** 매개 변수 형식으로 로그를 필터링 합니다. 파일, 공유 또는 WindowsEvent로 필터링 하도록 선택할 수 있습니다. 예: 

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
    ```
- 사용할 수는 **TimeOutInMinutes** 매개 변수 로그 컬렉션에 대 한 제한 시간을 설정 합니다. 기본적으로 150 (2.5 시간)에 설정 됩니다.
- 덤프 파일 로그 컬렉션은 기본적으로 비활성화 됩니다. 기능을 사용 하려면 사용 합니다 **IncludeDumpFile** 스위치 매개 변수입니다. 
- 현재 사용할 수는 **FilterByRole** 다음 역할에 의해 필터 로그 컬렉션에 대 한 매개 변수:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |OEM|
 |ACSDownloadService    |컴퓨팅                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
 |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |도메인                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage|
 |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
 |AzureMonitor          |게이트웨이                        |NC                             |되었습니다.|
 |BareMetal             |Healthmonitoring의               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |NRP                            |   |
 |CA                    |HRP                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>추가 고려 사항

* 명령에는 로그를 수집 하는 역할에 따라 실행 하려면 시간이 소요 됩니다. 영향을 주는 요소에는 로그 수집 및 Azure Stack 환경에서 노드 수에 대해 지정 된 기간이 포함 됩니다.
* 컬렉션 실행 로그를에서 만든 새 폴더를 확인 합니다 **OutputSharePath** 명령에 지정 된 매개 변수입니다.
* 각 역할에는 개별 zip 파일 내에서 해당 로그에 있습니다. 수집 된 로그의 크기에 따라 역할을 여러 zip 파일에 분할 로그가 있을 수 있습니다. 이러한 역할에 대 한 단일 폴더에 압축을 해제 하는 모든 로그 파일이 생성 하려는 경우 (예: 7zip) 대량에서 압축을 풀 수 있는 도구를 사용 합니다. 선택한 역할에 대 한 압축 된 파일을 모두 선택 **여기 추출**합니다. 이 병합 된 단일 폴더에 해당 역할에 대 한 모든 로그 파일 압축을 풀고 합니다.
* 이라는 파일이 **Get AzureStackLog_Output.log** 압축 된 로그 파일이 포함 된 폴더에 만들어집니다. 이 파일은 로그 수집 하는 동안 문제 해결에 사용할 수 있는 명령 출력을 로그 합니다. 로그 파일을 포함 하는 경우에 따라 `PS>TerminatingError` 안전 하 게 무시할 수 예상된 로그 파일이 한 후 누락 된 경우가 아니면 항목 컬렉션 실행 로그입니다.
* 특정 오류를 조사 하려면 둘 이상의 구성 요소에서 로그를 필요할 수 있습니다.
    -   시스템 및 인프라의 모든 Vm에 대 한 이벤트 로그에서 수집 되는 *VirtualMachines* 역할입니다.
    -   시스템 및 모든 호스트에 대 한 이벤트 로그에서 수집 되는 *BareMetal* 역할입니다.
    -   장애 조치 클러스터 및 Hyper-v 이벤트 로그에 수집 되는 *저장소* 역할입니다.
    -   ACS 로그에 수집 되는 *저장소* 하 고 *ACS* 역할.

> [!NOTE]
> 로그를 사용 하 여 플 러 딩 오지 되도록 저장소 공간의 효율적인 사용률을 확인 하는 데 필수적 이므로 수집 된 로그의 크기 및 보존 기간 제한이 적용 됩니다. 그러나 문제를 진단 하는 경우 로그는 이러한 제한으로 인해 더 이상 존재 하지 않을 경우에 따라 필요 합니다. 따라서 **좋습니다** (Azure에서 저장소 계정, 추가 온-프레미스 저장소 장치 등)는 외부 저장소 공간에 로그 오프 로드는 8 ~ 12 시간 마다 유지 1 ~ 3 개월에 대 한 따라 및 프로그램 요구 사항입니다. 또한이 저장소 위치 암호화 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[Microsoft Azure Stack 문제 해결](azure-stack-troubleshooting.md)

