---
title: Azure Network Watcher Next Hop을 사용하여 다음 홉 찾기 - Azure CLI 2.0 | Microsoft Docs
description: 이 문서에서는 Azure CLI에서 다음 홉을 사용하여 다음 홉 유형 및 IP 주소를 찾을 수 있는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: ''
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 873bd0f8c98cfa67c77841df8aa53eb2b895cd54
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809761"
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>Azure CLI 2.0을 사용하는 Azure Network Watcher에서 Next Hop 기능을 사용하는 다음 홉이 무엇인지 확인합니다.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop은 Network Watcher의 기능으로 지정된 가상 컴퓨터를 기반으로 하는 다음 홉 유형 및 IP 주소를 가져올 수 있습니다. 이 기능은 가상 컴퓨터에서 나가는 트래픽이 게이트웨이, 인터넷 또는 가상 네트워크를 트래버스하여 대상에 도달할지 여부를 결정하는 데 유용합니다.

이 문서에서는 Windows, Mac 및 Linux에서 사용할 수 있는 리소스 관리 배포 모델용 차세대 CLI인 Azure CLI 2.0을 사용합니다.

이 문서의 단계를 수행하려면 [Mac, Linux 및 Windows용 Azure 명령줄 인터페이스(Azure CLI)를 설치](https://docs.microsoft.com/cli/azure/install-az-cli2)해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 Azure CLI를 사용하여 다음 홉 유형 및 IP 주소를 찾습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 리소스에 대한 다음 홉 유형 및 IP 주소를 찾는 Network Watcher의 기능인 Next Hop을 사용합니다. Next Hop에 대한 자세한 내용을 보려면 [Next Hop 개요](network-watcher-next-hop-overview.md)를 방문하세요.


## <a name="get-next-hop"></a>다음 홉 가져오기

다음 홉을 가져오려면 `az network watcher show-next-hop` cmdlet을 호출합니다. cmdlet을 Network Watcher 리소스 그룹, NetworkWatcher, 가상 머신 ID, 원본 IP 주소 및 대상 IP 주소에 전달합니다. 이 예제에서 대상 IP 주소는 다른 가상 네트워크의 VM입니다. 두 개의 가상 네트워크 간에 가상 네트워크 게이트웨이가 있습니다.

아직 설치하지 않은 경우 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치 및 구성하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인합니다. 그런 후 다음 명령을 실행합니다.

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
VM에 여러 NIC가 있고 NIC에 IP를 전달할 수 있는 경우 NIC 매개 변수(-i nic-id)를 지정해야 합니다. 그렇지 않은 경우 선택적입니다.

## <a name="review-results"></a>결과 검토

완료되면 결과가 제공됩니다. 리소스의 유형뿐만 아니라 다음 홉 IP 주소가 반환됩니다.

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
```

다음 목록에서는 현재 사용할 수 있는 NextHopType 값을 보여 줍니다.

**다음 홉 유형**

* 인터넷
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* 없음

## <a name="next-steps"></a>다음 단계

[Network Watcher를 사용하여 NSG 감사](network-watcher-nsg-auditing-powershell.md)를 방문하여 네트워크 보안 그룹 설정을 프로그래밍 방식으로 검토하는 방법에 대해 알아봅니다.
