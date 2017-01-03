---
title: "가상 컴퓨터의 여러 IP 주소 - Azure CLI | Microsoft Docs"
description: "Azure CLI | Resource Manager를 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Azure CLI를 사용하여 가상 컴퓨터에 여러 IP 주소 할당

> [!div class="op_single_selector"]
> * [Azure 포털](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Azure VM(가상 컴퓨터)에는 하나 이상의 네트워크 인터페이스(NIC)가 연결되어 있습니다. 모든 NIC에는 하나 이상의 정적 또는 동적 공용 및 개인 IP 주소가 할당되어 있을 수 있습니다. VM에 여러 IP 주소를 할당하면 다음을 수행할 수 있습니다.

* 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
* 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.
* NIC의 개인 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IP 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../load-balancer/load-balancer-multiple-ip.md) 문서를 참조하세요.

VM에 연결된 모든 NIC에는 하나 이상의 IP 구성이 연결되어 있습니다. 각 구성에는 하나의 정적 또는 동적 개인 IP 주소가 할당됩니다. 각 구성에는 하나의 공용 IP 주소 리소스가 연결되어 있을 수도 있습니다. 공용 IP 주소 리소스에는 동적 또는 정적 IP 주소가 할당되어 있습니다. Azure의 IP 주소에 익숙하지 않은 경우 [Azure의 IP 주소](virtual-network-ip-addresses-overview-arm.md) 문서를 읽고 자세한 정보를 알아보세요.

이 문서에서는 PowerShell을 사용하여 Azure Resource Manager 배포 모델을 통해 만든 VM에 여러 IP 주소를 할당하는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>시나리오
단일 NIC가 있는 VM을 만들고 가상 네트워크에 연결합니다. VM은 세 개의 *개인* IP 주소와 두 개의 *공용* IP 주소가 필요합니다. IP 주소는 다음 IP 구성에 할당됩니다.

* **IPConfig-1:** *동적* 개인 IP 주소(기본값)와 *정적* 공용 IP 주소를 할당합니다.
* **IPConfig-2:** *정적* 개인 IP 주소와 *정적* 공용 IP 주소를 할당합니다.
* **IPConfig-3:** *동적* 개인 IP 주소를 할당하고 공용 IP 주소를 할당하지 않습니다.
  
    ![여러 IP 주소](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

NIC를 만들 때 IP 구성이 NIC에 연결되고 VM을 만들 때 NIC가 VM에 연결됩니다. 시나리오에 사용되는 IP 주소 유형은 예시입니다. 필요한 모든 IP 주소 및 할당 유형을 지정할 수 있습니다.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

다음 단계는 시나리오에 설명된 대로 여러 IP 주소를 가진 예시 VM을 만드는 방법을 설명합니다. 변수 이름과 IP 주소 유형을 구현에 필요한 대로 변경합니다.

1. [Azure CLI 설치 및 구성](../xplat-cli-install.md) 문서에 나오는 단계에 따라 Azure CLI를 설치 및 구성하고 Azure 계정에 로그인합니다.

2. 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)로 보내어 미리 보기를 등록합니다. 다음 경우에 나머지 단계를 완료하려 하지 마세요.
    - 미리 보기에 적용되었음을 알리는 전자 메일을 받을 때까지
    - 수신 메일의 지침을 따르기 전에
3. [가상 네트워크 및 서브넷](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)을 만들고 이어서[리소스 그룹을 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations). 이 문서에 개략적으로 설명된 정확한 시나리오에 따라 ``` --address-prefixes ``` 및 ```--address-prefix``` 필드를 다음으로 변경합니다.

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >위의 참조 문서는 리소스를 만들기 위한 위치로 유럽 서부를 사용하지만 이 문서는 미국 중서부를 사용합니다. 위치를 적절히 변경합니다.

4. VM에 [저장소 계정을 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account).


5. NIC에 할당하려는 NIC 및 IP 구성을 만듭니다. 필요에 따라 구성을 추가, 제거 또는 변경할 수 있습니다. 다음 구성은 시나리오에 설명되어 있습니다.

    **IPConfig-1**

    다음을 만들기 위해 수행할 명령을 입력합니다.

    - 고정 공용 IP 주소가 있는 공용 IP 주소 리소스
    - 공용 IP 주소 리소스와 동적 개인 IP 주소가 있는 IP 구성

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     고정 공용 IP 주소와 고정 개인 IP 주소가 있는 새 공용 IP 주소 리소스와 새 IP 구성을 만들려면 다음 명령을 입력합니다.
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    동적 개인 IP 주소가 있고 공용 IP 주소가 없는 IP 구성을 만들려면 다음 명령을 입력합니다.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >이 문서를 통해 모든 IP 구성을 단일 NIC에 할당하면 여러 IP 구성도 VM의 모든 NIC에 할당할 수 있습니다. 여러 NIC로 VM을 만드는 방법에 대해 자세히 알아보려면 여러 NIC를 사용하여 VM 만들기 문서를 참조하세요.

6. [Linux VM 만들기](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms) 문서입니다. 이 시나리오에서는 필요하지 않은 ```  --availset-name myAvailabilitySet \ ``` 속성을 반드시 제거합니다. 시나리오에 따라 적절한 위치를 사용합니다. 

    >[!WARNING] 
    > 선택한 위치에서 해당 VM 크기가 지원되지 않는 경우 VM 문서 만들기의 6단계는 실패합니다. 예를 들어 미국 중서부의 VM의 전체 목록을 가져오려면 다음 명령을 실행합니다. 이 위치 이름은 시나리오에 따라 변경할 수 있습니다.
    > 
    >       azure vm sizes --location westcentralus

    예를 들어 VM 크기를 표준 DS2 v2로 변경하려면 단순히 다음 속성 ```  --vm-size Standard_DS3_v2```을 6단계에서 ``` azure vm create ``` 명령에 추가합니다.

7. NIC와 연결된 IP 구성을 보려면 다음 명령을 입력합니다.

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>VM 운영 체제에 IP 주소 추가

여러 개인 IP 주소를 사용하여 만든 VM에 연결 및 로그인합니다. VM에 추가한 모든 개인 IP 주소(기본 포함)를 수동으로 추가해야 합니다. VM 운영 체제에 대한 다음 단계를 완료합니다.

### <a name="windows"></a>Windows

1. 명령 프롬프트에서 *ipconfig /all*을 입력합니다.  *기본* 개인 IP 주소(DHCP를 통한)만 표시됩니다.
2. 명령 프롬프트 창에 *ncpa.cpl*을 입력하여 **네트워크 연결** 창을 엽니다.
3. **로컬 영역 연결**에 대한 속성을 엽니다.
4. IPv4(인터넷 프로토콜 버전 4)를 두 번 클릭합니다.
5. **다음 IP 주소 사용**을 선택하고 다음 값을 입력합니다.

    * **IP 주소**: *기본* 개인 IP 주소를 입력합니다.
    * **서브넷 마스크**: 서브넷을 기준으로 설정됩니다. 예를 들어 서브넷이 /24이면 서브넷 마스크는 255.255.255.0입니다.
    * **기본 게이트웨이**: 서브넷의 첫 번째 IP 주소입니다. 서브넷이 10.0.0.0/24이면 게이트웨이 IP 주소는 10.0.0.1입니다.
    * **다음 DNS 서버 주소 사용** 을 클릭하고 다음 값을 입력합니다.
        * **기본 설정 DNS 서버**: 자체 DNS 서버를 사용하지 않는 경우 168.63.129.16을 입력합니다.  자체 DNS 서버를 사용하는 경우 서버의 IP 주소를 입력합니다.
    * **고급** 단추를 클릭하고 추가 IP 주소를 추가합니다. 8단계에 나열된 각 보조 개인 IP 주소를 기본 IP 주소에 대해 지정된 것과 동일한 서브넷을 갖는 NIC에 추가합니다.
    * **확인**을 클릭하여 TCP/IP 설정을 닫은 다음 **확인**을 다시 클릭하여 어댑터 설정을 닫습니다. RDP 연결이 다시 설정됩니다.
6. 명령 프롬프트에서 *ipconfig /all*을 입력합니다. 추가한 모든 IP 주소가 표시되고 DHCP는 해제됩니다.
    
### <a name="linux-ubuntu"></a>Linux(Ubuntu)

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 그렇지 않으면 다음 명령을 입력합니다.

    ```bash
    sudo -i
    ```

3. 네트워크 인터페이스(‘eth0’이라고 가정)의 구성 파일을 업데이트합니다.

    * dhcp에 대한 기존 줄 항목을 유지합니다. 기본 IP 주소가 이전에 구성된 대로 유지됩니다.
    * 다음 명령을 사용하여 추가 정적 IP 주소에 대한 구성을 추가합니다.

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    .cfg 파일이 표시됩니다.
4. vi *filename*을(를) 실행하여 파일을 엽니다.

    파일 끝에 다음 줄이 있어야 합니다.

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. 이 파일에 있는 줄 뒤에 다음 줄을 추가합니다.

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. 다음 명령을 실행하여 파일을 저장합니다.

    ```bash
    :wq
    ```

7. 다음 명령을 사용하여 네트워크 인터페이스를 다시 설정합니다.

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > 원격 연결을 사용하는 경우 같은 줄에서 ifdown 및 ifup을 둘 다 실행합니다.
    >

8. 다음 명령을 사용하여 네트워크 인터페이스에 IP 주소가 추가되는지 확인합니다.

    ```bash
    Ip addr list eth0
    ```

    목록의 일부로 추가한 IP 주소가 표시되어야 합니다.
    
### <a name="linux-redhat-centos-and-others"></a>Linux(Redhat, CentOS 및 기타)

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 그렇지 않으면 다음 명령을 입력합니다.

    ```bash
    sudo -i
    ```

3. 암호를 입력하고 화면 지시를 따릅니다. 루트 사용자인 경우 다음 명령을 사용하여 네트워크 스크립트 폴더로 이동합니다.

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 다음 명령을 사용하여 관련 ifcfg 파일을 나열합니다.

    ```bash
    ls ifcfg-*
    ```

    *ifcfg-eth0* 이 파일 중 하나로 표시되어야 합니다.

5. 다음 명령을 사용하여 *ifcfg-eth0* 파일을 복사하고 *ifcfg-eth0:0*이라고 이름을 지정합니다.

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. 다음 명령을 사용하여 *ifcfg-eth0:0* 파일을 편집합니다.

    ```bash
    vi ifcfg-eth1
    ```

7. 다음 명령을 사용하여 이 파일에서 장치 이름을 적절히 변경합니다(이 경우 *eth0:0* ).

    ```bash
    DEVICE=eth0:0
    ```

8. IP 주소를 반영하도록 *IPADDR = YourPrivateIPAddress* 줄을 변경합니다.
9. 다음 명령을 실행하여 파일을 저장합니다.

    ```bash
    :wq
    ```

10. 다음 명령을 실행하여 네트워크 서비스를 다시 시작하고 변경이 성공적으로 수행되었는지 확인합니다.

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    반환된 목록에서 추가한 IP 주소 *eth0:0*이 표시되어야 합니다.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 기존 NIC에 추가할 수 있습니다. 예제는 이 문서에서 설명된 [시나리오](#Scenario)를 기반으로 합니다.

1. Azure CLI를 열고 단일 CLI 세션 내에서 이 섹션의 나머지 단계를 완료합니다. 아직 Azure CLI를 설치 및 구성하지 않은 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md) 문서에 나오는 단계를 완료하고 Azure 계정에 로그인합니다.

2. 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)로 보내어 미리 보기를 등록합니다. 다음 경우에 나머지 단계를 완료하려 하지 마세요.
    - 미리 보기에 적용되었음을 알리는 전자 메일을 받을 때까지
    - 수신 메일의 지침을 따르기 전에


3. 요구 사항에 따라 다음 섹션 중 하나의 단계를 완료합니다.

    **개인 IP 주소 추가**
    
    NIC에 개인 IP 주소를 추가하려면 아래 명령을 사용하여 IP 구성을 만들어야 합니다.  동적 개인 IP 주소를 추가하려는 경우 명령을 입력하기 전에 ```-PrivateIpAddress 10.0.0.7```을 제거합니다. 고정 IP 주소를 지정하는 경우 서브넷에 사용되지 않는 주소이어야 합니다.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    고유한 구성 이름과 개인 IP 주소를 사용하여 필요한 만큼 구성을 만듭니다(정적 IP 주소를 가진 구성의 경우).

    **공용 IP 주소 추가**
    
    공용 IP 주소는 새 IP 구성 또는 기존 IP 구성에 연결하면 해당 주소가 추가됩니다. 필요에 따라 이후 섹션 중 하나에 나와 있는 단계를 완료합니다.

    > [!NOTE]
    > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
    >

    **새 IP 구성에 리소스 연결**
    
    새 IP 구성의 공용 IP 주소를 추가할 때마다 모든 IP 구성 시 개인 IP 주소가 있어야 하기 때문에 개인 IP 주소도 추가해야 합니다. 기존 공용 IP 주소 리소스를 추가하거나 새로 만들 수 있습니다. 새 파일을 만들려면 다음 명령을 입력합니다.
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    동적 개인 IP 주소 및 여기에 연결된 *myPublicP3* 공용 IP 주소 리소스가 있는 새 IP 구성을 만들려면 다음 명령을 입력합니다.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **기존 IP 구성에 리소스 연결**
   공용 IP 주소 리소스는 아직 연결한 리소스가 없는 IP 구성에만 연결될 수 있습니다. 다음 명령을 입력하면 IP 구성에 연결된 공용 IP 주소가 있는지 여부를 확인할 수 있습니다.

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    반환된 출력에 다음과 비슷한 줄을 찾습니다.
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    *IpConfig 3*에 대한 **공용 IP** 열이 비어 있기 때문에 현재 공용 IP 주소 리소스가 여기에 연결되어 있지 않습니다. IpConfig-3에 기존 공용 IP 주소 리소스를 추가하거나 다음 명령을 입력하여 새로 만들 수 있습니다.

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    *IPConfig 3*이라는 기존 IP 구성에 공용 IP 주소 리소스를 연결하려면 다음 명령을 입력합니다.
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. NIC에 할당된 개인 IP 주소 및 공용 IP 주소 리소스를 보려면 다음 명령을 입력합니다.

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    다음과 비슷한 결과가 나타나야 합니다. 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. 이 문서의 [VM 운영 체제에 IP 주소 추가](#OsConfig) 섹션에 나오는 지침에 따라 NIC에 추가한 IP 주소를 VM 운영 체제에 추가합니다.


<!--HONumber=Nov16_HO3-->


