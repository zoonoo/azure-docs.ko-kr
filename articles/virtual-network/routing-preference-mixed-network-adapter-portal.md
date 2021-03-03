---
title: 가상 컴퓨터에 대 한 라우팅 기본 설정 옵션 모두 구성-Azure Portal
description: 라우팅 기본 설정 선택 항목을 모두 사용 하도록 설정 하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679660"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>가상 컴퓨터에 대 한 라우팅 기본 설정 옵션 모두 구성

이 문서에서는 VM (가상 머신)에 대 한 [라우팅 기본 설정](./routing-preference-overview.md) 옵션 (인터넷 및 Microsoft 글로벌 네트워크)을 구성 하는 방법을 보여 줍니다. 이는 두 개의 가상 네트워크 인터페이스, Microsoft 글로벌 네트워크를 통해 라우팅되는 공용 IP가 있는 하나의 네트워크 인터페이스 및 ISP 네트워크를 통해 라우팅되는 공용 IP를 사용 하는 네트워크 인터페이스를 사용 하 여 수행 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Portal를 사용 하 여 [Azure Portal 사용 하 여 고정 공용 ip 주소](./virtual-network-deploy-static-pip-arm-portal.md) 를 사용 하는 가상 머신 만들기에 설명 된 지침에 따라 공용 ip 주소를 사용 하 여 가상 머신을 만듭니다. 공용 IP에 대 한 기본 라우팅 기본 설정은 **Microsoft 글로벌 네트워크** 를 통해 선택할 수 있습니다. 공용 IP가 생성 된 가상 머신이 있는 경우 전송 ISP 네트워크를 통해 트래픽을 라우팅하는 VM에 두 번째 공용 IP를 추가 하 고 라우팅 기본 설정이 **인터넷** 으로 구성 됩니다.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>라우팅 기본 설정 선택 인터넷을 사용 하 여 공용 IP 주소 만들기
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다. 
3. 검색 상자에 *공용 IP 주소* 를 입력합니다.
4. 검색 결과에서 **공용 IP 주소** 를 선택합니다. 그런 다음, **공용 IP 주소** 페이지에서 **만들기** 를 선택합니다.
5. **라우팅 기본 설정** 옵션에서 **인터넷** 을 선택합니다.

      ![공용 IP 주소 만들기](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > 공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 그러나 라우팅 기본 설정은 현재 IPV4만 지원합니다.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>네트워크 인터페이스를 만들고 공용 IP 연결

1. 기본 설정으로 Azure Portal를 사용 하 여 [네트워크 인터페이스](./routing-preference-overview.md) 를 만듭니다. 
1. 공용 IP를 이전 섹션에서 만든 [네트워크 인터페이스에 연결](./associate-public-ip-address-vm.md) 합니다. IP 주소가 표시 되는 데 몇 초 정도 걸릴 수 있습니다. 다음과 같이 IP 구성에 할당 된 공용 IP 주소를 확인 합니다.

    ![공용 IP 연결](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>VM에 네트워크 인터페이스 연결

1. [이전 섹션에서 만든 네트워크 인터페이스를 가상 머신에 연결](./virtual-network-network-interface-vm.md)합니다.
2. 이제 가상 머신과 연결 된 두 개의 가상 네트워크 인터페이스를 볼 수 있습니다. 하나는 Microsoft 글로벌 네트워크를 통해 라우팅되는 공용 IP를 사용 하 고 다른 하나는 ISP 네트워크를 통해로 라우팅됩니다. 여기에는  ![ 네트워크 인터페이스를 VM에 연결 합니다.](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>다음 단계
- [라우팅 기본 설정을 사용하는 공용 IP](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- [VM에 대한 라우팅 기본 설정을 구성합니다](tutorial-routing-preference-virtual-machine-portal.md).
- [PowerShell을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정을 구성합니다](routing-preference-powershell.md).
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.