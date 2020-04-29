---
title: Microsoft Azure 진단 확장 설치 및 구성 (WAD)
description: 사용할 수 있는 여러 도구 중 하나를 사용 하 여 볼 수 있도록 Azure Storage 계정에서 Azure 진단 데이터를 수집 하는 방법을 알아봅니다.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77672262"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Microsoft Azure 진단 확장 설치 및 구성 (WAD)
Azure 진단 확장은 게스트 운영 체제에서 모니터링 데이터를 수집 하 고 Azure virtual machines 및 기타 계산 리소스의 작업 부하를 수집 하는 Azure Monitor의 에이전트입니다. 이 문서에서는 Windows 진단 확장 설치 및 구성에 대 한 자세한 내용 및 Azure Storage 계정에 데이터가 저장 되는 방법에 대 한 설명을 제공 합니다.

진단 확장은 Azure에서 [가상 컴퓨터 확장](../../virtual-machines/extensions/overview.md) 으로 구현 되므로 리소스 관리자 템플릿, POWERSHELL 및 CLI를 사용 하 여 동일한 설치 옵션을 지원 합니다. 가상 머신 확장 설치 및 유지 관리에 대 한 자세한 내용은 [Windows 용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-windows.md) 을 참조 하세요.

## <a name="install-with-azure-portal"></a>Azure Portal를 사용 하 여 설치
Azure Portal에서 개별 가상 컴퓨터에 진단 확장을 설치 하 고 구성할 수 있습니다. 그러면 구성에서 직접 작업 하는 것과는 반대로 인터페이스를 제공 합니다. 진단 확장을 사용 하도록 설정 하면 가장 일반적인 성능 카운터 및 이벤트와 함께 기본 구성이 자동으로 사용 됩니다. 특정 요구 사항에 따라이 기본 구성을 수정할 수 있습니다.

> [!NOTE]
> Azure Event Hubs에 데이터를 보내는 것을 포함 하 여 Azure Portal를 사용 하 여 구성할 수 없는 진단 확장 설정이 있습니다. 이러한 설정에 대해 다른 구성 방법 중 하나를 사용 해야 합니다.

1. Azure Portal에서 가상 머신에 대 한 메뉴를 엽니다.
2. VM 메뉴의 **모니터링** 섹션에서 **진단 설정** 을 클릭 합니다.
3. 진단 확장을 아직 사용 하도록 설정 하지 않은 경우 **게스트 수준 모니터링 사용** 을 클릭 합니다.
4. Vm에 대 한 새 Azure Storage 계정은 VM에 대 한 리소스 그룹의 이름을 기반으로 생성 됩니다. **에이전트** 탭을 선택 하 여 VM을 다른 저장소 계정에 연결할 수 있습니다.

![진단 설정](media/diagnostics-extension-windows-install/diagnostic-settings.png)


진단 확장을 사용 하도록 설정한 후 기본 구성을 수정할 수 있습니다. 다음 표에서는 다양 한 탭에서 수정할 수 있는 옵션을 설명 합니다. 일부 옵션에는 보다 자세한 구성을 지정할 수 있는 **사용자 지정** 명령이 있습니다. 다른 설정에 대 한 자세한 내용은 [Windows 진단 확장 스키마](diagnostics-extension-schema-windows.md) 를 참조 하세요.

| 탭 | Description |
|:---|:---|
| 개요 | 현재 구성을 다른 탭에 대 한 링크와 함께 표시 합니다. |
| 성능 카운터 | 수집할 성능 카운터와 각에 대 한 샘플링 주기를 선택 합니다.  |
| 로그 | 수집할 로그 데이터를 선택 합니다. 여기에는 Windows 이벤트 로그, IIS 로그, .NET 응용 프로그램 로그 및 ETW 이벤트가 포함 됩니다.  |
| 크래시 덤프 | 다른 프로세스에 대해 크래시 덤프를 사용 하도록 설정 합니다. |
| Sinks | 데이터 싱크가 Azure Storage 외에도 대상에 데이터를 보낼 수 있도록 합니다.<br>Azure Monitor-Azure Monitor 메트릭에 성능 데이터를 보냅니다.<br>Application Insights-Application Insights 응용 프로그램으로 데이터를 보냅니다. |
| 에이전트 | 에이전트에 대해 다음 구성을 수정 합니다.<br>-저장소 계정을 변경 합니다.<br>-에이전트에 사용 되는 최대 로컬 디스크를 지정 합니다.<br>-에이전트 자체의 상태에 대 한 로그를 구성 합니다.|


> [!NOTE]
> JSON 또는 XML에서 진단 확장에 대 한 구성을 포맷할 수 있지만 Azure Portal에서 수행 되는 모든 구성은 항상 JSON으로 저장 됩니다. 다른 구성 방법과 함께 XML을 사용 하는 경우 Azure Portal를 사용 하 여 구성을 변경 하면 설정이 JSON으로 변경 됩니다.

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용 하 여 진단 확장을 배포 하는 경우 [WINDOWS VM에서 모니터링 및 진단 사용 및 Azure Resource Manager 템플릿](../../virtual-machines/extensions/diagnostics-template.md) 을 참조 하세요. 

## <a name="azure-cli-deployment"></a>Azure CLI 배포
다음 예제와 같이 [az vm extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) 을 사용 하 여 기존 가상 머신에 Azure 진단 확장을 배포 하는 데 Azure CLI 사용할 수 있습니다. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

보호 된 설정은 구성 스키마의 [privateconfig.json 요소](diagnostics-extension-schema-windows.md#privateconfig-element) 에 정의 되어 있습니다. 저장소 계정을 정의 하는 보호 된 설정 파일의 최소 예는 다음과 같습니다. 개인 설정에 대 한 전체 세부 정보는 [예제 구성](diagnostics-extension-schema-windows.md#privateconfig-element) 을 참조 하세요.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
공용 설정은 구성 스키마의 [public 요소](diagnostics-extension-schema-windows.md#publicconfig-element) 에 정의 되어 있습니다. 다음은 진단 인프라 로그, 단일 성능 카운터 및 단일 이벤트 로그 수집을 가능 하 게 하는 공용 설정 파일의 최소 예입니다. 공용 설정의 전체 정보에 대 한 [예제 구성](diagnostics-extension-schema-windows.md#publicconfig-element) 을 참조 하세요.

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
PowerShell을 사용 하 여 다음 예제와 같이 [AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) 를 사용 하 여 기존 가상 머신에 Azure 진단 확장을 배포할 수 있습니다. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

개인 설정은 [privateconfig.json 요소](diagnostics-extension-schema-windows.md#privateconfig-element)에 정의 되 고, 공용 설정은 구성 스키마의 [public 요소](diagnostics-extension-schema-windows.md#publicconfig-element) 에 정의 됩니다. 저장소 계정에 대 한 세부 정보를 개인 설정에 포함 하는 대신 AzVMDiagnosticsExtension cmdlet의 매개 변수로 지정 하도록 선택할 수도 있습니다.

다음은 진단 인프라 로그, 단일 성능 카운터 및 단일 이벤트 로그를 수집할 수 있도록 하는 구성 파일의 최소 예입니다. 개인 및 공용 설정의 전체 정보에 대 한 [예제 구성](diagnostics-extension-schema-windows.md#publicconfig-element) 을 참조 하세요. 

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
다음 표에서는 진단 확장에서 수집 된 데이터의 다양 한 유형과 테이블이 나 blob으로 저장 되었는지 여부를 보여 줍니다. 공용 구성의 [Storagetype 설정](diagnostics-extension-schema-windows.md#publicconfig-element) 에 따라 테이블에 저장 된 데이터를 blob에 저장할 수도 있습니다.


| 데이터 | 스토리지 유형 | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 테이블 | 진단 모니터 및 구성 변경 |
| WADDirectoriesTable | 테이블 | 진단 모니터가 모니터링 하는 디렉터리입니다.  IIS 로그, IIS 실패한 요청 로그 및 사용자 지정 디렉터리를 포함합니다.  Blob 로그 파일의 위치는 Container 필드에 지정되고 Blob의 이름은 RelativePath 필드에 있습니다.  AbsolutePath 필드는 Azure 가상 머신에 존재했던 파일의 이름과 위치를 나타냅니다. |
| WadLogsTable | 테이블 | 추적 수신기를 사용 하 여 코드로 작성 된 로그입니다. |
| WADPerformanceCountersTable | 테이블 | 성능 카운터. |
| WADWindowsEventLogsTable | 테이블 | Windows 이벤트 로그 |
| wad-wad-iis-failedreqlogfiles | Blob | IIS의 실패 한 요청 로그 정보를 포함 합니다. |
| wad-iis-logfiles | Blob | IIS 로그에 대 한 정보를 포함 합니다. |
| 재구성 | Blob | 진단 모니터에 의해 모니터링 되는 디렉터리 구성을 기반으로 하는 사용자 지정 컨테이너입니다.  이 Blob 컨테이너의 이름은 WADDirectoriesTable에 지정됩니다. |

## <a name="tools-to-view-diagnostic-data"></a>진단 데이터를 볼 도구
여러 도구를 사용하여 스토리지로 전송된 후 데이터를 볼 수 있습니다. 다음은 그 예입니다.

* Visual Studio의 서버 탐색기 - Microsoft Visual Studio용 Azure 도구를 설치한 경우 서버 탐색기에서 Azure Storage 노드를 사용하여 Azure Storage 계정에서 읽기 전용 Blob 및 테이블 데이터를 볼 수 있습니다. 로컬 스토리지 에뮬레이터 계정 및 Azure용으로 만든 스토리지 계정에서 데이터를 표시할 수 있습니다. 자세한 내용은 [서버 탐색기로 Storage 리소스 탐색 및 관리](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)를 참조하세요.
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, OSX 및 Linux에서 Azure Storage 데이터로 손쉽게 작업할 수 있도록 해주는 독립 실행형 앱입니다.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 에는 Azure에서 실행되는 애플리케이션으로 수집된 진단 데이터를 보고 다운로드하고 관리할 수 있는 Azure Diagnostics 관리자가 있습니다.

## <a name="next-steps"></a>다음 단계
- 모니터링 데이터를 Azure Event Hubs에 전달 하는 방법에 대 한 자세한 내용은 [Event Hubs Windows azure 진단 확장에서 데이터 전송을](diagnostics-extension-stream-event-hubs.md) 참조 하세요.
