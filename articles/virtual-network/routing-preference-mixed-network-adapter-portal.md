---
title: 가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기 - Azure portal
description: 라우팅 기본 설정을 두 가지 모두 선택하여 사용하는 방법 알아보기
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679660"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기

본 문서에서는 VM의 [라우팅 기본 설정](./routing-preference-overview.md) 옵션 두 가지(인터넷과 Microsoft 전역 네트워크)를 모두 구성하는 방법을 보여 줍니다. Microsoft 전역 네트워크를 통하여 라우팅되는 공용 IP를 사용하는 가상 네트워크 인터페이스와 ISP 네트워크를 통하여 라우팅되는 공용 IP를 사용하는 가상 네트워크 인터페이스를 각각 하나씩 사용하면 가능합니다.

## <a name="prerequisites"></a>필수 구성 요소

Create a virtual machine with a public IP address following instructions described in [Azure Portal을 사용하여 고정 공용 IP 주소를 갖는 가상 머신 만들기](./virtual-network-deploy-static-pip-arm-portal.md)에 설명된 지침에 따라 Azure Portal을 사용하여 공용 IP 주소를 갖는 가상 머신을 만듭니다. 공용 IP의 라우팅 기본 설정에 대한 기본값은 **Microsoft 전역 네트워크** 를 통하여 가능합니다. 공용 IP가 있는 가상 머신을 만들고 나면, 라우팅 기본 설정이 **인터넷** 으로 구성되어 있는 전송 ISP 네트워크를 통하여 트래픽을 라우팅하는 VM에 두 번째 공용 IP를 추가합니다.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>라우팅 기본 설정이 인터넷으로 되어 있는 공용 IP 주소 만들기
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다. 
3. 검색 상자에 *공용 IP 주소* 를 입력합니다.
4. 검색 결과에서 **공용 IP 주소** 를 선택합니다. 그런 다음, **공용 IP 주소** 페이지에서 **만들기** 를 선택합니다.
5. **라우팅 기본 설정** 옵션에서 **인터넷** 을 선택합니다.

      ![공용 IP 주소 만들기](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > 공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 그러나 라우팅 기본 설정은 현재 IPV4만 지원합니다.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>네트워크 인터페이스를 만들어 공용 IP에 연결하기

1. 기본값으로 설정된 Azure Portal을 사용하여 [네트워크 인터페이스](./routing-preference-overview.md)를 만듭니다. 
1. 이전 섹션에서 만들어진 [네트워크 인터페이스에 공용 IP를 연결](./associate-public-ip-address-vm.md)합니다. IP 주소가 표시되는 데에는 몇 초 정도 걸릴 수 있습니다. 해당 IP 구성에 할당된 공용 IP 주소가 다음과 같은지 확인합니다.

    ![공용 IP 연결](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>VM에 네트워크 인터페이스 연결

1. [이전 섹션에서 만든 네트워크 인터페이스를 가상 머신에 연결합니다](./virtual-network-network-interface-vm.md).
2. 이제 가상 머신에 연결된 가상 네트워크 인터페이스 둘 중 하나는 Microsoft 전역 네트워크를 통하여 라우팅되는 공용 IP를 이용하고 나머지 하나는 ![VM에 네트워크 인터페이스 연결](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png)에 표시된 것과 같이 ISP 네트워크를 통하여 라우팅되는 공용 IP를 이용하는 것을 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
- [라우팅 기본 설정을 사용하는 공용 IP](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- [VM에 대한 라우팅 기본 설정을 구성합니다](tutorial-routing-preference-virtual-machine-portal.md).
- [PowerShell을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정을 구성합니다](routing-preference-powershell.md).
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.