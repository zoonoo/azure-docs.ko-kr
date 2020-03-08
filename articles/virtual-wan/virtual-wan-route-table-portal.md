---
title: '가상 WAN: NVA에 대 한 가상 허브 경로 테이블 만들기: Azure Portal'
description: 포털을 사용 하 여 네트워크 가상 어플라이언스로 트래픽을 유도 하는 가상 WAN 가상 허브 경로 테이블
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402944"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Nva에 대 한 가상 WAN 허브 경로 테이블 만들기: Azure Portal

이 문서에서는 NVA (네트워크 가상 어플라이언스)를 통해 가상 WAN 허브에 연결 된 분기 (온-프레미스 사이트)에서 스포크 가상 네트워크 (VNet)로 트래픽을 유도 하는 방법을 보여 줍니다.

![Virtual WAN 다이어그램](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>시작하기 전에

다음 기준을 충족하는지 확인합니다.

*  NVA (네트워크 가상 어플라이언스)가 있습니다. 네트워크 가상 어플라이언스는 일반적으로 가상 네트워크의 Azure Marketplace에서 프로 비전 되는 사용자가 선택한 타사 소프트웨어입니다.

    * 개인 IP 주소는 NVA 네트워크 인터페이스에 할당 되어야 합니다.

    * NVA는 가상 허브에 배포 되지 않습니다. 별도의 가상 네트워크에 배포 해야 합니다.

    *  NVA 가상 네트워크에는 하나 이상의 가상 네트워크가 연결 되어 있을 수 있습니다. 이 문서에서는 NVA 가상 네트워크를 ' 간접 스포크 VNet '으로 참조 합니다. 이러한 가상 네트워크는 VNet 피어 링을 사용 하 여 NVA VNet에 연결할 수 있습니다. Vnet 피어 링 링크는 위의 그림에서 VNet 1, VNet 2 및 NVA VNet 사이의 검은색 화살표로 표시 됩니다.
*  두 개의 가상 네트워크를 만들었습니다. 스포크 Vnet 사용 됩니다.

    * VNet 스포크 주소 공간은: VNet1:10.0.2.0/24 및 VNet2:10.0.3.0/24입니다. 가상 네트워크를 만드는 방법에 대 한 정보가 필요한 경우 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조 하세요.

    * Vnet에 가상 네트워크 게이트웨이가 없는지 확인 합니다.

    * Vnet에는 게이트웨이 서브넷이 필요 하지 않습니다.

## <a name="signin"></a>1. 로그인

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

## <a name="vwan"></a>2. 가상 WAN 만들기

가상 WAN 만들기 다음 예제 값을 사용 합니다.

* **가상 wan 이름:** myvirtualwan
* **리소스 그룹:** testRG
* **위치:** 미국 서 부

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. 허브 만들기

허브를 만듭니다. 다음 예제 값을 사용 합니다.

* **위치:** 미국 서 부
* **이름:** westushub
* **허브 개인 주소 공간:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. 허브 경로 테이블 만들기 및 적용

허브 경로 테이블로 허브를 업데이트 합니다. 다음 예제 값을 사용 합니다.

* **스포크 VNet 주소 공간:** (VNet1 및 VNet2) 10.0.2.0/24 및 10.0.3.0/24
* **DMZ NVA 네트워크 인터페이스 개인 IP 주소:** 10.0.4.5

1. 가상 WAN으로 이동 합니다.
2. 경로 테이블을 만들려는 허브를 클릭 합니다.
3. **...를 클릭**한 다음 **가상 허브 편집**을 클릭 합니다.
4. **가상 허브 편집** 페이지에서 아래로 스크롤하여 **라우팅에 대 한 테이블 사용**확인란을 선택 합니다.
5. **대상 접두사** 열에 주소 공간을 추가 합니다. **다음 홉으로 보내기** 열에서 DMZ nva 네트워크 인터페이스 개인 IP 주소를 추가 합니다.
6. **확인** 을 클릭 하 여 경로 테이블 설정으로 허브 리소스를 업데이트 합니다.

## <a name="connections"></a>5. VNet 연결 만들기

각 간접 스포크 VNet (VNet1 및 VNet2)에서 허브로의 가상 네트워크 연결을 만듭니다. 이러한 가상 네트워크 연결은 위의 그림에 표시 된 파란색 화살표로 표시 됩니다. 그런 다음 NVA VNet에서 허브 (그림의 검은색 화살표)로 VNet 연결을 만듭니다.

 이 단계에서는 다음 값을 사용할 수 있습니다.

| 가상 네트워크 이름| 연결 이름|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

연결 하려는 각 가상 네트워크에 대해 다음 절차를 반복 합니다.

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
