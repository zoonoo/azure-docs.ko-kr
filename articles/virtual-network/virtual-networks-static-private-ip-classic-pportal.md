---
title: "VM(클래식)에 대한 개인 IP 주소 구성 - Azure Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 가상 컴퓨터(클래식)에 대한 개인 IP 주소를 구성하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 컴퓨터(클래식)에 대한 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-arm-pportal.md)할 수도 있습니다.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

아래 샘플에는 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 단계를 실행하려는 경우 먼저 [vnet 만들기](virtual-networks-create-vnet-classic-pportal.md)에 설명된 테스트 환경을 구축합니다.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM을 만들 때 정적 개인 IP 주소를 지정하는 방법
*192.168.1.101*의 정적 개인 IP 주소를 사용하여 *TestVNet*이라는 VNet의 *FrontEnd* 서브넷에 *DNS01*이라는 VM을 만들려면 다음 단계를 따르세요.

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. **새로 만들기** > **계산** > **Windows Server 2012 R2 Datacenter**를 클릭하고 **배포 모델 선택** 목록에 **클래식**이 이미 표시되는지 확인한 후 **만들기**를 클릭합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. **VM 만들기** 블레이드에서 만들 VM의 이름(이 시나리오에서는*DNS01* ), 로컬 관리자 계정 및 암호를 입력합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. **옵션 구성** > **네트워크** > **Virtual Network**를 클릭하고 **TestVNet**을 클릭합니다. **TestVNet** 을 사용할 수 없는 경우 *미국 중부* 위치를 사용 중이고 이 문서의 시작 부분에서 설명한 테스트 환경을 만들었는지 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. **네트워크** 블레이드에서 현재 선택된 서브넷이 *FrontEnd*인지 확인하고 **IP 주소 할당** 아래에서 **IP 주소**를 클릭하고 **정적**을 클릭한 후 아래와 같이 **IP 주소**에 대해 *192.168.1.101*을 입력합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. **IP 주소** 블레이드에서 **확인**을 클릭한 후 **네트워크** 블레이드에서 **확인**을 클릭하고 **옵션 구성** 블레이드에서 **확인**을 클릭합니다.
7. **VM 만들기** 블레이드에서 **만들기**를 클릭합니다. 대시보드에 아래 타일이 표시되는지 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보를 검색하는 방법
위의 단계를 사용하여 만든 VM에 대한 정적 개인 IP 주소를 보려면 아래 단계를 실행합니다.

1. Azure Portal에서 **모두 찾아보기** > **가상 컴퓨터(클래식)** > **DNS01All** > **모든 설정** > **IP 주소**를 클릭하고 아래와 같이 IP 주소 할당 및 IP 주소를 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소를 제거하는 방법
위에서 만든 VM에서 정적 개인 IP 주소를 제거하려면 다음 단계를 수행합니다.

1. 위에 표시된 **IP 주소** 블레이드에서 **IP 주소 할당** 오른쪽에서 **동적**을 클릭한 후 **저장**, **예**를 차례로 클릭합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소를 추가하는 방법
위의 단계를 사용하여 만든 VM에 정적 개인 IP 주소를 추가하려면 다음 단계를 따르세요.

1. 위에 표시된 **IP 주소** 블레이드에서 **IP 주소 할당** 오른쪽에서 **정적**을 클릭합니다.
2. **IP 주소**에 *192.168.1.101*을 입력하고 **저장**을 클릭한 후 **예**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
* [예약된 공용 IP](virtual-networks-reserved-public-ip.md) 주소에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
* [예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 참조합니다.

