---
title: "Azure Network Watcher 토폴로지 보기 - REST API | Microsoft Docs"
description: "이 문서에서는 REST API를 사용하여 네트워크 토폴로지를 쿼리하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 50d82847fb629880f298f79f9ab791a13836f01c
ms.openlocfilehash: 4fa34050a8039cebebe30842469c596c83744313
ms.lasthandoff: 03/31/2017

---

# <a name="view-network-watcher-topology-with-rest-api"></a>REST API를 사용하여 Network Watcher 토폴로지 보기

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Network Watcher의 토폴로지 기능은 구독에 있는 네트워크 리소스의 시각적 표현을 제공합니다. 포털에서 이 시각화가 자동으로 표시됩니다. PowerShell을 통해 포털에 있는 토폴로지 보기 이외의 정보를 검색할 수 있습니다.
다른 도구에서 데이터를 사용하여 시각화를 빌드할 수 있는 것처럼 이 기능을 사용하면 토폴로지 정보를 다양하게 만들 수 있습니다.

상호 연결은 두 개의 관계에서 모델링됩니다.

- **포함** - 예: VNet은 서브넷을 포함하고 NIC를 포함합니다.
- **연결됨** -예제: NIC는 VM과 연결됩니다.

다음 목록은 토폴로지 REST API를 쿼리할 때 반환되는 속성을 보여 줍니다.

* **name** - 리소스의 이름입니다.
* **id** - 리소스 그룹의 URI입니다.
* **location** - 리소스가 있는 위치입니다.
* **associations** - 참조되는 개체에 대한 연결 목록입니다.
    * **name** - 참조되는 리소스의 이름입니다.
    * **resourceId** - resourceId는 연결에서 참조되는 리소스의 URI입니다.
    * **associationType** - 이 값은 하위 개체 및 상위 개체 간의 관계를 참조합니다. 유효한 값은 **포함** 또는 **관련됨**입니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 토폴로지 정보를 검색합니다. PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 지정된 리소스 그룹에 대한 토폴로지 응답을 검색합니다.

## <a name="log-in-with-armclient"></a>ARMClient에 로그인

Azure 자격 증명으로 armclient에 로그인합니다.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>토폴로지 검색

다음 예제는 REST API에서 토폴로지를 요청합니다.  이 예제는 예제를 만드는 데 유연성을 허용하도록 매개 변수화됩니다.  모든 값을 \< \>로 둘러싸인 모든 값을 대체합니다.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

다음 응답은 리소스 그룹에 대해 토폴로지를 검색할 때 반환되는 축약된 응답의 예입니다.

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>다음 단계

[PowerBI에서 NSG 흐름 로그 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)에서 Power BI로 NSG 흐름 로그를 시각화하는 방법에 대해 알아보세요.


