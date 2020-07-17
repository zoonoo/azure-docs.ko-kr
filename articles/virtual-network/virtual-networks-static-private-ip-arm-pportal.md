---
title: Vm에 대 한 개인 IP 주소 구성-Azure Portal
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707857"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Azure Portal를 사용 하 여 VM에 대 한 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

다음 샘플 단계에서는 간단한 환경이 이미 생성 된 것으로 간주 합니다. 이 문서에 표시 된 대로 단계를 실행 하려면 먼저 [가상 네트워크를 만듭니다](quick-create-portal.md#create-a-virtual-network). 그러나 3 단계에서 다음 값을 대신 사용 합니다.

| Setting | Value |
| ------- | ----- |
| Name | *TestVNet* |
| 주소 공간 | *192.168.0.0/16* |
| Resource group | **TestRG** (필요한 경우 새로 만들기를 선택 하 여 **새로** 만듭니다.) |
| 서브넷 - 이름 | *엔드* |
| 서브넷 - 주소 범위 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>정적 개인 IP 주소를 테스트 하기 위한 VM 만들기
리소스 관리자 배포 모드에서 VM을 만들 때 Azure Portal를 사용 하 여 정적 개인 IP 주소를 설정할 수 없습니다. 대신 VM을 먼저 만듭니다. 그런 다음 해당 개인 IP를 정적으로 설정할 수 있습니다.

*Testvnet*이라는 가상 네트워크의 *프런트 엔드* 서브넷에 *DNS01* 이라는 VM을 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **리소스 만들기**를 선택 합니다.

    ![리소스 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. **계산**  >  **가상 머신**를 선택 합니다.

    ![VM 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. **기본 사항**에서 다음 표에 설명 된 대로 항목의 값을 지정 합니다. 그런 후 **다음 &nbsp; : &nbsp; 디스크** , 다음 ** &nbsp; : &nbsp; 네트워킹**을 차례로 선택 합니다.

    | 항목 | 값 |
    | --- | --- |
    | **구독** | 현재 구독 |
    | **리소스 그룹** | **TestRG** (드롭다운 목록에서 선택) |
    | **가상 머신 이름** | *DNS01* |
    | **지역** | **(미국) 미국 동부** |
    | **이미지** | **Windows Server 2019 Datacenter** |
    | **크기** | **B1ls**의 **VM 크기** , **표준** **제공** |
    | **사용자 이름** | 관리자 계정의 사용자 이름입니다. |
    | **암호** | 관리자 계정의 사용자 이름에 대 한 암호 |
    | **암호 확인** | 암호 다시 입력 |

    ![기본 탭에서 가상 컴퓨터 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. **네트워킹**에서 다음 표에 설명 된 대로 항목의 값을 지정한 후 **다음**을 선택 합니다.

    | 항목 | 값 |
    | --- | --- |
    | **가상 네트워크** | **TestVNet** |
    | **서브넷** | **엔드** |

    ![네트워킹 탭, 가상 컴퓨터 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. **관리**의 **진단 저장소 계정**에서 **vnetstorage**를 선택 합니다. 해당 저장소 계정이 목록에 표시 되지 않으면 **새로 만들기**를 선택 하 고, *Vnetstorage*의 **이름을** 지정 하 고, **확인**을 선택 합니다. 마지막으로 **검토 &nbsp; + &nbsp; 만들기**를 선택 합니다.

    ![관리 탭에서 가상 컴퓨터를 만듭니다. Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. **검토 + 만들기**에서 개요 정보를 검토 한 다음 **만들기**를 선택 합니다.

    ![검토 + 만들기 탭, 가상 컴퓨터 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

VM을 만든 후에는 다음 메시지가 표시 됩니다.

![배포 완료 메시지, 가상 컴퓨터 만들기, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>VM에 대 한 개인 IP 주소 정보 검색
새 VM에 대 한 개인 IP 주소 정보를 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 VM을 찾습니다. **가상 머신**을 검색하여 선택합니다.

    ![가상 컴퓨터, 검색 상자, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 새 VM의 이름 (**DNS01**)을 선택 합니다.

    ![가상 컴퓨터 목록, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. **네트워킹**을 선택 하 고 나열 된 유일한 네트워크 인터페이스를 선택 합니다.

    ![네트워크 인터페이스, 네트워킹, 가상 컴퓨터, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. **Ip 구성**을 선택 하 고 테이블에 나열 된 ip 구성을 선택 합니다.

    ![IP 구성, 네트워크 인터페이스, 네트워킹, 가상 컴퓨터, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. **개인 IP 주소 설정**의 **Testvnet/프런트 엔드** 가상 네트워크/서브넷에서 **할당** 값 (**동적** 또는 **정적**) 및 **IP 주소**를 확인 합니다.

    ![동적 또는 정적 할당, 이전 개인 IP 주소 설정, IP 구성, 네트워크 인터페이스, 네트워킹, 가상 컴퓨터, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소 추가
새 VM에 정적 개인 IP 주소를 추가 하려면 다음을 수행 합니다.

1. IP 구성 페이지에서 개인 IP 주소에 대 한 할당을 **고정**으로 설정 합니다.
2. 개인 **IP 주소** 를 *192.168.1.101*로 변경 하 고 **저장**을 선택 합니다.
   
    ![동적 또는 정적 할당, 새 개인 IP 주소 설정, IP 구성, 네트워크 인터페이스, 네트워킹, 가상 컴퓨터, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> **저장** 을 선택한 후에도 할당이 **동적**으로 설정 되어 있으면 입력 한 IP 주소가 이미 사용 중입니다. 다른 IP 주소를 사용해 보세요.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소 제거
VM에서 고정 개인 IP 주소를 제거 하려면 다음을 수행 합니다.

IP 구성 페이지에서 개인 IP 주소에 대 한 할당을 **동적**으로 설정 하 고 **저장**을 선택 합니다.

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

VM의 운영 체제 내에서 Azure VM에 할당 된 *개인* IP를 정적으로 할당 해서는 안 됩니다. [Vm에 여러 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)하는 경우와 같이 필요한 경우에만 개인 ip에 대 한 정적 할당을 수행 합니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정 하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당 된 개인 ip 주소와 일치 하는지 확인 합니다. 그렇지 않으면 VM에 대 한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다.

또한 가상 컴퓨터의 운영 체제 내에서 Azure 가상 컴퓨터에 할당 된 *공용* IP 주소를 수동으로 할당 해서는 안 됩니다.

## <a name="next-steps"></a>다음 단계

[IP 주소 설정](virtual-network-network-interface-addresses.md) 관리에 대해 자세히 알아봅니다.
