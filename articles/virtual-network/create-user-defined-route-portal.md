---
title: 네트워크 가상 어플라이언스를 통해 네트워크 트래픽을 라우팅할 사용자 정의 경로를 만듭니다. Azure Portal | Microsoft Docs
description: 네트워크 가상 어플라이언스를 통해 네트워크 트래픽을 라우팅하여 Azure의 기본 라우팅을 재정의하는 사용자 정의 경로를 만드는 방법에 대해 알아보세요.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2018
ms.locfileid: "29766248"
---
# <a name="create-a-user-defined-route---azure-portal"></a>사용자 정의 경로 만들기 - Azure Portal

이 자습서에서는 네트워크 가상 어플라이언스를 통해 두 [가상 네트워크](virtual-networks-overview.md) 서브넷 간의 트래픽을 라우팅하는 사용자 정의 경로를 만드는 방법에 대해 알아봅니다. 네트워크 가상 어플라이언스는 방화벽과 같은 네트워크 응용 프로그램을 실행하는 가상 머신입니다. Azure 가상 네트워크에 배포할 수 있는 미리 구성된 네트워크 가상 어플라이언스에 대한 자세한 내용은 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)를 참조하세요.

가상 네트워크에서 서브넷을 만들면 다음 그림과 같이 Azure에서 모든 서브넷의 리소스가 서로 통신할 수 있게 하는 기본 [시스템 경로](virtual-networks-udr-overview.md#system-routes)를 만듭니다.

![기본 경로](./media/create-user-defined-route/default-routes.png)

이 자습서에서는 다음 그림과 같이 공용, 사설 및 DMZ 서브넷을 사용하여 가상 네트워크를 만듭니다. 일반적으로 웹 서버는 공용 서브넷에 배포할 수 있으며, 응용 프로그램 또는 데이터베이스 서버는 사설 서브넷에 배포할 수 있습니다. DMZ 서브넷에서 네트워크 가상 어플라이언스 역할을 수행할 가상 컴퓨터를 만들고, 필요에 따라 네트워크 가상 어플라이언스를 통해 통신하는 각 서브넷에서 가상 컴퓨터를 만듭니다. 다음 그림과 같이 공용 서브넷과 사설 서브넷 간의 모든 트래픽은 어플라이언스를 통해 라우팅됩니다.

![사용자 정의 경로](./media/create-user-defined-route/user-defined-routes.png)

이 문서에서는 Resource Manager 배포 모델을 통해 사용자 정의 경로를 만드는 단계를 제공합니다. 이 모델은 사용자 정의 경로를 만들 때 사용하는 것이 좋습니다. 사용자 정의 경로(클래식)를 만들어야 하는 경우 [사용자 정의 경로 만들기(클래식)](virtual-network-create-udr-classic-ps.md)를 참조하세요. Azure 배포 모델에 대해 잘 모르는 경우에는 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요](virtual-networks-udr-overview.md#user-defined)를 참조하세요.

## <a name="create-routes-and-network-virtual-appliance"></a>경로 및 네트워크 가상 어플라이언스 만들기

1. **필수 구성 요소**: [가상 네트워크 만들기](#create-a-virtual-network)의 단계를 완료하여 두 서브넷이 있는 가상 네트워크를 만듭니다.
2. 가상 네트워크를 만든 후 인터넷 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다. [Azure 계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)을 사용하여 로그인합니다.
3. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. 리소스 그룹이 필수 구성 요소의 일부로 만들어졌습니다.
4. 다음 그림과 같이 **myVnet**을 클릭하세요.

    ![네트워크 인터페이스 설정](./media/create-user-defined-route/virtual-network.png)

5. 네트워크 가상 어플라이언스에 대한 서브넷을 만듭니다.
 
    - **myVnet**의 왼쪽에 있는 **설정**에서 **서브넷**을 클릭합니다.
    - 다음 그림과 같이 **+ 서브넷**을 클릭합니다.

        ![서브넷](./media/create-user-defined-route/subnets.png) 
    - **서브넷 추가** 아래에 다음 값을 입력한 다음 **확인**을 클릭합니다.

        |설정|값|
        |-----|-----|
        |Name|DMZ|
        |주소 범위(CIDR 블록)|10.0.2.0/24|

6. 네트워크 가상 어플라이언스 가상 머신을 만듭니다.

    - 포털 왼쪽에서 **새로 만들기**를 클릭한 다음 **계산**을 클릭하고 **Windows Server 2016 Datacenter** 또는 **Ubuntu Server 16.04 LTS**를 클릭합니다.
    - 표시되는 **기본 사항** 블레이드에서 다음 값을 입력한 다음 **확인**을 클릭합니다.

        |설정|값|
        |---|---|
        |Name|myVm-Nva|
        |사용자 이름|azureuser|
        |암호 및 암호 확인|사용자가 선택한 암호|
        |구독|구독 선택|
        |리소스 그룹|**기존 사용**을 클릭한 다음 **myResourceGroup**을 선택합니다.|
        |위치|미국 동부|
    - 표시되는 **크기 선택** 블레이드에서 **DS1_V2 기본**을 클릭한 다음 **선택**을 클릭합니다.
    - 표시되는 **설정** 블레이드에서 **가상 네트워크**를 클릭합니다. 표시되는 **가상 네트워크 선택** 블레이드에서 **myVnet**을 클릭합니다.
    - **설정** 블레이드에서 **서브넷**을 클릭합니다. 표시되는 **서브넷 선택** 블레이드에서 **DMZ**를 클릭합니다. 
    - 나머지 설정의 기본값을 그대로 두고 **확인**을 클릭합니다.
    - 표시되는 **만들기** 블레이드에서 **만들기**를 클릭합니다. 가상 머신 배포에는 몇 분 정도 걸립니다.

    > [!NOTE]
    > Azure Portal은 가상 머신을 만들 뿐만 아니라 기본적으로 공용 IP 주소를 만들어 이를 가상 머신에 할당합니다. 프로덕션 환경에서 가상 머신을 배포하는 경우, 가상 머신에 공용 IP 주소를 할당하지 않을 수 있습니다. 대신 가상 네트워크 내의 다른 가상 머신에서 네트워크 가상 어플라이언스에 액세스할 수도 있습니다. 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소 관리](virtual-network-public-ip-address.md)를 참조하세요.

7. 정적 개인 IP 주소를 할당하고 이전 단계에서 만들어진 포털에서 네트워크 인터페이스에 대한 IP 전달을 사용 가능하게 합니다. 
    - 페이지 상단의 **리소스 검색** 상자에 *myVm-Nva*를 입력합니다.
    - **myVm-Nva**가 검색 결과에 표시되면 클릭합니다.
    - 왼쪽에 있는 **설정**에서 **네트워킹**을 클릭합니다.
    - **myVm-Nva - 네트워크 인터페이스**에서 네트워크 인터페이스 이름을 클릭합니다. 이름은 **myvm-nva***X*이며, 여기서 *X*는 포털에서 할당한 번호입니다.
    - 다음 그림과 같이 네트워크 인터페이스에 대한 **설정**에서 **IP 구성**을 클릭합니다.

        ![네트워크 인터페이스 설정](./media/create-user-defined-route/network-interface-settings.png)
        
    - **IP 전달** 설정에 대해 **사용**을 클릭한 다음 **저장**을 클릭합니다. 할당된 IP 주소로 주소가 지정되지 않은 트래픽을 수신하는 각 네트워크 인터페이스에 대해 IP 전달을 사용할 수 있어야 합니다. IP 전달을 사용하면 네트워크 인터페이스에 대한 Azure의 소스/대상 검사를 사용할 수 없습니다.
    - IP 구성 목록에서 **ipconfig1**을 클릭합니다.
    - 다음 그림과 같이 **ipconfig1**에서 개인 IP 주소 **할당**에 대해 **정적**을 클릭합니다.

        ![IP 구성](./media/create-user-defined-route/ip-configuration.png)
    - 앞 그림과 같이 **개인 IP 주소 설정**의 **IP 주소** 아래에 *10.0.2.4*를 입력합니다. 네트워크 인터페이스에 정적 IP 주소를 할당하면 네트워크 인터페이스가 연결된 가상 머신의 수명 동안 주소가 변경되지 않습니다. 입력한 주소는 현재 네트워크 인터페이스가 있는 DMZ 서브넷의 다른 리소스에 할당되지 않습니다. 
    - 구성을 저장하려면 **ipconfig1** 아래에서 **저장**을 클릭합니다. 네트워크 인터페이스가 저장되는 포털에서 알림을 받기 전에는 ipconfig1 상자를 닫지 마세요.
 
8. 두 개의 경로 테이블을 만듭니다. 경로 테이블에는 0개 이상의 경로가 있습니다.

    - 포털 왼쪽에서 **+새로 만들기** > **네트워킹** > **경로 테이블**을 클릭합니다.
    - **경로 테이블 만들기**에 다음 값을 입력한 다음 **만들기**를 클릭합니다.

        |설정|값|
        |---|---|
        |Name|myRouteTable-Public|
        |구독|구독 선택|
        |리소스 그룹|**기존 사용**을 선택한 다음 **myResourceGroup**을 선택합니다.|
        |위치|미국 동부|
    
    - 단계 8의 이전 하위 단계를 다시 완료하되 경로 테이블 이름을 *myRouteTable-Private*이라고 지정합니다.
9. *myRouteTable-Public* 경로 테이블에 경로를 추가하고 경로 테이블을 *공개* 서브넷에 연결합니다.

    - 포털 상단의 **리소스 검색** 상자에 *myRouteTable-Public*를 입력하세요. 검색 결과에 표시되면 **myRouteTable-Public**을 클릭합니다.
    - **myRouteTable-Public**의 **설정** 목록에서 **경로**를 클릭합니다.
    - **myRouteTable-Public**에서 **+ 추가**를 클릭합니다.
    -  기본적으로 Azure는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 라우팅합니다. *공개* 서브넷의 트래픽이 *비공개* 서브넷으로 직접 라우팅되는 대신 NVA를 통해 라우팅되도록 Azure의 기본 라우팅을 변경하는 경로를 만듭니다. 표시되는 **경로 추가** 블레이드에서 다음 값을 입력한 다음 **확인**을 클릭합니다.

        |설정|값|설명|
        |---|---|---|
        |경로 이름|ToPrivateSubnet|이 경로는 네트워크 가상 어플라이언스를 통해 비공개 서브넷에 트래픽을 전달합니다.|
        |주소 접두사|10.0.1.0/24| 이 주소 접두사(10.0.1.0 - 10.0.1.254) 내의 주소로 전송된 모든 트래픽이 네트워크 가상 어플라이언스로 전송됩니다.|
        |다음 홉 유형| **가상 어플라이언스** 선택||
        |다음 홉 주소|10.0.2.4| 네트워크 가상 어플라이언스에 연결된 네트워크 인터페이스의 정적 개인 IP 주소입니다. 주소를 지정할 수 있는 유일한 홉 유형은 **가상 어플라이언스**입니다.|

    - **myRouteTable-Public**의 **설정**에서 **서브넷**을 클릭합니다. 
    - **myRouteTable-Public - 서브넷**에서 **+ 연결**을 클릭합니다.
    - **서브넷 연결**에서 **가상 네트워크**를 클릭한 다음 **myVnet**를 클릭합니다.
    - **서브넷 연결**에서 **서브넷**을 클릭한 다음 **서브넷 선택**에서 **공개**를 클릭합니다. 
    - 구성을 저장하려면 **서브넷 연결**에서 **확인**을 클릭합니다. 서브넷에는 0개 또는 1개의 경로 테이블이 연결될 수 있습니다.
10. **myRouteTable-Private**을 검색하고 다음 설정으로 경로를 만든 다음 경로 테이블을 **myVnet** 가상 네트워크의 **비공개** 서브넷에 연결하여 9단계를 다시 완료합니다.

    |설정|값|설명|
    |---|---|---|
    |경로 이름|ToPublicSubnet|이 경로는 네트워크 가상 어플라이언스를 통해 공개 서브넷에 트래픽을 전달합니다.|
    |주소 접두사|10.0.0.0/24| 이 주소 접두사(10.0.0.0 - 10.0.1.254) 내의 주소로 전송된 모든 트래픽이 네트워크 가상 어플라이언스로 전송됩니다.|
    |다음 홉 유형| **가상 어플라이언스** 선택||
    |다음 홉 주소|10.0.2.4||

    비공개 및 공개 서브넷의 리소스 간 네트워크 트래픽은 네트워크 가상 어플라이언스를 통해 전달됩니다. 
11. **선택 사항:** 공개 및 비공개 서브넷에서 가상 머신을 만들고, [라우팅 확인](#validate-routing)의 단계를 완료하여 가상 머신 간의 통신이 네트워크 가상 어플라이언스를 통해 라우팅되는지 확인합니다. 
12. **선택 사항:** [리소스 삭제](#delete-resources)의 단계를 완료하여 이 자습서에서 만든 리소스를 삭제합니다.

## <a name="validate-routing"></a>라우팅 확인

1. 아직 수행하지 않은 경우 [경로 및 네트워크 가상 어플라이언스 만들기](#create-routes-and-network-virtual-appliance)의 단계를 완료합니다.
2. 다음 상자에서 **사용해 보세요** 단추를 클릭하여 Azure Cloud Shell을 엽니다. 메시지가 표시되면 [Azure 계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)을 사용하여 Azure에 로그인합니다. Azure 계정이 없으면 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)에 등록할 수 있습니다. Azure Cloud Shell은 미리 설치된 Azure 명령줄 인터페이스가 있는 무료 Bash 셸입니다. 

    다음 스크립트에서는 *공용* 서브넷과 *사설* 서브넷에 각각 하나씩 두 개의 가상 머신을 만듭니다. 또한 이 스크립트를 사용하면 운영 체제에서 네트워크 인터페이스를 통해 트래픽을 라우팅할 수 있도록 NVA 운영 체제 내의 네트워크 인터페이스에 대한 IP 전달을 사용하도록 설정합니다. 프로덕션 NVA는 일반적으로 트래픽을 라우팅하기 전에 검사하지만, 이 자습서에서는 간단한 NVA에서 트래픽을 검사하지 않고 라우팅합니다. 

    다음의 **Linux** 또는 **Windows** 스크립트에서 **복사** 단추를 클릭하여 스크립트 콘텐츠를 텍스트 편집기에 붙여넣습니다. *adminPassword* 변수의 암호를 변경한 다음 Azure Cloud Shell에 스크립트를 붙여넣습니다. [경로 및 네트워크 가상 어플라이언스 만들기](#create-routes-and-network-virtual-appliance)의 6단계에서 네트워크 가상 어플라이언스를 만들 때 선택한 운영 체제에 대한 스크립트를 실행합니다. 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. 공용 및 사설 서브넷의 가상 머신 간의 통신에 대한 유효성을 검사합니다. 

    - *myVm-Public* 가상 머신의 공용 IP 주소에 대한 [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm)(Linux) 또는 [원격 데스크톱](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm)(Windows) 연결을 엽니다.
    - *myVm-Public* 가상 머신의 명령 프롬프트에서 `ping myVm-Private`를 입력합니다. NVA에서 트래픽을 공용 서브넷에서 사설 서브넷으로 라우팅하므로 회신을 받습니다.
    - *myVm-Public* 가상 머신에서 공용 서브넷과 사설 서브넷의 가상 머신 간의 추적 경로를 실행합니다. 공용 서브넷 및 사설 서브넷의 가상 머신에 설치된 운영 체제에 따라 다음과 같은 적절한 명령을 입력합니다.
        - **Windows**: 명령 프롬프트에서 `tracert myvm-private` 명령을 실행합니다.
        - **Ubuntu**: `tracepath myvm-private` 명령을 실행합니다.
      트래픽은 10.0.1.4(사설 서브넷의 가상 컴퓨터)에 도달하기 전에 10.0.2.4(NVA)를 통해 전달합니다. 
    - *myVm-Private* 가상 머신에 연결하고 *myVm-Public* 가상 머신을 ping하여 이전 단계를 완료합니다. 추적 경로에서 10.0.0.4(공용 서브넷의 가상 머신)에 도달하기 전에 10.0.2.4를 통해 이동하는 통신을 보여 줍니다.

      > [!NOTE]
      > 이전 단계에서는 Azure 개인 IP 주소 간의 라우팅을 확인할 수 있습니다. 네트워크 가상 어플라이언스를 통해 트래픽을 공용 IP 주소로 전달 또는 프록시하려는 경우:
      > - 어플라이언스는 네트워크 주소 변환 또는 프록시 기능을 제공해야 합니다. 네트워크 주소 변환의 경우 어플라이언스는 원본 IP 주소를 자체 주소로 변환한 후 해당 요청을 공용 IP 주소로 전달해야 합니다. 어플라이언스가 원본 주소로 변환된 네트워크 주소를 가지고 있는지 또는 프록시 중인지 여부에 관계없이, Azure는 네트워크 가상 어플라이언스의 개인 IP 주소를 공용 IP 주소로 변환합니다. Azure에서 개인 IP 주소를 공용 IP 주소를 변환하는 데 사용하는 다양한 방법에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
      > - 경로 테이블에서 접두사가 0.0.0.0/0이고, 다음 홉 형식이 VirtualAppliance이고, 다음 홉 IP 주소가 10.0.2.4인 추가 경로(앞의 예제 스크립트 참조)를 참조하세요.
      >
    - **선택 사항**: Azure 내 두 가상 머신 간의 다음 홉에 대한 유효성을 검사하려면 Azure Network Watcher의 다음 홉 기능을 사용합니다. Network Watcher를 사용하기 전에 먼저 사용할 지역에 대한 [Azure Network Watcher 인스턴스를 만들어야](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 합니다. 이 자습서에서는 미국 동부 지역을 사용합니다. 이 지역에 대한 Network Watcher 인스턴스를 사용 설정한 후 다음 명령을 입력하여 공개 및 비공개 서브넷에서 가상 머신 간의 다음 홉 정보를 확인합니다.
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       출력에서는 *10.0.2.4*를 **nextHopIpAddress**로, *VirtualAppliance*를 **nextHopType**으로 반환합니다. 

> [!NOTE]
> 이 자습서의 개념을 설명하기 위해 공용 및 개인 서브넷 가상 머신에 공용 IP 주소가 할당되며, 두 가상 머신 모두에 대해 모든 네트워크 포트 액세스가 Azure 내에서 활성화됩니다. 프로덕션 환경에서 사용할 가상 머신을 만들 때 공용 IP 주소를 할당할 수 없으며, 네트워크 가상 어플라이언스를 앞에 배포하거나 서브넷, 네트워크 인터페이스 또는 둘 다에 네트워크 보안 그룹을 할당하여 사설 서브넷에 대한 네트워크 트래픽을 필터링할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

이 자습서에서는 두 서브넷이 있는 기존 가상 네트워크가 필요합니다. 다음 상자에서 **사용해 보세요** 단추를 클릭하여 가상 네트워크를 빠르게 만듭니다. **사용해 보세요** 단추를 클릭하면 [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이 열립니다. Cloud Shell에서 PowerShell 또는 Bash 셸을 실행하지만, 이 섹션에서는 Bash 셸을 사용하여 가상 네트워크를 만듭니다. Bash 셸에는 Azure 명령줄 인터페이스가 설치되어 있습니다. Cloud Shell에서 메시지가 표시되면 [Azure 계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)을 사용하여 Azure에 로그인합니다. Azure 계정이 없으면 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)에 등록할 수 있습니다. 이 자습서에 사용된 가상 네트워크를 만들려면 다음 상자에서 **복사** 단추를 클릭한 다음 Azure Cloud Shell에 스크립트를 붙여넣습니다.

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

포털, PowerShell 또는 Azure Resource Manager 템플릿을 사용하여 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [가상 네트워크 만들기](virtual-networks-create-vnet-arm-pportal.md)를 참조하세요.

## <a name="delete-resources"></a>리소스 삭제

이 자습서를 마친 후에는 사용 요금이 발생하지 않도록 작성했던 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하면 리소스 그룹에 있는 리소스도 모두 삭제됩니다. 포털이 열리면 다음 단계를 완료합니다.

1. 포털 검색 상자에 **myResourceGroup**을 입력합니다. 검색 결과에서 **myResourceGroup**을 클릭합니다.
2. **myResourceGroup** 블레이드에서 **삭제** 아이콘을 클릭합니다.
3. 삭제를 확인하려면 **리소스 그룹 이름 입력** 상자에 **myResourceGroup**을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [고가용성 네트워크 가상 어플라이언스](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)를 만듭니다.
- 네트워크 가상 어플라이언스에는 종종 여러 개의 네트워크 인터페이스와 IP 주소가 할당됩니다. [기존 가상 머신에 네트워크 인터페이스를 추가](virtual-network-network-interface-vm.md#vm-add-nic)하고 [기존 네트워크 인터페이스에 IP 주소를 추가](virtual-network-network-interface-addresses.md#add-ip-addresses)하는 방법에 대해 알아보세요. 모든 가상 머신 크기에는 둘 이상의 네트워크 인터페이스가 연결될 수 있지만, 각 가상 머신 크기에는 최대 네트워크 인터페이스 수가 지원됩니다. 각 가상 머신 크기가 지원하는 네트워크 인터페이스 수를 알아보려면 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 및 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 크기를 확인하세요. 
- [사이트 간 VPN 연결](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 통해 온-프레미스에서 터널 트래픽을 강제로 적용하는 사용자 정의 경로를 만듭니다.
