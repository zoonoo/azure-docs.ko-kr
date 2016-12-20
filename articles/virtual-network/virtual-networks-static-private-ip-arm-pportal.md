---
title: "Azure Portal을 사용하여 ARM 모드에서 정적 개인 IP를 설정하는 방법 | Microsoft Docs"
description: "개인 IP(DIP) 및 Azure 포털을 사용하여 ARM 모드에서 관리 방법 이해"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 5d56a0d2c9c44d37520c6a9a3db326f9bdc5420e
ms.openlocfilehash: e4f6d14f79536c58e40d3ac4b48d00a8c397f1b4


---
# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Azure 포털에서 정적 개인 IP 주소를 설정하는 방법

> [!div class="op_single_selector"]
- [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
- [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
- [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
- [Azure Portal(클래식)](virtual-networks-static-private-ip-classic-pportal.md)
- [PowerShell(클래식)](virtual-networks-static-private-ip-classic-ps.md)
- [Azure CLI(클래식)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 Resource Manager 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-classic-pportal.md)할 수도 있습니다.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

아래 샘플에는 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 단계를 실행하려는 경우 먼저 [vnet 만들기](virtual-networks-create-vnet-arm-pportal.md)에 설명된 테스트 환경을 구축합니다.

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>정적 개인 IP 주소 테스트용 VM을 만드는 방법
Azure 포털을 사용하여 리소스 관리자 배포 모드에서 VM을 만드는 동안에는 정적 개인 IP 주소를 설정할 수 없습니다. VM를 먼저 만들어야, tehn에서 해당 개인 IP를 정적으로 설정합니다.

*TestVNet*이라는 VNet의 *FrontEnd* 서브넷에 *DNS01*이라는 VM을 만들려면 다음 단계를 따르세요.

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 아래 그림과 같이 **새로 만들기** > **계산** > **Windows Server 2012 R2 Datacenter**를 클릭하고 **배포 모델 선택** 목록에 **리소스 관리자**가 이미 표시되는지 확인한 후 **만들기**를 클릭합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. **기본 사항** 블레이드에서 만들 VM의 이름(이 시나리오에서는*DNS01* ), 로컬 관리자 계정 및 암호를 아래 그림처럼 입력합니다.
   
    ![기본 사항 블레이드](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. 선택된 **위치**가 *미국 중부*인지 확인한 후 **리소스 그룹** 아래에서 **기존 선택**을 클릭하고 **리소스 그룹**을 다시 클릭한 후 *TestRG*, **확인**을 차례로 클릭합니다.
   
    ![기본 사항 블레이드](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. **크기 선택** 블레이드에서 **A1 표준**을 선택하고 **선택**을 클릭합니다.
   
    ![크기 선택 블레이드](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. **설정** 블레이드에서 다음 속성이 아래 값으로 설정되어 있는지 확인하고 **확인**을 클릭합니다.
   
    -**저장소 계정**: *vnetstorage*
   
   * **네트워크**: *TestVNet*
   * **서브넷**: *FrontEnd*
     
     ![크기 선택 블레이드](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. **요약** 블레이드에서 **확인**을 클릭합니다. 대시보드에 아래 타일이 표시되는지 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보를 검색하는 방법
위의 단계를 사용하여 만든 VM에 대한 정적 개인 IP 주소를 보려면 아래 단계를 실행합니다.

1. Azure Portal에서 **모두 찾아보기** > **Virtual machines** > **DNS01** > **모든 설정** > **네트워크 인터페이스**를 클릭한 후 나열된 네트워크 인터페이스만 클릭합니다.
   
    ![VM 타일 배포](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. **네트워크 인터페이스** 블레이드에서 **모든 설정** > **IP 주소**를 클릭하고 **할당** 및 **IP 주소** 값을 확인합니다.
   
    ![VM 타일 배포](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소를 추가하는 방법
위의 단계를 사용하여 만든 VM에 정적 개인 IP 주소를 추가하려면 다음 단계를 따르세요.

1. 위에 표시된 **IP 주소** 블레이드에서 **할당** 아래에 **정적**을 클릭합니다.
2. **IP 주소**에 *192.168.1.101*을 입력하고 **저장**을 클릭합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> **저장**을 클릭한 후에도 할당이 **동적**으로 설정된 것으로 확인되면 입력한 IP 주소가 이미 사용 중임을 의미합니다. 다른 IP 주소로 시도하세요.
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소를 제거하는 방법
위에서 만든 VM에서 정적 개인 IP 주소를 제거하려면 다음 단계를 수행합니다.

1. 위에 표시된 **IP 주소** 블레이드에서 **할당** 아래에 **동적**을 클릭하고 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
* [예약된 공용 IP](virtual-networks-reserved-public-ip.md) 주소에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
* [예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 참조합니다.




<!--HONumber=Nov16_HO3-->


