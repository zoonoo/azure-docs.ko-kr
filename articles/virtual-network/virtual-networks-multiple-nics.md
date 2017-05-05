---
title: "PowerShell을 사용하여 다중 NIC이 있는 VM(클래식) 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 다중 NIC가 있는 VM을 만들고 구성하는 방법 알아보기."
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a1a3952c-2dcc-4977-bd7a-52d623c1fb07
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5b26aecc7b9797d7bf604ea5e3d8bdece5bea17b
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-vm-classic-with-multiple-nics"></a>다중 NIC이 있는 VM(클래식) 만들기
Azure에서 VM(가상 컴퓨터)을 만들고 각 VM에 여러 NIC(네트워크 인터페이스)를 연결할 수 있습니다. 다중 NIC는 응용 프로그램 전달 및 WAN 최적화 솔루션과 같은 여러 네트워크 가상 장비를 위한 요구 사항입니다. 또한 다중 NIC는 NIC 간의 트래픽 격리를 제공합니다.

![VM에 대한 다중 NIC](./media/virtual-networks-multiple-nics/IC757773.png)

그림에서는 각각 다른 서브넷에 연결된 3개의 NIC를 사용하여 VM을 보여줍니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자를 사용하는 것이 좋습니다.

* 인터넷 연결 VIP(클래식 배포)는 "기본" NIC에서만 지원됩니다. 기본 NIC의 IP에 하나의 VIP만 있습니다.
* 현재 LPIP(인스턴스 수준 공용 IP) 주소(클래식 배포)는 다중 NIC VM에 대해 지원되지 않습니다.
* VM 내에서 Nic의 순서가 임의로 지정되며, Azure 인프라 업데이트에서 변경할 수도 있습니다. 그러나 IP 주소 및 해당 이더넷 MAC 주소는 동일하게 유지됩니다. 예를 들어, **Eth1**의 IP 주소는 10.1.0.100이며 MAC 주소는 00-0D-3A-B0-39-0D입니다. Azure 인프라 업데이트 및 다시 부팅 후, **Eth2**로 변경될 수 있지만 IP 및 MAC 페어링은 그대로 유지됩니다. 다시 시작은 고객이 시작한 것이며 NIC 순서는 그대로 유지됩니다.
* 각 VM에서 각 NIC에 대한 주소는 서브넷에 있어야 하며, 단일 VM의 여러 NIC는 동일한 서브넷에 있는 주소에 각각 할당됩니다.
* VM 크기는 VM에 대해 만들 수 있는 NIC의 수를 결정합니다. 각 VM 크기가 지원하는 NICS 수를 확인하려면 [Windows Server](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM 크기 문서를 참조하세요. 

## <a name="network-security-groups-nsgs"></a>NSG(네트워크 보안 그룹)
리소스 관리자 배포에서는 다중 NIC를 사용할 수 있는 VM의 NIC를 포함하여 VM의 모든 NIC를 NSG(네트워크 보안 그룹)에 연결할 수 있습니다. NIC가 서브넷이 NSG와 연관된 서브넷 내에서 할당되면, 서브넷의 NSG의 규칙도 해당 NIC에 적용됩니다. 서브넷을 NSG와 연결하는 것 외에도 NSG와 NIC를 연결할 수도 있습니다.

서브넷이 해당 서브넷이 NSG와 개별적으로 연관된 NIC 및 NSG와 연결된 경우, NIC 안팎으로 전달되는 트래픽의 방향에 따라 연관된 NSG 규칙은 **흐름 순서** 에 적용됩니다.

* **들어오는 트래픽** 대상은 논의 중인 서브넷을 통한 NIC 흐름으로, 서브넷의 NSG 규칙을 트리거하며 NIC로 전달되기 전에 NIC의 NSG 규칙을 트리거합니다.
* **들어오는 트래픽** 소스는 처음 NIC를 통한 논의 중인 NIC 흐름으로, NIC의 NSG 규칙을 트리거하며 서브넷으로 전달되기 전에 서브넷의 NSG 규칙을 트리거합니다.

[네트워크 보안 그룹](virtual-networks-nsg.md) 과 서브넷, VM 및 NIC 연결을 기반으로 이를 적용하는 방법에 대해 자세히 알아보세요.

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>클래식 배포에서 다중 NIC VM을 구성하는 방법
아래의 지침에 따라 기본 NIC 하나와 추가 NIC 두 개 등 3개의 NIC가 포함된 다중 NIC VM을 만들 수 있습니다. 구성 단계는 아래 서비스 구성 파일 조각에 따라 구성될 VM을 만듭니다.

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


예제의 PowerShell 명령 실행을 시도하기 전에 다음 필수 조건에 부합해야 합니다.

* Azure 구독.
* 구성된 가상 네트워크입니다. 자세한 내용은 [가상 네트워크 개요(영문)](virtual-networks-overview.md) 를 참조하세요.
* 최신 버전의 Azure PowerShell이 다운로드되어 설치됩니다. [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

다중 NIC이 있는 VM을 만들려면 단일 PowerShell 세션 내에 각 명령을 입력하여 다음 단계를 완료합니다.

1. Azure VM 이미지 갤러리에서 VM 이미지를 선택합니다. 이미지를 자주 변경하고 지역에 따라 사용할 수 있습니다. 아래 예제에서 지정된 이미지는 변경되거나 사용자 지역에 없을 수 있으므로, 필요한 이미지를 지정해야 합니다.

    ```powershell
    $image = Get-AzureVMImage `
    -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
    ```

2. VM 구성을 만듭니다.

    ```powershell
    $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
    -Image $image.ImageName –AvailabilitySetName "MyAVSet"
    ```

3. 기본 관리자 로그인을 만듭니다.

    ```powershell
    Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
    -Password "<YourAdminPassword>"
    ```

4. VM 구성에 추가 Nic를 추가합니다.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
    -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
    Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
    -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
    ```

5. 기본 NIC에 대한 서브넷 및 IP 주소를 지정합니다.

    ```powershell
    Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
    Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
    ```

6. 가상 네트워크에 VM 만들기

    ```powershell
    New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm
    ```

    > [!NOTE]
    > 여기서 지정하는 VNet(전제 조건에서 설명한 대로)은 이미 존재해야 합니다. 다음 예제에서는 **MultiNIC-VNet**으로 명명된 가상 네트워크를 지정합니다.
    >

## <a name="limitations"></a>제한 사항
다중 NIC을 사용하는 경우 다음과 같은 제한이 적용될 수 있습니다.

* 다중 NIC가 있는 VM은 Azure VNet(가상 네트워크)에서 생성되어야 합니다. VNet이 아닌 VM은 다중 NIC로 구성할 수 없습니다.
* 가용성 집합의 모든 VM은 다중 NIC 또는 단일 NIC를 사용해야 합니다. 가용성 집합 안에 다중 NIC VM과 단일 NIC VM이 혼합될 수 없습니다. 동일한 규칙이 클라우드 서비스의 VM에도 적용됩니다. 다중 NIC VM의 경우 적어도 각기 두 개 이상 있으면 동일한 NIC 번호를 가질 필요가 없습니다.
* 단일 NIC가 있는 VM은 일단 배포되면 삭제한 후 다시 만들지 않고 다중 NIC로 구성할 수 없습니다(반대의 경우도 마찬가지임).

## <a name="secondary-nics-access-to-other-subnets"></a>다른 서브넷에 대한 보조 NIC 액세스
기본적으로 보조 NIC의 트래픽 흐름은 동일한 서브넷 내로 제한되므로 보조 NIC가 기본 게이트웨이로 구성되지 않습니다. 보조 NIC가 자체 서브넷 외부와 통신할 수 있도록 설정하려는 경우, 아래 설명된 게이트웨이를 구성하도록 라우팅 테이블에 항목을 추가해야 합니다.

> [!NOTE]
> 2015년 7월 이전에 만든 VM에는 모든 NIC에 대한 기본 게이트웨이가 구성되어 있을 수 있습니다. 보조 NIC에 대한 기본 게이트웨이는 이러한 VM을 다시 부팅해야만 제거됩니다. Linux와 같은 취약한 호스트 라우팅 모델을 사용하는 운영 체제에서는 송/수신 트래픽이 다른 NIC를 사용하는 경우 인터넷 연결이 중단됩니다.
> 

### <a name="configure-windows-vms"></a>Windows VM 구성
다음과 같은 두 개의 NIC가 있는 Windows VM이 있다고 가정합니다.

* 주 NIC IP 주소: 192.168.1.4
* 보조 NIC IP 주소: 192.168.2.5

이 VM에 대한 IPv4 경로 테이블은 다음과 같습니다.

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

기본 경로(0.0.0.0)는 주 NIC에서만 사용할 수 있음에 주의합니다. 다음과 같이 보조 NIC의 서브넷 외부 리소스에 액세스할 수 없습니다.

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

보조 NIC에 기본 경로 추가하려면 다음 단계를 수행합니다.

1. 명령 프롬프트에서 아래 명령을 실행하여 보조 NIC에 대한 인덱스 번호를 식별합니다.
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. 테이블에서 27 인덱스(이 예에서)가 있는 두 번째 항목에 주의합니다.
3. 명령 프롬프트에서 다음과 같이 **route add** 명령을 실행합니다. 이 예제에서는 192.168.2.1을 보조 NIC에 대한 기본 게이트웨이로 지정합니다.
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. 연결을 테스트하려면 명령 프롬프트로 돌아가서 다음 예와 같이 보조 NIC에서 다른 서브넷을 ping합니다.
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. 또한 다음과 같이 경로 테이블을 확인하여 새로 추가된 경로를 확인할 수 있습니다.
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Linux VM 구성
Linux VM의 경우, 기본 동작에서 취약한 호스트 라우팅을 사용하므로 보조 NIC는 동일한 서브넷 내 트래픽 흐름으로만 제한하는 것이 좋습니다. 그러나 특정 시나리오에 서브넷 외부 연결을 요청하는 경우, 사용자는 정책 기반 라우팅을 사용하도록 설정하여 송/수신 트래픽이 동일한 NIC를 사용하게 해야 합니다.

## <a name="next-steps"></a>다음 단계
* [리소스 관리자 배포를 통해 2계층 응용 프로그램 시나리오에서 MultiNIC VM](virtual-network-deploy-multinic-arm-template.md)배포
* [클래식 배포를 통해 2계층 응용 프로그램 시나리오에서 MultiNIC VM](virtual-network-deploy-multinic-classic-ps.md)배포


