---
title: "Azure Network Watcher Next Hop을 사용하여 다음 홉 찾기 - Azure Portal | Microsoft Docs"
description: "이 문서에서는 Azure Portal에서 Next Hop을 사용하여 다음 홉 유형 및 IP 주소를 찾을 수 있는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 5fb85551d3c5fb8d1c63a965d89bae788000afe8
ms.lasthandoff: 03/31/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Azure Portal을 사용하는 Azure Network Watcher에서 Next Hop 기능을 사용하는 다음 홉이 무엇인지 확인합니다.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop은 Network Watcher의 기능으로 지정된 가상 컴퓨터를 기반으로 하는 다음 홉 유형 및 IP 주소를 가져올 수 있습니다. 이 기능은 가상 컴퓨터에서 나가는 트래픽이 게이트웨이, 인터넷 또는 가상 네트워크를 트래버스하여 대상에 도달할지 여부를 결정하는 데 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 리소스에 대한 다음 홉 유형 및 IP 주소를 찾는 데 Next Hop을 사용합니다. Next Hop에 대한 자세한 내용을 보려면 [Next Hop 개요](network-watcher-next-hop-overview.md)를 방문하세요.

이 시나리오에서는 다음을 수행합니다.

* 가상 컴퓨터에서 다음 홉을 검색합니다.

## <a name="get-next-hop"></a>다음 홉 가져오기

### <a name="step-1"></a>1단계

Azure Portal에서 Network Watcher 리소스로 이동합니다.

### <a name="step-2"></a>2단계

탐색 창에서 **다음 홉**을 클릭하고 가상 컴퓨터 및 네트워크 인터페이스를 선택한 후 원본 및 대상 IP를 입력하고 **다음 홉** 단추를 클릭합니다.

> [!NOTE]
> 다음 홉에서는 VM 리소스가 실행되기 위해 할당되어야 합니다.

![다음 홉 가져오기 개요][1]

### <a name="step-3"></a>3단계

작업이 완료되면 결과가 제공됩니다. 다음 홉에 대한 IP 주소 및 장치 유형이 표시됩니다. 다음 표에서는 포털에서 사용할 수 있는 반환된 값을 보여 줍니다.

**다음 홉 유형**

* 인터넷
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* 없음

이 트래픽을 라우팅하는 데 사용자 지정 경로가 사용된 경우 UDR(사용자 정의 경로)도 결과와 함께 표시됩니다.

![다음 홉 결과 가져오기][2]

## <a name="next-steps"></a>다음 단계

[Network Watcher를 사용하여 NSG 감사](network-watcher-nsg-auditing-powershell.md)를 방문하여 네트워크 보안 그룹 설정을 프로그래밍 방식으로 검토하는 방법에 대해 알아봅니다.

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png















