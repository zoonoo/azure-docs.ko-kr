---
title: Miscrosoft Azure 진단을 사용하여 Azure Service Fabric 이벤트 집계 | Microsoft Docs
description: Azure Service Fabric 클러스터 모니터링 및 진단을 위해 MAD를 사용하여 이벤트를 집계 및 수집하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: ede128d23ca73dc46f2d4dc4b1dd4b1f83a2bc3f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Miscrosoft Azure 진단을 사용하여 이벤트 집계 및 수집
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 클러스터나 해당 클러스터에서 실행 중인 응용 프로그램 및 서비스의 문제를 분석하고 해결하는 데 도움이 됩니다.

로그를 업로드 및 수집하는 방법 중 하나는 MAD(Microsoft Azure 진단) 확장을 사용하는 것입니다. 이 확장을 사용하면 Azure Storage에 로그를 업로드하고 Azure Application Insights 또는 Event Hubs에 로그를 보낼 수 있습니다. 또한 외부 프로세스를 사용하여 저장소의 이벤트를 읽고 [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) 또는 기타 로그 구문 분석 솔루션 등의 분석 플랫폼 제품에 배치할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이러한 도구는 이 문서의 일부 작업을 수행하는 데 사용됩니다.

* [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md)(Azure Cloud Services와 관련이 있지만 여러 좋은 정보와 예 제공)
* [Azure 리소스 관리자](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager 클라이언트](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager 템플릿](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>로그 및 이벤트 원본

### <a name="service-fabric-platform-events"></a>Service Fabric 플랫폼 이벤트
[이 문서](service-fabric-diagnostics-event-generation-infra.md)의 설명대로 Service Fabric은 WAD를 사용하여 모니터링 및 진단 데이터를 저장소 테이블이나 다른 곳에 보내도록 다음 채널을 쉽게 구성할 수 있는 몇 가지 기본 제공 로깅 채널을 소개합니다.
  * 작업 이벤트: Service Fabric 플랫폼이 수행하는 상위 수준 작업입니다. 응용 프로그램 및 서비스 만들기, 노드 상태 변경, 업그레이드 정보 등을 예로 들 수 있습니다. 이러한 이벤트를 ETW(Windows용 이벤트 추적) 로그로 내보냅니다.
  * [Reliable Actors 프로그래밍 모델 이벤트](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 프로그래밍 모델 이벤트](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>응용 프로그램 이벤트
 응용 프로그램 및 서비스 코드에서 발생하며 Visual Studio에서 제공하는 EventSource 도우미 클래스를 사용하여 작성된 이벤트입니다. 응용 프로그램에서 EventSource 로그를 작성하는 방법에 대한 자세한 내용은 [로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)를 참조하세요.

## <a name="deploy-the-diagnostics-extension"></a>진단 확장 배포
로그를 수집하는 첫 단계는 서비스 패브릭 클러스터의 각 VM에 진단 확장을 배포하는 것입니다. 진단 확장은 각 VM에서 로그를 수집하여 사용자가 지정하는 저장소 계정에 업로드합니다. 단계는 Azure Portal 또는 Azure Resource Manager 사용 여부에 따라 약간 달라집니다. 또한 배포가 클러스터 생성의 일부인지 아니면 이미 있는 클러스터에 대한 것인지에 따라서도 단계가 달라집니다. 각 시나리오에 대한 단계를 살펴보겠습니다.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Azure Portal을 통해 클러스터 만들기의 일환으로 진단 확장 배포
클러스터 만들기의 일부로 클러스터의 VM에 진단 확장을 배포하려면 다음 이미지에 표시된 진단 설정 패널을 사용합니다. [진단]이 **켜기**(기본 설정)로 설정되어 있는지 확인합니다. 클러스터를 만든 후에는 포털을 사용하여 이러한 설정을 변경할 수 없습니다.

![클러스터를 만들기 위해 포털에서 Azure 진단 설정](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

포털을 사용하여 클러스터를 만드는 경우 클러스터를 만들기 위해 **확인을 클릭하기 전에** 템플릿을 다운로드하는 것이 좋습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터 설정](service-fabric-cluster-creation-via-arm.md)을 참조하세요. 일부는 포털을 사용하여 변경할 수 없으므로 나중에 변경하려면 템플릿이 필요합니다.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Azure 리소스 관리자를 사용하여 클러스터 만들기의 일환으로 진단 확장 배포
리소스 관리자를 사용하여 클러스터를 만들려면 클러스터를 만들기 전에 진단 구성 JSON을 전체 클러스터 Resource Manager 템플릿에 추가해야 합니다. Resource Manager 템플릿 샘플의 일부로 진단 구성이 추가된 샘플 5VM 클러스터 Resource Manager 템플릿이 제공됩니다. Azure 샘플 갤러리의 [진단 Resource Manager 템플릿 샘플이 포함된 5노드 클러스터](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)에서 샘플을 볼 수 있습니다.

Resource Manager 템플릿에서 진단 설정을 표시하려면 azuredeploy.json 파일을 열고 **IaaSDiagnostics**를 검색합니다. 이 템플릿을 사용하여 클러스터를 만들려면 이전 링크에 제공된 **Azure에 배포** 버튼을 선택합니다.

또는 리소스 관리자 샘플을 다운로드하여 변경한 후 Azure PowerShell 창에서 `New-AzureRmResourceGroupDeployment` 명령을 사용하여 수정된 템플릿으로 클러스터를 만들 수 있습니다. 명령에 전달할 매개 변수는 다음 코드를 참조하세요. PowerShell을 사용하여 리소스 그룹을 배포하는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](../azure-resource-manager/resource-group-template-deploy.md) 문서를 참조하세요.

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>기존 클러스터에 진단 확장 배포
진단이 배포되지 않은 기존 클러스터가 있거나 기존 구성을 수정하려는 경우 기존 클러스터를 추가하거나 업데이트할 수 있습니다. 기존 클러스터를 만드는 데 사용되는 Resource Manager 템플릿을 수정하거나 앞의 설명대로 포털에서 템플릿을 다운로드합니다. 다음 작업을 수행하여 template.json 파일을 수정합니다.

리소스 섹션에 추가하여 새 저장소 리소스를 템플릿에 추가합니다.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 저장소 계정 정의 바로 뒤 `supportLogStorageAccountName`과 `vmNodeType0Name` 사이의 매개 변수 섹션에 추가합니다. 자리 표시자 텍스트 *storage account name goes here*를 저장소 계정의 이름으로 바꿉니다.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
그런 다음 확장 배열 내에 다음 코드를 추가하여 template.json의 `VirtualMachineProfile` 섹션을 업데이트합니다. 삽입되는 위치에 따라 시작 부분 또는 끝 부분에 쉼표를 추가해야 합니다.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

template.json 파일을 설명대로 수정한 후에는 Resource Manager 템플릿을 다시 게시합니다. 템플릿을 내보낸 후 deploy.ps1 파일을 실행하면 템플릿이 다시 게시됩니다. 배포 후에는 **ProvisioningState**가 **성공**했는지 확인합니다.

> [!TIP]
> 클러스터에 컨테이너를 배포하려는 경우, **WadCfg > DiagnosticMonitorConfiguration** 섹션에 이 컨테이너를 추가하는 방식으로 WAD를 통해 Docker 통계를 선택할 수 있습니다.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

## <a name="log-collection-configurations"></a>로그 컬렉션 구성
컬렉션에 대한 추가 채널의 로그도 사용할 수 있습니다. 여기서는 Azure에서 실행되는 클러스터용 템플릿에서 수행할 수 있는 가장 일반적인 몇 가지 구성을 소개합니다.

* 작동 채널 - 기본: 기본적으로 사용하도록 설정됩니다. 노드에 대해 발생하는 이벤트, 배포되는 새 응용 프로그램, 업그레이드 롤백 등을 포함하여 Service Fabric 및 클러스터에서 수행하는 상위 수준 작업입니다. 이벤트 목록은 [작동 채널 이벤트](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-diagnostics-event-generation-operational)를 참조하세요.
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* 작동 채널 - 상세: 기본 작동 채널의 모든 내용과 상태 보고서 및 부하 분산 결정이 포함됩니다. 이러한 이벤트는 시스템이나 코드에서 [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) 또는 [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx)와 같은 상태 또는 부하 보고 API를 사용하여 생성됩니다. Visual Studio의 Diagnostic Event Viewer에서 이 이벤트를 보려면 ETW 공급자 목록에 "Microsoft-ServiceFabric:4:0x4000000000000008"을 추가합니다.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* 데이터 및 메시징 채널 - 기본: 상세 작동 채널 로그 외에 메시징(현재 ReverseProxy만) 및 데이터 경로에 생성된 중요 로그 및 이벤트입니다. 이러한 이벤트는 요청 처리 실패와 ReverseProxy 및 처리된 요청의 기타 중요 문제입니다. **포괄적인 로깅을 위한 권장 사항입니다**. Visual Studio의 진단 이벤트 뷰어에서 이 이벤트를 보려면 ETW 공급자 목록에 “Microsoft-ServiceFabric:4:0x4000000000000010”을 추가합니다.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* 데이터 및 메시징 채널 - 상세: 클러스터 및 상세 작동 채널의 메시징 데이터에서 중요하지 않은 모든 로그를 포함하는 상세 채널입니다. 모든 역방향 프록시 이벤트의 문제 해결에 대한 자세한 내용은 [역방향 프록시 진단 가이드](service-fabric-reverse-proxy-diagnostics.md)를 참조하세요.  Visual Studio의 진단 이벤트 뷰어에서 이 이벤트를 보려면 ETW 공급자 목록에 “Microsoft-ServiceFabric:4:0x4000000000000020”을 추가합니다.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>이 채널에는 많은 이벤트가 있으므로 이 상세 채널에서 이벤트 수집을 설정하면 많은 추적이 빠르게 생성되어 저장소 용량을 소비할 수 있습니다. 반드시 필요한 경우에만 이 옵션을 설정하세요.


포괄적인 로깅을 위한 권장 사항인 **기본 데이터 및 메시징 채널**을 사용하도록 설정하려면, 템플릿의 `WadCfg`에 있는 `EtwManifestProviderConfiguration`은 다음과 같습니다.

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387928",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>새 EventSource 채널에서 수집

배포하려는 새 응용 프로그램을 나타내는 새 EventSource 채널에서 로그를 수집하도록 진단을 업데이트하려면 기존 클러스터에 대한 이전 설명과 동일한 단계를 수행해야 합니다.

새 EventSource 채널의 항목을 추가하도록 template.json 파일의 `EtwEventSourceProviderConfiguration` 섹션을 업데이트한 후 `New-AzureRmResourceGroupDeployment` PowerShell 명령을 사용하여 구성 업데이트를 적용합니다. 이벤트 원본의 이름은 ServiceEventSource.cs 파일을 생성한 Visual Studio에서 코드의 일부로 정의됩니다.

예를 들어 이벤트 원본의 이름을 Eventsource라고 지정한 경우 My-Eventsource의 이벤트를 MyDestinationTableName이라는 테이블에 배치하는 다음 코드를 추가합니다.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

성능 카운터 또는 이벤트 로그를 수집하려면 [Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단이 포함된 Windows 가상 머신 만들기](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 제공된 예제를 사용하여 Resource Manager 템플릿을 수정합니다. 그런 다음 Resource Manager 템플릿을 다시 게시합니다.

## <a name="collect-performance-counters"></a>성능 카운터 수집

클러스터에서 성능 메트릭을 수집하려면 클러스터에 대한 Resource Manager 템플릿에서 "WadCfg > DiagnosticMonitorConfiguration"에 성능 카운터를 추가합니다. 특정 성능 카운터를 수집하는 `WadCfg` 수정에 대한 단계는 [WAD를 사용하여 성능 모니터링](service-fabric-diagnostics-perf-wad.md)을 참조하세요. 수집을 권장하는 성능 카운터 목록에 대해서는 [Service Fabric 성능 카운터](service-fabric-diagnostics-event-generation-perf.md)를 참조하세요.
  
아래 섹션에 설명된 대로 Application Insights 싱크를 사용하고 있을 때 이러한 메트릭을 Application Insights에 표시하려면 위에 표시된 대로 "sinks" 섹션에 싱크 이름을 추가해야 합니다. Application Insights 리소스에 개별적으로 구성된 성능 카운터를 자동으로 보냅니다.


## <a name="send-logs-to-application-insights"></a>Application Insights에 로그 보내기

WAD 구성의 일부로 모니터링 및 진단 데이터를 AI(Application Insights)에 보낼 수 있습니다. 이벤트 분석 및 시각화에 AI를 사용하도록 결정할 경우 "WadCfg"의 일부로 AI 싱크를 설정하려면 [Application Insights를 사용하여 이벤트 분석 및 시각화](service-fabric-diagnostics-event-analysis-appinsights.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 진단을 제대로 구성하면 저장소 테이블에서 ETW 및 EventSource 로그의 데이터를 확인할 수 있습니다. OMS 또는 Kibana를 사용하거나 Resource Manager 템플릿에서 직접 구성되지 않은 기타 데이터 분석 및 시각화 플랫폼을 사용하도록 선택할 경우 선택한 플랫폼이 이러한 저장소 테이블에서 데이터를 읽도록 설정해야 합니다. OMS에 대해 이 작업을 수행하는 방법은 비교적 간단하고 [OMS를 통해 이벤트 및 로그 분석](service-fabric-diagnostics-event-analysis-oms.md)에서 설명됩니다. Application Insights는 진단 확장 구성의 일부로 구성될 수 있으므로 이런 의미에서 약간 특별한 경우입니다. 따라서 AI를 사용하도록 선택할 경우 [관련 문서](service-fabric-diagnostics-event-analysis-appinsights.md)를 참조하세요.

>[!NOTE]
>현재 테이블로 전송되는 이벤트를 필터링하거나 영구 제거할 방법은 없습니다. 테이블에서 이벤트를 제거하는 프로세스를 구현하지 않으면 테이블이 계속 커집니다. 현재 [Watchdog 샘플](https://github.com/Azure-Samples/service-fabric-watchdog-service)에서 실행되는 데이터 그루밍 서비스의 예제가 있고, 30일 또는 90일 넘어서 로그를 저장해야 하는 적절한 이유가 없다면 직접 작성하는 것이 좋습니다.

* [진단 확장을 사용하여 성능 카운터 또는 로그를 수집하는 방법 알아보기](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Application Insights를 사용하여 이벤트 분석 및 시각화](service-fabric-diagnostics-event-analysis-appinsights.md)
* [OMS를 사용하여 이벤트 분석 및 시각화](service-fabric-diagnostics-event-analysis-oms.md)
