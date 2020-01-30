---
title: Network Watcher-Azure Resource Manager 템플릿을 사용 하 여 NSG 흐름 로그 만들기
description: Azure Resource Manager 템플릿 및 PowerShell을 사용 하 여 NSG 흐름 로그를 쉽게 설정할 수 있습니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 2ae6314accf5ad9143e1e103239c2e7c884ec947
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776639"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 NSG 흐름 로그 구성

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure 리소스 관리자](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 는 Azure의 기본 이며 [인프라를 코드로](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)관리 하는 강력한 방법입니다.

이 문서에서는 Azure Resource Manager 템플릿 및 Azure PowerShell를 사용 하 여 [Nsg 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) 를 프로그래밍 방식으로 사용 하도록 설정 하는 방법을 보여 줍니다. 먼저 NSG 흐름 로그 개체의 속성에 대 한 개요를 제공 하 고 몇 가지 샘플 템플릿을 제공 합니다. 그런 다음 로컬 PowerShell 인스턴스를 사용 하 여 템플릿을 배포 합니다.


## <a name="nsg-flow-logs-object"></a>NSG 흐름 로그 개체

모든 매개 변수가 포함 된 NSG 흐름 로그 개체는 아래에 표시 됩니다.
속성의 전체 개요를 확인 하려면 [Nsg 흐름 로그 템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)를 읽어 보십시오.

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Microsoft. Network/networkWatchers/flowLogs 리소스를 만들려면 템플릿의 리소스 섹션에 위의 JSON을 추가 합니다.


## <a name="creating-your-template"></a>템플릿 만들기

Azure Resource Manager 템플릿을 처음 사용 하는 경우 아래 링크를 사용 하 여 자세히 알아볼 수 있습니다.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [자습서: 첫 번째 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


다음은 NSG 흐름 로그를 설정 하는 전체 템플릿의 두 가지 예입니다.

**예 1**: 최소 매개 변수가 전달 된 위의 가장 간단한 버전입니다. 아래 템플릿에서는 대상 NSG에 대 한 NSG 흐름 로그를 사용 하도록 설정 하 고 지정 된 저장소 계정에 저장 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * 리소스 이름에는 "Parent Resource >/Child resource" 형식이 있습니다. 여기에서 부모 리소스는 지역 Network Watcher 인스턴스입니다 (형식: NetworkWatcher_<RegionName>. 예: NetworkWatcher_centraluseuap)
> * targetResourceId는 대상 NSG의 리소스 ID입니다.
> * storageId는 대상 저장소 계정의 리소스 ID입니다.

**예 2**: 다음 템플릿은 Nsg 흐름 로그 (버전 2)를 5 일 동안 보존 하도록 설정 합니다. 처리 간격이 10 분인 트래픽 분석를 사용 하도록 설정 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
            "enabled": true,
        "workspaceResourceId": "91a3d1e9-698e-4a49-96dc-f6fc585ae888",
        "trafficAnalyticsInterval": 10
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 1
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 배포

이 자습서에서는 흐름 로깅을 사용 하도록 설정할 수 있는 기존 리소스 그룹 및 NSG가 있는 것으로 가정 합니다.
위의 예제 템플릿 중 하나를 `azuredeploy.json`으로 로컬로 저장할 수 있습니다. 구독에서 유효한 리소스를 가리키도록 속성 값을 업데이트 합니다.

템플릿을 배포 하려면 PowerShell에서 다음 명령을 실행 합니다.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>배포 확인

배포가 성공 했는지 확인 하는 몇 가지 방법이 있습니다. PowerShell 콘솔에 "ProvisioningState"이 "성공"으로 표시 되어야 합니다. 또한 [Nsg 흐름 로그 포털 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) 를 방문 하 여 변경 내용을 확인할 수 있습니다. 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)을 살펴보세요.


## <a name="next-steps"></a>다음 단계

다음을 사용 하 여 NSG 흐름 데이터를 시각화 하는 방법을 알아봅니다.
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [오픈 소스 도구](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 트래픽 분석](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
