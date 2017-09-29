---
title: "Azure Portal을 사용하여 영역이 설정된 공용 IP 주소 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 가용성 영역에서 공용 IP 주소를 만듭니다."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 0398e9f814614b3dccf849385903a8ed8717eafb
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Azure Portal을 사용하여 가용성 영역에서 공용 IP 주소 만들기

Azure 가용성 영역(미리 보기)에서 공용 IP 주소를 배포할 수 있습니다. 가용성 영역은 Azure 지역에서 물리적으로 별도 영역입니다. 방법 배우기:

> * 가용성 영역에서 공용 IP 주소 만들기
> * 가용성 영역에서 만든 관련 리소스 식별

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

> [!NOTE]
> 가용성 영역은 미리 보기 상태이며 개발 및 테스트 시나리오에 대한 준비를 마쳤습니다. Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수 있습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다. 

## <a name="create-a-zonal-public-ip-address"></a>영역 공용 IP 주소 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
2. **네트워킹**을 선택한 다음 **공용 IP 주소**를 선택합니다.
3. 다음 설정에 대한 값을 입력 또는 선택하고 구독을 선택하고 나머지 설정에 기본값을 적용한 다음 **만들기**를 클릭합니다.

    |설정|값|
    |---|---|
    |SKU| **기본**: 정적 또는 동적 할당 방법으로 할당됩니다. 네트워크 인터페이스, VPN Gateway, Application Gateway 및 인터넷 연결 부하 분산 장치 등 공용 IP 주소를 할당할 수 있는 모든 Azure 리소스에 할당할 수 있습니다. **가용성 영역** 설정에서 특정 영역에 공용 IP 주소를 할당할 수 있습니다. 영역 중복되지 않습니다. 사용 가능한 영역에 대한 자세한 내용은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. **표준**: 정적 할당 방법으로만 할당됩니다. 네트워크 인터페이스 또는 표준 인터넷 연결 부하 분산 장치에 할당할 수 있습니다. 표준 인터넷 연결 부하 분산 장치에 공용 IP 주소를 할당할 경우 표준을 선택해야 합니다. Azure 부하 분산 장치 SKU에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)를 참조하세요. 기본적으로 영역 중복됩니다. 영역을 만들고 특정 가용성 영역에서 보장할 수 있습니다. 표준 SKU는 미리 보기 릴리스 상태입니다. 표준 SKU 공용 IP 주소를 만들기 전에 먼저 미리 보기에 등록해야 합니다. 미리 보기에 등록하려면 [표준 SKU 미리 보기에 등록](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up)을 참조하세요. 표준 SKU는 지원되는 위치에만 만들 수 있습니다.  지원되는 위치(지역) 목록은 [지역 가용성](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability)을 참조하고 추가 지역 지원은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 모니터링하세요.|   
    |이름|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
    |리소스 그룹|[새로 만들기]를 클릭하고 myResourceGroup을 입력합니다.|
    |위치|서유럽|
    |가용성 영역|**표준** SKU를 선택한 경우 영역에서 IP 주소를 복원할 수 있도록 하려면 *영역 중복*을 선택할 수 있습니다. **기본** SKU를 선택할 경우 영역에서 IP 주소를 복원할 수 없습니다. 선택한 SKU에 관계없이, 선택할 경우 특정 영역에 주소를 할당할 수 있습니다. |

    설정이 다음 그림과 같이 포털에 나타납니다.

    ![영역 공용 IP 주소 만들기](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> 가상 컴퓨터의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)에 대해 자세히 알아보기
- [공용 IP 주소](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아보기
