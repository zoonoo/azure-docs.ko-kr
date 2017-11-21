---
title: "Azure Site Recovery를 사용하여 보조 사이트로 장애 조치(failover) 후 VM에 연결 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 보조 사이트로 장애 조치(failover)한 후 VM에 연결하기 위한 네트워킹 지침입니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2017
ms.author: pratshar
ms.openlocfilehash: ce33a90a303a10de8ff198754e8e39c334035537
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="connecting-to-vms-after-failover-to-a-secondary-site"></a>보조 사이트로 장애 조치(failover) 후 VM에 연결

배포 필수 구성 요소를 검토한 후 이 문서의 내용에 따라 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 Hyper-V VM(가상 컴퓨터)을 보조 사이트로 복제할 때의 네트워킹을 계획할 수 있습니다. 

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.

## <a name="overview"></a>개요

복제 및 장애 조치(failover) 전략을 계획할 때 고려해야 하는 중요한 요소 중 하나는 장애 조치(failover) 이후 복제본에 연결할 방법입니다. 다음의 두 가지 방법 중에서 선택할 수 있습니다. 

- **다른 IP 주소 사용**: 복제된 Azure VM에 대해 다른 IP 주소를 사용하도록 선택할 수 있습니다. 이 시나리오에서 VM은 장애 조치 후에 새 IP 주소를 갖게 되며 DNS 업데이트가 필요합니다.
- **동일한 IP 주소 유지**: 복제본 VM에 대해 동일한 IP 주소를 사용할 수 있습니다. 동일한 IP 주소를 유지하면 장애 조치 후 네트워크 관련 문제가 줄어들어 복구가 간소화됩니다. 

## <a name="retaining-ip-addresses"></a>IP 주소 유지

보조 사이트로의 장애 조치(failover) 이후 기본 사이트의 IP 주소를 유지하려는 경우 전체 서브넷 장애 조치(failover)를 수행하고 IP 주소의 새 위치를 나타내도록 경로를 업데이트하거나, 기본 사이트와 복구 사이트 간에 확대 서브넷을 배포할 수 있습니다.

### <a name="stretched-subnet"></a>늘어난 서브넷

확대 서브넷에서는 기본 사이트와 보조 사이트 둘 다에서 서브넷을 동시에 사용할 수 있습니다. 서버와 해당 IP(계층 3) 구성을 보조 사이트로 이동하는 경우 네트워크는 새 위치로 트래픽을 자동 라우팅합니다. 

계층 2(데이터 링크 계층) 측면을 고려하면 확대 VLAN을 관리할 수 있는 네트워킹 장비가 필요합니다. 또한 VLAN을 확대하면 잠재적 장애 도메인이 두 사이트로 모두 확장되므로 단일 실패 지점이 발생하게 됩니다. 이러한 현상이 발생할 가능성은 거의 없기는 하지만, 브로드캐스트 스톰이 시작해서 격리되지 못하게 될 수도 있습니다. 


### <a name="subnet-failover"></a>서브넷 장애 조치(failover)

서브넷 장애 조치(failover)를 실행하면 서브넷을 실제로 확대하지 않고도 확대 서브넷의 이점을 활용할 수 있습니다. 이 솔루션에서는 원본 또는 대상 사이트 중 하나에서 서브넷을 사용할 수 있으며 두 사이트에서 동시에 사용할 수는 없습니다. 장애 조치(failover) 시 IP 주소 공간을 유지 관리하기 위해 라우터 인프라에 대해 프로그래밍 방식으로 정렬하여 한 사이트에서 다른 사이트로 서브넷을 이동할 수 있습니다. 장애 조치(failover) 후에는 서브넷이 연결된 VM과 함께 이동됩니다. 주요 단점은 오류 발생 시 전체 서브넷을 이동해야 한다는 점입니다.

### <a name="example"></a>예

전체 서브넷 장애 조치(failover)의 예는 다음과 같습니다. 주 사이트에 서브넷 192.168.1.0/24에서 실행되는 응용 프로그램이 있습니다. 장애 조치(failover) 시 이 서브넷의 모든 VM은 보조 사이트로 장애 조치(failover)되며 IP 주소는 유지됩니다. 서브넷 192.168.1.0/24에 속하는 모든 VM(가상 컴퓨터)이 보조 사이트로 이동했음을 반영하여 경로를 수정해야 합니다.

다음 그림에는 장애 조치(failover) 전과 후의 서브넷이 나와 있습니다.

- 서브넷 192.168.0.1/24는 장애 조치(failover) 전에는 원본 사이트에서 활성화되며 장애 조치(failover) 후에는 보조 사이트에서 활성화됩니다.
- 기본 사이트와 복구 사이트, 세 번째 사이트와 기본 사이트, 그리고 세 번째 사이트와 복구 사이트 간의 경로를 적절하게 수정해야 합니다.

**장애 조치(failover) 전**

![장애 조치(failover) 전](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**장애 조치(failover) 후**

![장애 조치(failover) 후](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

장애 조치(failover) 후에는 다음 작업이 수행됩니다.

- Site Recovery가 각 VMM 인스턴스에 대해 관련 네트워크의 고정 IP 주소 풀에서 VM의 각 네트워크 인터페이스에 대한 IP 주소를 할당합니다.
- 보조 사이트의 IP 주소 풀이 원본 사이트의 풀과 같은 경우 Site Recovery는 복제본 VM에 원본 VM의 동일 IP 주소를 할당합니다. IP 주소는 VMM에서 예약되지만 Hyper-V 호스트에서 장애 조치(failover) IP 주소로 설정되지는 않습니다. Hyper-V 호스트의 장애 조치(failover) IP 주소는 장애 조치(failover) 직전에 설정됩니다.
- 동일한 IP 주소를 사용할 수 없는 경우 Site Recovery가 풀에서 사용 가능한 다른 IP 주소를 할당합니다.
- VM이 DHCP를 사용하는 경우 Site Recovery는 IP 주소를 관리하지 않습니다. 보조 사이트의 DHCP 서버가 원본 사이트와 동일한 범위에서 주소를 할당할 수 있는지 확인해야 합니다.

### <a name="validate-the-ip-address"></a>IP 주소 유효성 검사

VM을 보호하도록 설정한 후에는 다음 샘플 스크립트를 사용하여 VM에 할당된 주소를 확인할 수 있습니다. 동일한 IP 주소가 장애 조치(failover) IP 주소로 설정되고 장애 조치(failover) 시 VM에 할당됩니다.

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>IP 주소 변경

이 시나리오에서는 장애 조치(failover)되는 VM의 IP 주소가 변경됩니다. 이 솔루션의 단점은 유지 관리가 필요하다는 것입니다. 일반적으로는 복제본 VM이 시작된 후에 DNS가 업데이트됩니다. 그러므로 네트워크에서 DNS 항목을 변경하거나 클러스터로 만들어야 할 수 있으며 캐시된 항목을 업데이트해야 할 수 있습니다. 이로 인해 가동 중지 시간이 발생할 수 있습니다. 다음 방법을 통해 가동 중지 시간을 줄일 수 있습니다.

- 인트라넷 응용 프로그램의 경우 낮은 TTL 값을 사용합니다.
- 업데이트가 제때 수행되도록 Site Recovery 복구 계획에서 다음 스크립트를 사용하여 DNS 서버를 업데이트합니다. 동적 DNS 등록을 사용하는 경우에는 이 스크립트를 사용할 필요가 없습니다.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>예 

기본 사이트와 복구 사이트에서 서로 다른 IP 주소를 사용하려는 시나리오를 예로 들어 보겠습니다. 이 예에서는 기본 사이트와 보조 사이트의 IP 주소가 다릅니다. 그리고 기본 사이트 또는 복구 사이트에서 호스트되는 응용 프로그램이 액세스할 수 있는 세 번째 사이트도 있습니다.

- 앱은 장애 조치(failover) 전에는 기본 사이트의 서브넷 192.168.1.0/24에서 호스트되며, 장애 조치(failover) 후에는 서브넷 172.16.1.0/24에 포함되도록 구성됩니다.
- 세 사이트 모두 서로 액세스할 수 있도록 VPN 연결/네트워크 라우팅이 적절하게 구성되었습니다.
- 장애 조치(failover) 후에는 앱이 복구 서브넷에서 복구됩니다. 이 시나리오에서는 전체 서브넷을 장애 조치(failover)할 필요가 없으며 VPN 또는 네트워크 경로를 다시 구성하기 위해 변경해야 하는 사항이 없습니다. 장애 조치(failover) 및 일부 DNS 업데이트를 수행하면 응용 프로그램이 액세스 가능한 상태로 유지됩니다.
- DNS가 동적 업데이트를 허용하도록 구성된 경우 장애 조치(failover) 후 시작되는 VM은 새 IP 주소를 사용하여 자체 등록됩니다.

**장애 조치(failover) 전**

![다른 IP - 장애 조치 전](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**장애 조치(failover) 후**

![다른 IP - 장애 조치 후](./media/vmm-to-vmm-walkthrough-network/network-design11.png)




