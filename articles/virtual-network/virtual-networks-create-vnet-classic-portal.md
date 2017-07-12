---
title: "Azure 가상 네트워크(클래식) 만들기 - 클래식 포털 | Microsoft Docs"
description: "Azure 클래식 포털에서 netcfg 파일을 사용하여 가상 네트워크(클래식)를 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6
ms.contentlocale: ko-kr
ms.lasthandoff: 01/31/2017


---

<a id="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal" class="xliff"></a>

# Azure 클래식 포털에서 netcfg 파일을 사용하여 가상 네트워크(클래식) 만들기
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 Azure 클래식 포털에서 클래식 배포 모델을 통해 netcfg 파일을 사용하여 가상 네트워크를 만드는 방법에 대해 설명합니다. 또한 [netffg 파일을 사용하지 않고 클래식 배포 모델을 통해 가상 네트워크를 만들거나](virtual-networks-create-vnet-classic-pportal.md) [Azure Portal을 사용하여 Azure Resource Manager 배포 모델을 통해 가상 네트워크를 만들](virtual-networks-create-vnet-arm-pportal.md) 수 있습니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

<a id="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal" class="xliff"></a>

## Microsoft Azure 클래식 포털에 네트워크 구성 파일로 VNet을 만드는 방법
Azure에서는 xml 파일을 사용하여 구독에 사용할 수 있는 모든 VNet을 정의합니다. 이 파일을 다운로드하고 편집하여 클래식 배포 모델을 통해 VNet을 만들거나 기존 VNet을 수정하거나 삭제할 수 있습니다. 이 문서에서는 네트워크 구성(또는 netcfg) 파일이라고 하는 파일을 다운로드하고, VNet을 추가하고, 파일을 업로드하여 VNet을 만드는 방법을 설명합니다. 네트워크 구성 파일에 대해 자세히 알아보려면 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 검토하세요.

Azure 클래식 포털을 통해 netcfg 파일을 사용하여 VNet을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://manage.windowsazure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 다음 그림과 같이 서비스 목록을 아래로 스크롤하고 **네트워크**, **내보내기**를 차례로 클릭합니다.

    ![Azure 가상 네트워크](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. **네트워크 구성 내보내기** 대화 상자에서 가상 네트워크 구성을 내보낼 구독을 선택한 다음 상자의 오른쪽 아래 모서리에 있는 확인 표시 단추를 클릭합니다.
4. 브라우저의 지침에 따라 **NetworkConfig.xml** 파일을 저장합니다. 파일을 저장하는 위치를 적어둡니다.
5. XML 또는 텍스트 편집기 응용 프로그램을 사용하여 4단계에서 저장한 파일을 열고 `<VirtualNetworkConfiguration>` 요소에서 `<VirtualNetworkSites>` 요소를 찾습니다. 기존 VNet이 있는 경우 각 VNet이 자체의 `<VirtualNetworkSite>` 요소에 나열되어 있습니다. 파일의 `<VirtualNetworkConfiguration>` 요소 내에 `<VirtualNetworkSites>` 요소가 없으면 파일 하나를 새로 만듭니다.
6. 기존 NetworkConfig 파일에 VNet이 없는 경우 이 시나리오에서 설명하는 VNet을 추가하면 이 파일은 다음 예제와 비슷하게 됩니다.

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. 네트워크 구성 파일을 저장합니다.
8. Azure 클래식 포털에서 다음 그림과 같이 **새로 만들기**, **Network Services**, **Virtual Network**, **구성 가져오기**를 차례로 클릭합니다.

    ![구성 가져오기](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. 다음 그림과 같이 **네트워크 구성 파일 가져오기** 대화 상자에서 **파일 찾아보기...**를 클릭하고, 7단계에서 파일을 저장한 폴더로 이동하여 파일을 선택한 다음 **열기**를 클릭합니다.

    ![네트워크 구성 파일 가져오기 페이지](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    상자의 오른쪽 아래 모서리에서 화살표 단추를 클릭하여 다음 단계로 이동합니다.

9. 다음 그림과 같이 **네트워크를 빌드하는 중** 대화 상자에서 새 VNet에 대한 항목을 확인합니다.

    ![네트워크를 빌드하는 중 페이지](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. VNet을 만들려면 이전 그림의 상자 오른쪽 모서리에 있는 확인 표시 단추를 클릭합니다. 잠시 후에 다음 그림과 같이 사용 가능한 VNet 목록에 해당 VNet이 표시됩니다.

    ![새 가상 네트워크](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)

