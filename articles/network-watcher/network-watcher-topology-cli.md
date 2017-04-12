---
title: "Azure Network Watcher 토폴로지 보기 - Azure CLI | Microsoft Docs"
description: "이 문서에서는 Azure CLI를 사용하여 네트워크 토폴로지를 쿼리하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02add070405f3f00d3631362a2b139239d44c34d
ms.lasthandoff: 03/28/2017

---

# <a name="view-network-watcher-topology-with-azure-cli"></a>Azure CLI를 사용하여 Network Watcher 토폴로지 보기

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Network Watcher의 토폴로지 기능은 구독에 있는 네트워크 리소스의 시각적 표현을 제공합니다. 포털에서 이 시각화가 자동으로 표시됩니다. PowerShell을 통해 포털에 있는 토폴로지 보기 이외의 정보를 검색할 수 있습니다.
다른 도구에서 데이터를 사용하여 시각화를 빌드할 수 있는 것처럼 이 기능을 사용하면 토폴로지 정보를 다양하게 만들 수 있습니다.

이 문서에서는 Windows, Mac 및 Linux에 사용할 수 있는 플랫폼 간 Azure CLI 1.0을 사용합니다. Network Watcher는 현재 CLI 지원을 위한 Azure CLI 1.0을 사용합니다.

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

이 시나리오에서는 `network watcher topology` cmdlet을 사용하여 토폴로지 정보를 검색합니다. [REST API를 사용하여 네트워크 토폴로지를 검색](network-watcher-topology-rest.md)하는 방법에 대한 문서도 있습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 지정된 리소스 그룹에 대한 토폴로지 응답을 검색합니다.

## <a name="retrieve-topology"></a>토폴로지 검색

`network watcher topology` cmdlet은 지정된 리소스 그룹에 대한 토폴로지를 검색합니다. "-json" 인수를 추가하여 json 형식인 출력을 봅니다.

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>결과

반환된 결과에는 "리소스" 속성 이름이 있으며 여기에는 `network watcher topology` cmdlet에 대한 json 응답 본문이 포함됩니다.  응답은 네트워크 보안 그룹 및 해당 연결에 있는 리소스를 포함합니다(즉, 포함 및 연결됨).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>다음 단계

[보안 그룹 개요 보기](network-watcher-security-group-view-overview.md)를 방문하여 네트워크 리소스에 적용되는 보안 규칙에 대해 자세히 알아봅니다.

