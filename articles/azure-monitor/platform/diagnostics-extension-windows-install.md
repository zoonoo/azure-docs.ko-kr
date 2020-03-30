---
title: 설치 및 Windows Azure 진단 확장 (WAD) 구성
description: 사용 가능한 여러 도구 중 하나를 사용하여 볼 수 있도록 Azure Storage 계정에서 Azure 진단 데이터를 수집하는 방법에 대해 알아봅니다.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672262"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>설치 및 Windows Azure 진단 확장 (WAD) 구성
Azure 진단 확장은 Azure 가상 시스템 및 기타 계산 리소스의 게스트 운영 체제 및 워크로드에서 모니터링 데이터를 수집하는 Azure Monitor의 에이전트입니다. 이 문서에서는 Windows 진단 확장 설치 및 구성에 대한 세부 정보와 데이터가 저장되는 방법 및 Azure Storage 계정에 대한 설명을 제공합니다.

진단 확장은 Azure에서 [가상 시스템 확장으로](../../virtual-machines/extensions/overview.md) 구현되므로 리소스 관리자 템플릿, PowerShell 및 CLI를 사용하여 동일한 설치 옵션을 지원합니다. 가상 컴퓨터 확장 설치 및 유지 관리에 대한 자세한 내용은 [Windows용 가상 컴퓨터 확장 및 기능을](../../virtual-machines/extensions/features-windows.md) 참조하십시오.

## <a name="install-with-azure-portal"></a>Azure 포털로 설치
Azure 포털의 개별 가상 컴퓨터에 진단 확장을 설치하고 구성을 직접 수행하는 것이 아니라 인터페이스를 제공하는 구성을 구성할 수 있습니다. 진단 확장을 사용하도록 설정하면 가장 일반적인 성능 카운터 및 이벤트가 있는 기본 구성이 자동으로 사용됩니다. 특정 요구 사항에 따라 이 기본 구성을 수정할 수 있습니다.

> [!NOTE]
> Azure 이벤트 허브로 데이터를 보내는 것을 포함하여 Azure 포털을 사용하여 구성할 수 없는 진단 확장 설정이 있습니다. 이러한 설정에 대해 다른 구성 방법 중 하나를 사용해야 합니다.

1. Azure 포털에서 가상 컴퓨터의 메뉴를 엽니다.
2. VM 메뉴의 **모니터링** 섹션에서 **진단 설정을** 클릭합니다.
3. 진단 확장이 아직 활성화되지 않은 경우 **게스트 수준 모니터링 을 활성화를** 클릭합니다.
4. 이름은 VM에 대한 리소스 그룹의 이름을 기반으로 하는 VM에 대해 새 Azure Storage 계정이 만들어집니다. **에이전트** 탭을 선택하여 VM을 다른 저장소 계정에 연결할 수 있습니다.

![진단 설정](media/diagnostics-extension-windows-install/diagnostic-settings.png)


진단 확장이 활성화되면 기본 구성을 수정할 수 있습니다. 다음 표에서는 다른 탭에서 수정할 수 있는 옵션에 대해 설명합니다. 일부 옵션에는 보다 자세한 구성을 지정할 수 있는 **사용자 지정** 명령이 있습니다. 다른 설정에 대한 자세한 [내용은 Windows 진단 확장 스키마를](diagnostics-extension-schema-windows.md) 참조하십시오.

| 탭 | 설명 |
|:---|:---|
| 개요 | 다른 탭에 대한 링크가 있는 현재 구성을 표시합니다. |
| 성능 카운터 | 수집할 성능 카운터와 각 성능 카운터의 샘플 속도를 선택합니다.  |
| 로그 | 수집할 로그 데이터를 선택합니다. 여기에는 Windows 이벤트 로그, IIS 로그, .NET 응용 프로그램 로그 및 ETW 이벤트가 포함됩니다.  |
| 크래시 덤프 | 다른 프로세스에 대해 크래시 덤프를 활성화합니다. |
| Sinks | Azure Storage 외에 데이터 싱크를 사용하여 대상에 데이터를 보낼 수 있습니다.<br>Azure 모니터 - 성능 데이터를 Azure 모니터 메트릭으로 보냅니다.<br>애플리케이션 인사이트 - 애플리케이션 인사이트 애플리케이션에 데이터를 보냅니다. |
| 에이전트 | 에이전트에 대한 다음 구성을 수정합니다.<br>- 저장소 계정을 변경합니다.<br>- 에이전트에 사용되는 최대 로컬 디스크를 지정합니다.<br>- 에이전트 자체의 상태를 위해 로그를 구성합니다.|


> [!NOTE]
> 진단 확장에 대한 구성은 JSON 또는 XML로 포맷할 수 있지만 Azure 포털에서 수행되는 모든 구성은 항상 JSON으로 저장됩니다. 다른 구성 방법으로 XML을 사용한 다음 Azure 포털에서 구성을 변경하면 설정이 JSON으로 변경됩니다.

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure 리소스 관리자 템플릿을 사용하여 진단 확장을 배포할 [때 Windows VM 및 Azure 리소스 관리자 템플릿을](../../virtual-machines/extensions/diagnostics-template.md) 사용하여 모니터링 및 진단 사용을 참조하세요. 

## <a name="azure-cli-deployment"></a>Azure CLI 배포
Azure CLI는 다음 예제와 같이 [az vm 확장 집합을](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) 사용하여 기존 가상 시스템에 Azure 진단 확장을 배포하는 데 사용할 수 있습니다. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

보호된 설정은 구성 스키마의 [PrivateConfig 요소에](diagnostics-extension-schema-windows.md#privateconfig-element) 정의됩니다. 다음은 저장소 계정을 정의하는 보호된 설정 파일의 최소 예제입니다. 개인 설정에 대한 자세한 내용은 [예제 구성을](diagnostics-extension-schema-windows.md#privateconfig-element) 참조하십시오.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
공용 설정은 구성 스키마의 [공용 요소에](diagnostics-extension-schema-windows.md#publicconfig-element) 정의됩니다. 다음은 진단 인프라 로그, 단일 성능 카운터 및 단일 이벤트 로그를 수집할 수 있는 공용 설정 파일의 최소 예제입니다. 공용 설정에 대한 자세한 내용은 [예제 구성을](diagnostics-extension-schema-windows.md#publicconfig-element) 참조하십시오.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell 배포
PowerShell은 다음 예제와 같이 [Set-AzVMDiagnosticsExtension을](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) 사용하여 기존 가상 시스템에 Azure 진단 확장을 배포하는 데 사용할 수 있습니다. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

개인 설정은 [PrivateConfig 요소에](diagnostics-extension-schema-windows.md#privateconfig-element)정의되고 공용 설정은 구성 스키마의 [공용 요소에](diagnostics-extension-schema-windows.md#publicconfig-element) 정의됩니다. 저장소 계정의 세부 정보를 개인 설정에 포함하지 않고 Set-AzVMDiagnosticsExtension cmdlet의 매개 변수로 지정하도록 선택할 수도 있습니다.

다음은 진단 인프라 로그, 단일 성능 카운터 및 단일 이벤트 로그를 수집할 수 있는 구성 파일의 최소 예제입니다. 개인 및 공용 설정에 대한 자세한 내용은 [예제 구성을](diagnostics-extension-schema-windows.md#publicconfig-element) 참조하십시오. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

[PowerShell을 사용하여 Windows를 실행하는 가상 머신에서 Azure Diagnostics를 사용하도록 설정](../../virtual-machines/extensions/diagnostics-windows.md)을 참조하세요.

## <a name="data-storage"></a>데이터 스토리지
다음 표에는 진단 확장에서 수집된 다양한 데이터 유형과 테이블 또는 Blob으로 저장되는지 여부가 나열되어 있습니다. 테이블에 저장된 데이터는 공용 구성의 [StorageType 설정에](diagnostics-extension-schema-windows.md#publicconfig-element) 따라 Blob에 저장할 수도 있습니다.


| 데이터 | 스토리지 유형 | 설명 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 테이블 | 진단 모니터 및 구성 변경. |
| 와디렉토리테이블 | 테이블 | 진단 모니터가 모니터링하는 디렉터리입니다.  IIS 로그, IIS 실패한 요청 로그 및 사용자 지정 디렉터리를 포함합니다.  Blob 로그 파일의 위치는 Container 필드에 지정되고 Blob의 이름은 RelativePath 필드에 있습니다.  AbsolutePath 필드는 Azure 가상 머신에 존재했던 파일의 이름과 위치를 나타냅니다. |
| 와드로그테이블 | 테이블 | 추적 수신기를 사용하여 코드로 작성된 로그입니다. |
| WADPerformanceCountersTable | 테이블 | 성능 카운터. |
| WADWindowsEventLogsTable | 테이블 | Windows 이벤트 로그 |
| 와드-아이스-데어로그파일 | Blob | IIS 실패 요청 로그의 정보를 포함합니다. |
| wad-iis-logfiles | Blob | IIS 로그에 대한 정보가 들어 있습니다. |
| "사용자 지정" | Blob | 진단 모니터에서 모니터링하는 디렉터리 구성을 기반으로 하는 사용자 지정 컨테이너입니다.  이 Blob 컨테이너의 이름은 WADDirectoriesTable에 지정됩니다. |

## <a name="tools-to-view-diagnostic-data"></a>진단 데이터를 볼 도구
여러 도구를 사용하여 스토리지로 전송된 후 데이터를 볼 수 있습니다. 예를 들어:

* Visual Studio의 서버 탐색기 - Microsoft Visual Studio용 Azure 도구를 설치한 경우 서버 탐색기에서 Azure Storage 노드를 사용하여 Azure Storage 계정에서 읽기 전용 Blob 및 테이블 데이터를 볼 수 있습니다. 로컬 스토리지 에뮬레이터 계정 및 Azure용으로 만든 스토리지 계정에서 데이터를 표시할 수 있습니다. 자세한 내용은 [서버 탐색기로 Storage 리소스 탐색 및 관리](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)를 참조하세요.
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, OSX 및 Linux에서 Azure Storage 데이터로 손쉽게 작업할 수 있도록 해주는 독립 실행형 앱입니다.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 에는 Azure에서 실행되는 애플리케이션으로 수집된 진단 데이터를 보고 다운로드하고 관리할 수 있는 Azure Diagnostics 관리자가 있습니다.

## <a name="next-steps"></a>다음 단계
- Azure [Azure 진단 확장에서 이벤트 허브로](diagnostics-extension-stream-event-hubs.md) 데이터 보내기를 참조하여 모니터링 데이터를 Azure 이벤트 허브로 전달하는 방법에 대한 자세한 내용을 참조하세요.
