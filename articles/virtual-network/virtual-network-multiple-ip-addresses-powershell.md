<properties
   pageTitle="가상 컴퓨터의 여러 IP 주소 - PowerShell | Microsoft Azure"
   description="Azure PowerShell을 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />



# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>가상 컴퓨터에 여러 IP 주소 할당

Azure VM(가상 컴퓨터)에는 하나 이상의 네트워크 인터페이스(NIC)가 연결되어 있을 수 있습니다. 각 NIC에는 하나 이상의 공용 또는 개인 IP 주소가 할당되어 있을 수 있습니다. Azure의 IP 주소에 익숙하지 않은 경우 [Azure의 IP 주소](virtual-network-ip-addresses-overview-arm.md) 문서를 읽고 자세한 정보를 알아보세요. 이 문서에서는 Azure PowerShell을 사용하여 Azure Resource Manager 배포 모델에서 NIC에 여러 IP 주소를 할당하는 방법을 설명합니다.

NIC에 여러 IP 주소를 할당하면 VM에서 다음을 수행할 수 있습니다.

- 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트합니다.
- 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

미리 보기를 등록하려면 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 로 보냅니다.
## <a name="scenario"></a>시나리오

이 문서에서는 다음 세 가지 IP 구성을 네트워크 인터페이스에 연결합니다.
다음 예제 구성이 만들어지고, 개인 IP 주소 3개와 공용 IP 주소 1개가 할당된 NIC에 할당됩니다.

- IPConfig-1: 동적 개인 IP 주소(기본값) 및 공용 IP 주소 리소스 PIP1의 공용 IP 주소
- IPConfig-2: 정적 개인 IP 주소, 공용 IP 주소는 없음
- IPConfig-3: 동적 개인 IP 주소, 공용 IP 주소는 없음

    ![대체 이미지 텍스트](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

이 시나리오에서는 *RG1*이라는 리소스 그룹을 있다고 가정하고 내부에 *VNet1*이라는 VNet과 *Subnet1*이라는 서브넷이 있다고 가정합니다. 또한 *VM1*VM이 있고 *VM1-NIC1*네트워크 인터페이스가 연결되어 있으며 *PIP1*이라는 공용 IP 주소가 있다고 가정합니다.

[이 문서](./virtual-machines/virtual-machines-windows-ps-create.md )에서는 위에 언급된 리소스를 아직 만들지 않은 경우 만드는 방법을 안내합니다.

## <a name="<a-name-=-"create"></a>create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 문서의 단계를 완료합니다.

2. 다음 $Variables의 "값"을 가상 네트워크가 있는 Azure [위치](https://azure.microsoft.com/regions), [리소스 그룹](../resource-group-overview.md#resource-groups)의 이름, 리소스 그룹 내의 VNet, NIC를 연결할 서브넷 및 NIC의 이름으로 변경합니다.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    기존 Azure 위치 또는 리소스 그룹의 이름을 모르는 경우 다음 명령을 입력합니다.

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>NIC는 기존 Azure VNet(가상 네트워크) 내의 서브넷에 연결되어야 합니다. 세 가지 구성 요소인 NIC, 서브넷 및 VNet은 모두 동일한 하위 지역 및 [구독](../azure-glossary-cloud-terminology.md#subscription)에 존재해야 합니다.  VNet에 익숙하지 않은 경우 [가상 네트워크 개요](virtual-networks-overview.md) 문서를 읽어 자세한 내용을 알아보거나 [VNet 만들기](virtual-networks-create-vnet-arm-ps.md) 문서를 읽어 해당 방법을 알아봅니다.

    기존 VNet의 이름을 모르는 경우 다음 명령을 입력하고 이전 변수의 *VNet1* 을 VNet의 이름으로 바꿉니다.

        Get-AzureRmVirtualNetwork | Format-Table Name

    반환된 목록이 비어 있으면 VNet을 만들어야 합니다. 방법에 대해서는 [가상 네트워크 만들기](virtual-networks-create-vnet-arm-ps.md) 문서를 읽어보세요.

    VNet 내의 기존 서브넷 이름을 가져오려면 다음 명령을 입력하고 위의 *Subnet1* 을 서브넷의 이름으로 바꿉니다.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. 다음 명령을 입력하여 서브넷을 검색하고 변수에 할당합니다.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>NIC에 할당할 IP 구성을 정의합니다. 각 구성에는 하나의 정적 또는 동적 개인 IP 주소와 정적 또는 동적 주소에 연결된 하나의 공용 IP 주소 리소스가 있을 수 있습니다.

    NIC에 연결하려는 IP 주소 수와 구성하려는 설정에 따라 다음에 나오는 구성을 개수 제한 없이 추가하거나 제거합니다.

    **IPConfig-1**

    값 *PIP1* 을 NIC를 만드는 위치에 있으며 현재 다른 NIC와 연결되지 않은 기존 공용 IP 주소 리소스의 이름으로 변경합니다. *RG1* 을 공용 IP 주소 리소스가 있는 리소스 그룹의 이름으로 변경합니다. *IPConfig-1* 을 첫 번째 IP 구성에 지정할 이름으로 변경합니다. 다음 명령을 입력합니다.

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    *-Primary* 스위치에 유의합니다. NIC에 여러 IP 구성을 할당하는 경우 하나의 구성이 *기본*으로 할당되어야 합니다. 기존 공용 IP 주소 리소스의 이름을 모르는 경우 다음 명령을 입력합니다.

        Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration

    반환된 출력에서 **IPConfiguration** 열에 값이 없는 경우 공용 IP 주소 리소스가 기존 NIC와 연결되지 않은 것이므로 사용할 수 있습니다. 이 목록이 비어 있거나 사용 가능한 공용 IP 주소 리소스가 없는 경우 **New-AzureRmPublicIPAddress** 명령을 사용하여 만들 수 있습니다.

    >[AZURE.NOTE] 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

    **IPConfig-2**

    *IPConfig-2*를 두 번째 IP 구성에 지정하려는 이름으로 변경하고 *10.0.0.5*를 NIC를 할당하려는 서브넷의 사용하지 않는 유효한 IP 주소로 변경합니다. 다음 명령을 입력합니다.

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    서브넷에 할당된 IP 주소 범위를 모르는 경우 다음 명령을 입력합니다.

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    *IPConfig-3* 을 세 번째 IP 구성에 지정하려는 이름으로 변경하고 다음 명령을 입력합니다.

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] NIC에 최대 250개의 개인 IP 주소를 할당할 수 있습니다. 구독 내에서 사용할 수 있는 공용 IP 주소 수는 제한되어 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) 문서를 참조하세요.

6. 이전 단계에서 정의한 IP 구성을 사용하여 NIC를 만듭니다.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. [VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md) 문서의 단계에 따라 VM을 만들 때 NIC를 연결합니다. 이 문서에서는 Windows Server를 실행하는 VM을 만들지만 Linux VM의 경우도 단계는 동일하며 운영 체제만 다르게 선택하면 됩니다. 이 문서의 1-3단계를 완료합니다. 4 및 5단계는 건너뛰고 VM 만들기 문서의 6단계를 완료합니다.

    >[AZURE.WARNING] 이 문서의 6단계에서 $nic 변수를 다른 값으로 변경하거나 이 문서의 이전 단계를 완료하지 않으면 VM 만들기 문서의 6단계가 실패합니다.

8. 다음 명령을 입력하여 Azure DHCP가 NIC에 할당한 개인 IP 주소와 NIC에 할당된 공용 IP 주소 리소스를 확인합니다.

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>모든 보조 개인 IP 주소(이전 단계의 출력에 있는 *Primary* 열이 **False** 인 IP 주소)를 운영 체제의 TCP/IP 구성에 수동으로 추가합니다. 5단계에서 *IPConfig-1*에 할당된 개인 IP 주소는 *기본* 구성이기 때문에 Azure DHCP를 통해 운영 체제에 자동으로 할당됩니다.


**Windows**

1. 명령 프롬프트에서 *ipconfig /all*을 입력합니다.  *기본* 개인 IP 주소(DHCP를 통한)만 표시됩니다.
2. 다음으로 명령 프롬프트 창에 *ncpa.cpl*을 입력합니다. 그러면 새 창이 열립니다.
3. **로컬 영역 연결**에 대한 속성을 엽니다.
4. IPv4(인터넷 프로토콜 버전 4)를 두 번 클릭합니다.
5. **다음 IP 주소 사용**을 선택하고 다음 값을 입력합니다.
    - **IP 주소**: *기본* 개인 IP 주소를 입력합니다.
    - **서브넷 마스크**: 서브넷을 기준으로 설정됩니다. 예를 들어 서브넷이 /24이면 서브넷 마스크는 255.255.255.0입니다.
    - **기본 게이트웨이**: 서브넷의 첫 번째 IP 주소입니다. 서브넷이 10.0.0.0/24이면 게이트웨이 IP 주소는 10.0.0.1입니다.
    - **다음 DNS 서버 주소 사용** 을 클릭하고 다음 값을 입력합니다.
        - **기본 설정 DNS 서버:** 자체 DNS 서버를 사용하지 않는 경우 168.63.129.16을 입력합니다.  이러한 서버를 사용하는 경우 DNS 서버의 IP 주소를 입력합니다.
    - **고급** 단추를 클릭하고 추가 IP 주소를 추가합니다. 8단계에 나열된 각 보조 개인 IP 주소를 기본 IP 주소에 대해 지정된 것과 동일한 서브넷을 갖는 NIC에 추가합니다.
    - **확인**을 클릭하여 TCP/IP 설정을 닫은 다음 **확인**을 다시 클릭하여 어댑터 설정을 닫습니다. 그러면 RDP 연결이 다시 설정됩니다.

6. 명령 프롬프트에서 *ipconfig /all*을 입력합니다. 추가한 모든 IP 주소가 표시되고 DHCP는 해제됩니다.


**Linux(Ubuntu)**

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 루트 사용자가 아니면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

            sudo -i
3. 네트워크 인터페이스(‘eth0’이라고 가정)의 구성 파일을 업데이트합니다.
    - dhcp에 대한 기존 줄 항목을 유지합니다. 이렇게 하면 기본 IP 주소가 이전과 같이 구성됩니다.
    - 다음 명령을 사용하여 추가 정적 IP 주소에 대한 구성을 추가합니다.

                cd /etc/network/interfaces.d/
                ls

        .cfg 파일이 표시됩니다.
4. vi *filename*을(를) 실행하여 파일을 엽니다.

    파일 끝에 다음 줄이 있어야 합니다.

            auto eth0
            iface eth0 inet dhcp
5. 이 파일에 있는 줄 뒤에 다음 줄을 추가합니다.

            iface eth0 inet static
            address <your private IP address here>
6. 다음 명령을 실행하여 파일을 저장합니다.

            :wq
7.  다음 명령을 사용하여 네트워크 인터페이스를 다시 설정합니다.

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] 원격 연결을 사용하는 경우 같은 줄에서 ifdown 및 ifup을 둘 다 실행합니다.
8. 다음 명령을 사용하여 네트워크 인터페이스에 IP 주소가 추가되는지 확인합니다.

            ip addr list eth0

    목록의 일부로 추가한 IP 주소가 표시되어야 합니다.

**Linux(Redhat, CentOS 및 기타)**

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 루트 사용자가 아니면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

            sudo -i
3. 암호를 입력하고 화면 지시를 따릅니다. 루트 사용자인 경우 다음 명령을 사용하여 네트워크 스크립트 폴더로 이동합니다.

            cd /etc/sysconfig/network-scripts
4. 다음 명령을 사용하여 관련 ifcfg 파일을 나열합니다.

            ls ifcfg-*

    *ifcfg-eth0* 이 파일 중 하나로 표시되어야 합니다.
5. 다음 명령을 사용하여 *ifcfg-eth0* 파일을 복사하고 *ifcfg-eth0:0*이라고 이름을 지정합니다.

            cp ifcfg-eth0 ifcfg-eth0:0
6. 다음 명령을 사용하여 *ifcfg-eth0:0* 파일을 편집합니다.

            vi ifcfg-eth1
7. 다음 명령을 사용하여 이 파일에서 장치 이름을 적절히 변경합니다(이 경우 *eth0:0* ).

            DEVICE=eth0:0
8. IP 주소를 반영하도록 *IPADDR = YourPrivateIPAddress* 줄을 변경합니다.
9. 다음 명령을 실행하여 파일을 저장합니다.

            :wq
10. 다음 명령을 실행하여 네트워크 서비스를 다시 시작하고 변경이 성공적으로 수행되었는지 확인합니다.

            /etc/init.d/network restart
            Ipconfig

    반환된 목록에서 추가한 IP 주소 *eth0:0*이 표시되어야 합니다.

## <a name="<a-name="add"></a>add-ip-addresses-to-an-existing-vm"></a><a name="add"></a>기존 VM에 IP 주소 추가

다음 단계를 완료하여 기존 NIC에 추가 IP 주소를 추가합니다.

1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 문서의 단계를 완료합니다.

2. 다음 $Variables의 "값"을 IP 주소를 추가하려는 NIC의 이름과 NIC가 있는 리소스 그룹 및 위치로 변경합니다.

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    변경하려는 NIC의 이름을 잘 모르는 경우 다음 명령을 입력한 다음 이전 변수의 값을 변경합니다.

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. 다음 명령을 입력하여 변수를 만들고 기존 NIC로 설정합니다.

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. 이 문서에 포함된 여러 IP 주소로 VM 만들기 섹션의 [3단계](#subnet) 를 완료하여 NIC가 연결된 서브넷 ID를 검색합니다.

5. 이 문서에 포함된 여러 IP 주소로 VM 만들기 섹션의 [5단계](#ipconfigs) 에 나오는 지침에 따라 네트워크에 추가하려는 IP 구성을 만듭니다.

6. *$IPConfigName4* 를 이전 단계에서 만든 IP 구성의 이름으로 변경합니다. 구성을 추가하려면 다음 명령을 입력합니다.

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. IP 구성을 사용하여 NIC를 설정하려면 다음 명령을 입력합니다.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. 이 문서에 포함된 여러 IP 주소로 VM 만들기 섹션의 [9단계](#os) 에 나오는 지침에 따라 NIC에 추가한 IP 주소를 VM 운영 체제에 추가합니다.



<!--HONumber=Oct16_HO2-->


