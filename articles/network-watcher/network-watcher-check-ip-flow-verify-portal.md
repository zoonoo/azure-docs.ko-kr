---
title: "Azure Network Watcher IP 흐름 확인을 사용하여 트래픽 확인 - Azure Portal | Microsoft Docs"
description: "이 문서에서는 가상 컴퓨터 간에 트래픽을 허용하는지 아니면 거부하는지를 확인하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1aa8fff31d8d1908b1ea93cb970487d08ea4adf6
ms.openlocfilehash: ddd6fcc8de133a5ff5f427d070c6c6ec207d9236
ms.lasthandoff: 03/31/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher의 구성 요소인 IP 흐름 확인을 사용하여 VM 간에 트래픽을 허용하는지 아니면 거부하는지를 확인합니다.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

IP 흐름 확인은 가상 컴퓨터 간에 트래픽을 허용하는지를 확인할 수 있는 Network Watcher의 기능입니다. 들어오거나 나가는 트래픽에 대해 유효성 검사를 실행할 수 있습니다. 이 시나리오는 가상 컴퓨터가 외부 리소스 또는 다른 리소스에 연결할 수 있는지에 대한 현재 상태를 가져올 때 유용합니다. IP 흐름 확인은 NSG(네트워크 보안 그룹) 규칙이 모두 제대로 구성되었는지 확인하고 NSG 규칙에 의해 차단되는 흐름 문제를 해결하는 데 사용될 수 있습니다. IP 흐름 확인을 사용하여 차단하려는 트래픽이 NSG에서 제대로 차단되었는지 확인할 수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 사용자가 Network Watcher를 만들거나 Network Watcher의 기존 인스턴스를 가져오는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 시나리오에서는 IP 확인 흐름을 사용하여 가상 컴퓨터가 포트 443을 통해 다른 컴퓨터와 통신할 수 있는지 확인합니다. 트래픽이 거부된 경우 해당 트래픽을 거부하는 보안 규칙을 반환합니다. IP 흐름 확인에 대한 자세한 내용을 보려면 [IP 흐름 확인 개요](network-watcher-ip-flow-verify-overview.md)를 방문하세요.

### <a name="run-ip-flow-verify"></a>IP 흐름 확인 실행

Network Watcher로 이동하고 **IP 흐름 확인**을 클릭합니다. 트래픽을 확인하려는 가상 컴퓨터 및 네트워크 인터페이스를 선택합니다. 추가 필터링 정보를 입력하고 **확인**을 클릭합니다.

**확인**을 클릭하면 사용자가 제공한 기준에 따라 흐름이 확인됩니다. 결과는 **액세스 허용됨** 또는 **액세스 거부됨**입니다. 액세스가 거부되면 트래픽을 차단하는 NSG(네트워크 보안 그룹) 및 보안 규칙이 제공됩니다. 트래픽 거부가 예상되는 동작인 경우 규칙이 성공한 것입니다.

> [!NOTE]
> IP 흐름 확인에서는 VM 리소스가 할당되어야 합니다.

다음 이미지에서 볼 수 있는 것처럼 아웃바운드 HTTPS 트래픽이 허용되었습니다.

![IP 흐름 확인 개요][1]

다음 이미지에 표시된 것처럼 트래픽은 인바운드로 변경되고 인바운드 포트는 123으로 변경되었습니다. 트래픽이 거부되면 트래픽을 거부하는 네트워크 보안 그룹 및 보안 규칙이 함께 "액세스 거부됨" 메시지가 표시됩니다.

![IP 흐름 결과][2]

## <a name="next-steps"></a>다음 단계

트래픽이 차단되지 않아야 하는데 차단된 경우 [네트워크 보안 그룹 관리](../virtual-network/virtual-network-manage-nsg-arm-portal.md)를 참조하여 정의된 네트워크 보안 그룹 및 보안 규칙을 추적합니다.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














