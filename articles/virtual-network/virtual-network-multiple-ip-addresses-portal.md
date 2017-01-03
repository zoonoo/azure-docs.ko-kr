---
title: "가상 컴퓨터의 여러 IP 주소 - Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 716046d5e23de015da42f25f8a1f674de228efac
ms.openlocfilehash: a04192ffde57ae38d901a78a74724a89f43caedb


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>가상 컴퓨터에 여러 IP 주소 할당
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)


Azure VM(가상 컴퓨터)에는 하나 이상의 네트워크 인터페이스(NIC)가 연결되어 있을 수 있습니다. 모든 NIC에는 하나 이상의 공용 또는 개인 IP 주소가 할당되어 있을 수 있습니다. Azure의 IP 주소에 익숙하지 않은 경우 [Azure의 IP 주소](virtual-network-ip-addresses-overview-arm.md) 문서를 읽고 자세한 정보를 알아보세요. 이 문서에서는 Azure Portal을 사용하여 Azure Resource Manager 배포 모델에서 VM에 여러 IP 주소를 할당하는 방법을 설명합니다.

VM에 여러 IP 주소를 할당하면 다음을 수행할 수 있습니다.

* 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
* 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.
* NIC의 개인 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

미리 보기를 등록하려면 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 로 보냅니다.

## <a name="scenario"></a>시나리오
이 문서에서는 다음 세 가지 IP 구성을 네트워크 인터페이스에 연결합니다.
다음 예제 구성이 만들어지고, 개인 IP 주소 3개와 공용 IP 주소 1개가 할당된 NIC에 할당됩니다.

* IPConfig-1: 동적 개인 IP 주소(기본값) 및 공용 IP 주소 리소스 PIP1의 공용 IP 주소
* IPConfig-2: 정적 개인 IP 주소, 공용 IP 주소는 없음
* IPConfig-3: 동적 개인 IP 주소, 공용 IP 주소는 없음

    ![대체 이미지 텍스트](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

이 시나리오에서는 *RG1*이라는 리소스 그룹을 있다고 가정하고 내부에 *VNet1*이라는 VNet과 *Subnet1*이라는 서브넷이 있다고 가정합니다. 또한 *VM1*VM이 있고 *VM1-NIC1*네트워크 인터페이스가 연결되어 있으며 *PIP1*이라는 공용 IP 주소가 있다고 가정합니다.

[이 문서](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서는 위에 언급된 리소스를 아직 만들지 않은 경우 만드는 방법을 안내합니다.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기
Azure Preview 포털을 사용하여 위의 시나리오에 따라 여러 IP 구성을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 다음과 같이 IP 구성을 추가하려는 네트워크 인터페이스를 선택합니다. **가상 컴퓨터** > **VM1** > **네트워크 인터페이스** > **VM1-NIC1**
3. **네트워크 인터페이스** 블레이드에서 **IP 구성**을 선택합니다. 기존 IP 구성의 목록이 표시 됩니다.
4. **IP 구성** 블레이드에서 **PIP1**을 **ipconfig1**과 연결하려면 **ipconfig1**을 선택합니다. **공용 IP 주소** 아래에 있는 **ipconfig1** 블레이드에서 **Enabled**를 선택합니다.
5. **IP 주소** 탭에서 **필수 설정 구성**을 선택한 다음 **PIP1** 또는 이 기본 IP 구성과 연결하려는 공용 IP 주소를 선택하고 저장을 클릭합니다. 여기에서 연결할 새 공용 IP 주소를 만들 수도 있습니다. 그러면 네트워크 인터페이스를 저장하는 중 알림이 나타나고 이 작업이 완료되면 **ipconfig1**과 연결된 **PIP1**이 표시됩니다.

    ![대체 이미지 텍스트](media\\virtual-network-multiple-ip-addresses-portal\\01-portal.PNG)

    >[!NOTE] 
    > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

1. 그 다음 네트워크 인터페이스의 **IP 구성** 섹션 아래에서 IP 구성을 추가하려면 **+추가**를 클릭합니다.

   > [!NOTE]
   > NIC에 최대 250개의 개인 IP 주소를 할당할 수 있습니다. 구독 내에서 사용할 수 있는 공용 IP 주소 수는 제한되어 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager) 문서를 참조하세요.
   >
   >
2. **IP 구성 추가** 블레이드에서, IP 구성의 이름을 지정합니다. 여기에서는 **IPConfig-2**입니다. **할당**에서 **정적**을 선택하고 원하는 IP 주소를 입력합니다. 이 시나리오에서는 10.0.0.5입니다. 그런 후 **OK**를 클릭합니다. 구성이 저장되면 목록에 IP 구성이 표시됩니다.

    ![대체 이미지 텍스트](media\\virtual-network-multiple-ip-addresses-portal\\02-portal.PNG)
3. 다음으로, **IP 구성** 블레이드에서 **+추가**를 선택하고 아래와 같이 필요한 정보를 입력하여 세 번째 IP 구성을 추가합니다. 그런 다음 **확인**을 선택합니다.

    ![대체 이미지 텍스트](media\\virtual-network-multiple-ip-addresses-portal\\03-portal.PNG)

    IPConfig-2 및 IPConfig-3은 **IP 구성 추가** 블레이드의 **공용 IP 주소** 섹션에서 **Enabled**를 선택하여 공용 IP와 연결할 수도 있습니다. 새 공용 IP를 만들거나 이미 만든 IP를 IP 구성과 연결할 수 있습니다.
4. 아래와 같이 운영 체제의 TCP/IP 구성에 모든 개인 IP 주소(기본 포함)를 직접 추가합니다. IP 주소를 직접 추가하려면 VM에 연결하고 아래에 설명된 단계를 수행해야 합니다.

**Windows**

1. 명령 프롬프트에서 *ipconfig /all*을 입력합니다.  *기본* 개인 IP 주소(DHCP를 통한)만 표시됩니다.
2. 다음으로 명령 프롬프트 창에 *ncpa.cpl*을 입력합니다. 그러면 새 창이 열립니다.
3. **로컬 영역 연결**에 대한 속성을 엽니다.
4. IPv4(인터넷 프로토콜 버전 4)를 두 번 클릭합니다.
5. **다음 IP 주소 사용**을 선택하고 다음 값을 입력합니다.

   * **IP 주소**: *기본* 개인 IP 주소를 입력합니다.
   * **서브넷 마스크**: 서브넷을 기준으로 설정됩니다. 예를 들어 서브넷이 /24이면 서브넷 마스크는 255.255.255.0입니다.
   * **기본 게이트웨이**: 서브넷의 첫 번째 IP 주소입니다. 서브넷이 10.0.0.0/24이면 게이트웨이 IP 주소는 10.0.0.1입니다.
   * **다음 DNS 서버 주소 사용** 을 클릭하고 다음 값을 입력합니다.
     * **기본 설정 DNS 서버:** 자체 DNS 서버를 사용하지 않는 경우 168.63.129.16을 입력합니다.  이러한 서버를 사용하는 경우 DNS 서버의 IP 주소를 입력합니다.
   * **고급** 단추를 클릭하고 추가 IP 주소를 추가합니다. 8단계에 나열된 각 보조 개인 IP 주소를 기본 IP 주소에 대해 지정된 것과 동일한 서브넷을 갖는 NIC에 추가합니다.
   * **확인**을 클릭하여 TCP/IP 설정을 닫은 다음 **확인**을 다시 클릭하여 어댑터 설정을 닫습니다. 그러면 RDP 연결이 다시 설정됩니다.
6. 명령 프롬프트에서 *ipconfig /all*을 입력합니다. 추가한 모든 IP 주소가 표시되고 DHCP는 해제됩니다.

**Linux(Ubuntu)**

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 루트 사용자가 아니면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

            sudo -i
3. 네트워크 인터페이스(‘eth0’이라고 가정)의 구성 파일을 업데이트합니다.

   * dhcp에 대한 기존 줄 항목을 유지합니다. 이렇게 하면 기본 IP 주소가 이전과 같이 구성됩니다.
   * 다음 명령을 사용하여 추가 정적 IP 주소에 대한 구성을 추가합니다.

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
7. 다음 명령을 사용하여 네트워크 인터페이스를 다시 설정합니다.

           sudo ifdown eth0 && sudo ifup eth0

   > [!IMPORTANT]
   > 원격 연결을 사용하는 경우 같은 줄에서 ifdown 및 ifup을 둘 다 실행합니다.
   >
   >
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

## <a name="a-nameaddaadd-ip-addresses-to-an-existing-vm"></a><a name="add"></a>기존 VM에 IP 주소 추가
다음 단계를 완료하여 기존 NIC에 추가 IP 주소를 추가합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. **네트워크 인터페이스** 섹션에서 IP 구성을 추가하려는 네트워크 인터페이스를 선택합니다.
3. **네트워크 인터페이스** 블레이드에서 **IP 구성**을 선택합니다. 기존 IP 구성의 목록이 표시 됩니다.
4. 그 다음 네트워크 인터페이스의 IP 구성 섹션 아래에서 IP 구성을 추가하려면 **+추가**를 클릭합니다.
5. IP 구성 추가 블레이드에서, IP 구성의 이름을 지정합니다. **할당** 아래에 있는 정적 또는 동적 중에서 원하는 IP 주소 유형을 선택합니다. IP 구성을 공용 IP와 연결하려는 경우 **공용 IP 주소** 아래에서 **사용**을 선택합니다. 그렇지 않으면 **사용 안 함**을 클릭합니다. **사용**을 선택하면 기존 공용 IP를 구성에 연결할 수 있습니다. 그런 다음 확인을 클릭합니다. 구성이 저장되면 목록에 IP 구성이 표시됩니다.



<!--HONumber=Nov16_HO3-->


