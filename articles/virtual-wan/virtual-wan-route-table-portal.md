---
title: Azure 가상 WAN 가상 허브 경로 테이블 만들기-Azure portal | Microsoft Docs
description: 가상 WAN 가상 허브 경로 테이블을 포털을 사용 하 여 네트워크 가상 어플라이언스로 트래픽을 유도 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580585"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Nva에 대 한 가상 WAN 허브 경로 테이블을 만듭니다. Azure portal

이 문서를 네트워크 가상 어플라이언스 (NVA)를 허브에서 트래픽을 유도 하는 방법을 보여 줍니다.

![Virtual WAN 다이어그램](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>시작하기 전에

다음 기준을 충족하는지 확인합니다.

*  네트워크 가상 어플라이언스 (NVA) 해야합니다. 네트워크 가상 어플라이언스는 일반적으로 프로 비전 되는 Azure Marketplace에서 가상 네트워크에서 선택한 타사 소프트웨어.

    * NVA 네트워크 인터페이스에 개인 IP 주소가 할당 되어야 합니다.

    * NVA 가상 허브에 배포 되지 않습니다. 별도의 VNet에 배포해야 합니다.

    *  NVA VNet 하나 있을 수 있습니다 또는 여러 가상 네트워크에 연결 합니다. 이 문서에서는 '간접 스포크 VNet'으로 NVA VNet을 언급 했습니다. 이러한 Vnet은 VNet 피어 링을 사용 하 여 NVA VNet에 연결할 수 있습니다.
*  2 개의 Vnet을 만든 것입니다. 스포크 Vnet으로 사용 됩니다.

    * 이 연습에서는 스포크 VNet 주소 공간은 다음과 같습니다. VNet1: 10.0.2.0/24과 VNet2의 경우: 10.0.3.0/24. VNet을 만드는 방법에 대 한 정보를 참조 하세요 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)합니다.

    * Vnet에서 가상 네트워크 게이트웨이가 있는지 확인 하십시오.
    * 이 구성에 대 한 이러한 Vnet 게이트웨이 서브넷을 필요 하지 않습니다.

## <a name="signin"></a>1. 로그인

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

## <a name="vwan"></a>2. 가상 WAN 만들기

가상 WAN 만들기 이 연습에서는 다음 값을 사용할 수 있습니다.

* **가상 WAN 이름:** myVirtualWAN
* **리소스 그룹:** testRG
* **위치:** 미국 서부

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. 허브 만들기

허브를 만듭니다. 이 연습에서는 다음 값을 사용할 수 있습니다.

* **위치:** 미국 서부
* **이름:** westushub
* **허브 개인 주소 공간:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. 만들고 허브 경로 테이블 적용

허브 경로 테이블을 사용 하 여 허브를 업데이트 합니다. 이 연습에서는 다음 값을 사용할 수 있습니다.

* **간접 스포크 VNet 주소 공간:** (VNet1 and VNet2) 10.0.2.0/24 and 10.0.3.0/24
* **DMZ NVA 네트워크 인터페이스 개인 IP 주소:** 10.0.4.5

1. 가상 WAN에 이동 합니다.
2. 경로 테이블을 만들려는 허브를 클릭 합니다.
3. 클릭 된 **...** 를 클릭 하 고 **편집 가상 허브**합니다.
4. 에 **편집 가상 허브** 페이지에서 아래로 스크롤하여 확인란을 선택 **라우팅에 사용 하 여 테이블**합니다.
5. 에 **대상 접두사가 경우** 열 주소 공간을 추가 합니다. 에 **다음 홉 보내기** 열 DMZ NVA 네트워크 인터페이스 개인 IP 주소를 추가 합니다.
6. 클릭 **Confirm** 경로 테이블 설정을 사용 하 여 허브 리소스를 업데이트 합니다.

## <a name="connections"></a>5. VNet 연결 만들기

허브에 간접 각 스포크 VNet (VNet1 및 VNet2)에서 연결을 만듭니다. 그런 다음 허브에 NVA VNet에서 연결을 만듭니다.

 이 단계에서는 다음 값을 사용할 수 있습니다.

| VNet 이름| 연결 이름|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

연결 하려는 각 VNet에 대해 다음 절차를 반복 합니다.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가**를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
4. **확인**을 클릭하여 연결을 만듭니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.