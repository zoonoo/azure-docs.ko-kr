---
title: Azure Network Watcher IP 흐름 확인을 사용하여 트래픽 확인 - Azure CLI | Microsoft Docs
description: 이 문서에서는 Azure CLI를 사용하여 가상 머신 간에 트래픽을 허용하는지 아니면 거부하는지를 확인하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8681118e55d8ddddf17ebac3bae63486446c70e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809711"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher의 구성 요소인 IP 흐름 확인을 사용하여 VM 간에 트래픽을 허용하는지 아니면 거부하는지를 확인합니다.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


IP 흐름 확인은 가상 컴퓨터 간에 트래픽을 허용하는지를 확인할 수 있는 Network Watcher의 기능입니다. 이 시나리오는 가상 머신이 외부 리소스 또는 백 엔드에 연결할 수 있는지에 대한 현재 상태를 가져올 때 유용합니다. IP 흐름 확인은 NSG(네트워크 보안 그룹) 규칙이 모두 제대로 구성되었는지 확인하고 NSG 규칙에 의해 차단되는 흐름 문제를 해결하는 데 사용될 수 있습니다. IP 흐름 확인을 사용하여 차단하려는 트래픽이 NSG에서 제대로 차단되었는지 확인할 수도 있습니다.

이 문서에서는 Windows, Mac 및 Linux에 사용할 수 있는 플랫폼 간 Azure CLI 1.0을 사용합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 사용자가 Network Watcher를 만들거나 Network Watcher의 기존 인스턴스를 가져오는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 머신을 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 시나리오에서는 IP 확인 흐름을 사용하여 가상 머신이 알려진 Bing IP 주소에 연결할 수 있는지 확인합니다. 트래픽이 거부된 경우 해당 트래픽을 거부하는 보안 규칙을 반환합니다. IP 흐름 확인에 대한 자세한 내용을 보려면 [IP 흐름 확인 개요](network-watcher-ip-flow-verify-overview.md)를 방문하세요.


## <a name="get-a-vm"></a>VM 확인

IP 흐름 확인은 원격 대상 간에 가상 컴퓨터의 트래픽 또는 IP 주소를 테스트합니다. 가상 머신의 ID가 cmdlet에 대해 필요합니다. 사용할 가상 머신의 ID를 이미 알고 있는 경우 이 단계를 건너뛸 수 있습니다.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>NIC 가져오기

가상 머신에 있는 NIC의 IP 주소가 필요합니다. 다음에 나오는 명령을 사용하여 가상 머신에 대한 NIC를 가져옵니다. 가상 머신에서 테스트하려는 IP 주소를 이미 알고 있는 경우 이 단계를 건너뛸 수 있습니다.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>IP 흐름 확인 실행

`network watcher ip-flow-verify` cmdlet을 실행하여 트래픽을 테스트합니다. 이 예제에서는 첫 번째 NIC의 첫 번째 IP 주소가 사용됩니다.

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> IP 흐름 확인에서는 VM 리소스가 실행되기 위해 할당되어야 합니다.

## <a name="review-results"></a>결과 검토

다음 예제는 결과가 반환된 `network watcher ip-flow-verify`을 실행한 후에 이전 단계에서 반환된 결과입니다.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>다음 단계

트래픽이 차단되지 않아야 하는데 차단된 경우 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)를 참조하여 정의된 네트워크 보안 그룹 및 보안 규칙을 추적합니다.

[Network Watcher를 사용하여 NSG(네트워크 보안 그룹) 감사](network-watcher-nsg-auditing-powershell.md)를 방문하여 NSG 설정을 감사하는 방법에 대해 알아봅니다.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
