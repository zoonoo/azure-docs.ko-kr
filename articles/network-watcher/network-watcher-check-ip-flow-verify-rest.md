---
title: "Azure Network Watcher IP 흐름 확인을 사용하여 트래픽 확인 - REST | Microsoft Docs"
description: "이 문서에서는 가상 컴퓨터 간에 트래픽을 허용하는지 아니면 거부하는지를 확인하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 18b98300ee103e3f3118f6db4e1c1a0e169eecad
ms.lasthandoff: 03/04/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher의 구성 요소인 IP 흐름 확인을 사용하여 트래픽을 허용하는지 아니면 거부하는지를 확인합니다.

> [!div class="op_single_selector"]
> - [Azure 포털](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

IP 흐름 확인은 가상 컴퓨터 간에 트래픽을 허용하는지를 확인할 수 있는 Network Watcher의 기능입니다. 들어오거나 나가는 트래픽에 대해 유효성 검사를 실행할 수 있습니다. 이 시나리오는 가상 컴퓨터가 외부 리소스 또는 백 엔드에 연결할 수 있는지에 대한 현재 상태를 가져올 때 유용합니다. IP 흐름 확인은 NSG(네트워크 보안 그룹) 규칙이 모두 제대로 구성되었는지 확인하고 NSG 규칙에 의해 차단되는 흐름 문제를 해결하는 데 사용될 수 있습니다. IP 흐름 확인을 사용하여 차단하려는 트래픽이 NSG에서 제대로 차단되었는지 확인할 수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

## <a name="scenario"></a>시나리오

이 시나리오에서는 IP 흐름 확인을 사용하여 가상 컴퓨터가 포트 443을 통해 다른 컴퓨터와 통신할 수 있는지 확인합니다. 트래픽이 거부된 경우 해당 트래픽을 거부하는 보안 규칙을 반환합니다. IP 흐름 확인에 대한 자세한 내용을 보려면 [IP 흐름 확인 개요](network-watcher-ip-flow-verify-overview.md)를 방문하세요.

이 시나리오에서는 다음을 수행합니다.

* 가상 컴퓨터 검색
* IP 흐름 확인 호출
* 결과 확인

## <a name="log-in-with-armclient"></a>ARMClient로 로그인

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>가상 컴퓨터 검색

다음 스크립트를 실행하여 가상 컴퓨터를 반환합니다. 다음 코드에는 다음 변수에 대한 값이 필요합니다.

* **subscriptionId** - 사용할 구독 ID입니다.
* **resourceGroupName** - 가상 컴퓨터를 포함하는 리소스 그룹의 이름입니다.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

필요한 정보는 유형 `Microsoft.Compute/virtualMachines` 아래의 ID입니다. 결과는 다음 코드 샘플과 유사합니다.

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>IP 흐름 확인 호출

다음 예제에서는 지정된 가상 컴퓨터에 대한 트래픽을 확인하는 요청을 만듭니다. 응답은 트래픽이 허용되는 경우 또는 트래픽이 거부되는 경우를 반환합니다. 트래픽이 거부되는 경우 어떤 규칙에서 트래픽을 차단하는지 반환합니다.

> [!NOTE]
> IP 흐름 확인에서는 VM 리소스가 할당되어야 합니다.

스크립트에는 가상 컴퓨터의 리소스 ID 및 가상 컴퓨터에 있는 네트워크 인터페이스 카드의 리소스 ID가 필요합니다. 위의 출력에서 이러한 값을 제공합니다.

> [!Important]
> Network Watcher REST 호출의 경우 요청 URI에 있는 리소스 그룹 이름은 진단 작업을 수행하는 리소스가 아니라, Network Watcher 인스턴스를 포함하는 리소스입니다.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>결과 이해

사용자가 받는 응답은 트래픽의 허용 또는 거부 여부를 알려줍니다. 응답은 다음 예제 중 하나와 같습니다.

**허용됨**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**거부됨**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>다음 단계

트래픽이 차단되지 않아야 하는데 차단된 경우 [네트워크 보안 그룹 관리](../virtual-network/virtual-network-manage-nsg-arm-portal.md)를 참조하여 네트워크 보안 그룹에 대해 자세히 알아봅니다.













