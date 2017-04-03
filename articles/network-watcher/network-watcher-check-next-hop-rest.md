---
title: "Azure Network Watcher Next Hop을 사용하여 다음 홉 찾기 - REST | Microsoft Docs"
description: "이 문서에서는 Azure REST API로 Next Hop을 사용하여 다음 홉 유형 및 IP 주소를 찾을 수 있는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 04801ce75a767da2756165f51f49192f89887380
ms.lasthandoff: 03/31/2017

---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Azure REST API를 사용하는 Azure Network Watcher에서 Next Hop 기능을 사용하는 다음 홉이 무엇인지 확인합니다.

> [!div class="op_single_selector"]
> - [Azure 포털](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop은 Network Watcher의 기능으로 지정된 가상 컴퓨터를 기반으로 하는 다음 홉 유형 및 IP 주소를 가져올 수 있습니다. 이 기능은 가상 컴퓨터에서 나가는 트래픽이 게이트웨이, 인터넷 또는 가상 네트워크를 트래버스하여 대상에 도달할지 여부를 결정하는 데 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에

PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 리소스에 대한 다음 홉 유형 및 IP 주소를 찾는 Network Watcher의 기능인 Next Hop을 사용합니다. Next Hop에 대한 자세한 내용을 보려면 [Next Hop 개요](network-watcher-next-hop-overview.md)를 방문하세요.

이 시나리오에서는 다음을 수행합니다.

* 가상 컴퓨터에 대한 다음 홉을 검색합니다.

## <a name="log-in-with-armclient"></a>ARMClient에 로그인

Azure 자격 증명으로 armclient에 로그인합니다.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>가상 컴퓨터 검색

다음 스크립트를 실행하여 가상 컴퓨터를 반환합니다. 다음 홉을 실행하는 데 이 정보가 필요합니다.

다음 코드에는 다음 변수에 대한 값이 필요합니다.

- **subscriptionId** - 사용할 구독 ID입니다.
- **resourceGroupName** - 가상 컴퓨터를 포함하는 리소스 그룹의 이름입니다.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

다음 출력에서는 다음 예제에 가상 컴퓨터의 ID가 사용됩니다.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>다음 홉 가져오기

권한 부여 헤더가 생성되면 가상 컴퓨터의 다음 홉을 검색할 수 있습니다. 작동할 코드 예에 대해 다음 값을 대체해야 합니다.

> [!Important]
> Network Watcher REST API 호출의 경우 요청 URI에 있는 리소스 그룹 이름은 진단 작업이 수행되는 리소스가 아니라, Network Watcher를 포함하는 리소스 그룹입니다.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> 다음 홉에서는 VM 리소스가 실행되기 위해 할당되어야 합니다.

## <a name="results"></a>결과

다음 코드 조각은 수신된 출력의 예입니다. 결과에는 다음 값이 포함될 수 있습니다.

* **nextHopType** - 이 값은 Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway 또는 None 값 중 하나입니다.
* **nextHopIpAddress** - 다음 홉의 IP 주소입니다.
* **routeTableId** - 이 값은 경로와 연결된 경로 테이블에 대한 URI이거나 사용자 정의 경로가 정의되지 않은 경우 *System Route* 값이 반환됩니다.

다음은 json 형식의 결과입니다.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>다음 단계

가상 컴퓨터에 대해 다음 홉을 확인할 수 있으려면 [보안 보기 개요](network-watcher-security-group-view-overview.md)를 방문하여 네트워크 리소스의 보안을 확인할 수 있습니다.















