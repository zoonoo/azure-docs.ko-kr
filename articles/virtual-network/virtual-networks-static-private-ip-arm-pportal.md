---
title: VM의 개인 IP 주소 구성 - Azure Portal
description: Azure Portal을 사용하여 가상 머신에 대한 개인 IP 주소를 구성하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015947"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Azure Portal을 사용하여 VM의 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

다음 샘플 단계를 수행하려면 단순한 환경이 이미 만들어져 있어야 합니다. 이 문서에 표시된 대로 단계를 실행하려면 먼저 [가상 네트워크를 만듭니다](quick-create-portal.md#create-a-virtual-network). 그러나 3단계에서는 다음 값을 대신 사용합니다.

| 설정 | 값 |
| ------- | ----- |
| 이름 | *TestVNet* |
| 주소 공간 | *192.168.0.0/16* |
| Resource group | **TestRG**(필요한 경우 **새로 만들기** 를 선택하여 만들기) |
| 서브넷 - 이름 | *FrontEnd* |
| 서브넷 - 주소 범위 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>고정 개인 IP 주소 테스트용 VM 만들기
Resource Manager 배포 모드에서 VM을 만드는 경우 Azure Portal을 사용하여 고정 개인 IP 주소를 설정할 수 없습니다. 대신 VM을 먼저 만듭니다. 그런 다음, 개인 IP를 고정으로 설정할 수 있습니다.

*TestVNet* 이라는 가상 네트워크의 *FrontEnd* 서브넷에 *DNS01* 이라는 VM을 만들려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **리소스 만들기** 를 선택합니다.

    ![리소스를 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. **컴퓨팅** > **가상 머신** 을 선택합니다.

    ![VM 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. **기본 사항** 에서 다음 표에 설명된 대로 항목 값을 지정합니다. 다음으로, **다음&nbsp;:&nbsp;디스크** 를 선택한 후 **다음&nbsp;:&nbsp;네트워킹** 을 선택합니다.

    | 항목 | 값 |
    | --- | --- |
    | **구독** | 현재 구독 |
    | **리소스 그룹** | **TestRG**(드롭다운 목록에서 선택) |
    | **가상 머신 이름** | *DNS01* |
    | **지역** | **(미국) 미국 동부** |
    | **이미지** | **Windows Server 2019 Datacenter** |
    | **크기** | **B1ls** 의 **VM 크기**, **표준** 의 **제품** |
    | **사용자 이름** | 관리자 계정의 사용자 이름 |
    | **암호** | 관리자 계정 사용자 이름의 암호 |
    | **암호 확인** | 암호 다시 |

    ![기본 사항 탭, 가상 머신 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. **네트워킹** 에서 다음 표에 설명된 대로 항목의 값을 지정한 후 **다음** 을 선택합니다.

    | 항목 | 값 |
    | --- | --- |
    | **가상 네트워크** | **TestVNet** |
    | **서브넷** | **FrontEnd** |

    ![네트워킹 탭, 가상 머신 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. **관리** 의 **진단 스토리지 계정** 에서 **vnetstorage** 를 선택합니다. 해당 스토리지 계정이 목록에 표시되지 않으면 **새로 만들기** 를 선택하고, **이름** 을 *vnetstorage* 로 지정하고, **확인** 을 선택합니다. 마지막으로, **검토&nbsp;+&nbsp;만들기** 를 선택합니다.

    ![관리 탭, 가상 머신 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. **검토 + 만들기** 에서 개요 정보를 검토한 다음, **만들기** 를 선택합니다.

    ![검토 + 만들기 탭, 가상 머신 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

VM이 만들어지면 다음 메시지가 나타납니다.

![배포 완료 메시지, 가상 머신 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>VM의 개인 IP 주소 정보 검색
새 VM의 개인 IP 주소 정보를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 VM을 찾습니다. **가상 머신** 을 검색하여 선택합니다.

    ![가상 머신, 검색 상자, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 새 VM의 이름(**DNS01**)을 선택합니다.

    ![가상 머신 목록, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. **네트워킹** 을 선택하고 나열된 유일한 네트워크 인터페이스를 선택합니다.

    ![네트워크 인터페이스, 네트워킹, 가상 머신, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. **IP 구성** 을 선택하고 표에 나열된 IP 구성을 선택합니다.

    ![IP 구성, 네트워크 인터페이스, 네트워킹, 가상 머신, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. **개인 IP 주소 설정** 의 **TestVNet/FrontEnd** 가상 네트워크/서브넷에서 **할당** 값(**동적** 또는 **정적**) 및 **IP 주소** 를 기록해 둡니다.

    ![동적 또는 정적 할당, 이전 개인 IP 주소 설정, IP 구성, 네트워크 인터페이스, 네트워킹, 가상 머신, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소 추가
새 VM에 고정 개인 IP 주소를 추가하려면 다음을 수행합니다.

1. IP 구성 페이지에서 개인 IP 주소에 대한 할당을 **정적** 으로 설정합니다.
2. 개인 **IP 주소** 를 *192.168.1.101* 로 변경한 후 **저장** 을 선택합니다.
   
    ![동적 또는 정적 할당, 새 개인 IP 주소 설정, IP 구성, 네트워크 인터페이스, 네트워킹, 가상 머신, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> **저장** 을 선택한 후에도 할당이 **동적** 으로 설정되어 있으면 입력한 IP 주소가 이미 사용 중인 것입니다. 다른 IP 주소를 사용해 보세요.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소 제거
VM에서 고정 개인 IP 주소를 제거하려면 다음을 수행합니다.

IP 구성 페이지에서 개인 IP 주소에 대한 할당을 **동적** 으로 설정한 후 **저장** 을 선택합니다.

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

VM의 운영 체제 내에서 Azure VM에 할당된 ‘개인’ IP를 정적으로 할당하면 안 됩니다. [VM에 많은 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)하는 경우와 같이 필요한 경우에만 개인 IP의 정적 할당을 수행합니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 일치하는지 확인합니다. 그러지 않으면 VM에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다.

또한 가상 머신의 운영 체제 내에서 Azure 가상 머신에 할당된 ‘공용’ IP 주소는 수동으로 할당하면 안 됩니다.

## <a name="next-steps"></a>다음 단계

[IP 주소 설정](virtual-network-network-interface-addresses.md) 관리에 대해 자세히 알아봅니다.
