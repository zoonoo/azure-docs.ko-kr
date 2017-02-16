---
title: "Site Recovery(Azure)에서 테스트 장애 조치(failover)(VMM에서 VMM으로) | Microsoft Docs"
description: "Azure Site Recovery는 가상 컴퓨터 및 실제 서버의 복제, 장애 조치 및 복구를 조정합니다. Azure로 또는 보조 데이터 센터로 장애 조치에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/09/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 1c3a3a3e9381e20382bb537299b04993180ee729
ms.openlocfilehash: 465da7ae1aa3a31400706c28d0516507ddf66ff0


---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Site Recovery에서 테스트 장애 조치(failover)(VMM에서 VMM으로)

이 문서는 VMM으로 관리되는 온-프레미스 사이트를 복구 사이트로 사용하여 Site Recovery로 보호되는 가상 컴퓨터 및 물리적 서버의 테스트 장애 조치(failover) 또는 재해 복구 훈련을 수행하기 위한 정보와 지침을 제공합니다. 

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

데이터 손실 또는 가동 중지 없이 복제 전략의 유효성을 검사하거나 재해 복구 훈련을 수행하기 위해 테스트 장애 조치(failover)가 실행됩니다. 테스트 장애 조치(failover)를 수행해도 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않습니다. 가상 컴퓨터 또는 [복구 계획](site-recovery-create-recovery-plans.md)에서 테스트 장애 조치(failover)를 수행할 수 있습니다. 테스트 장애 조치(failover)를 트리거할 때 테스트 가상 컴퓨터를 연결할 네트워크를 지정해야 합니다. 테스트 장애 조치(failover)가 트리거되면 **작업** 페이지에서 진행률을 추적할 수 있습니다.  


## <a name="preparing-infrastructure-for-test-failover"></a>테스트 장애 조치(failover)를 위한 인프라 준비
* 기존 네트워크를 사용하여 테스트 장애 조치를 실행하려는 경우 해당 네트워크에서 Active Directory, DHCP 및 DNS를 준비합니다.
* VM 네트워크를 자동으로 생성하는 옵션을 사용하여 테스트 장애 조치를 실행하고자 한다면 복구 계획 내 그룹&1; 앞에 수동 단계를 추가하고 테스트 장애 조치를 실행하기 전에 자동으로 생성된 네트워크에 인프라 리소스를 추가합니다.

### <a name="things-to-note"></a>주의할 사항
* 보조 사이트에 복제 시, 복제 컴퓨터에서 사용되는 네트워크 유형이 테스트 장애 조치에 사용되는 논리 네트워크 유형과 일치해야 하는 것은 아니지만 일부 조합이 작동하지 않을 수 있습니다. 복제본에서 DHCP 및 VLAN 기반 격리를 사용하는 경우 복제본에 대한 VM 네트워크는 고정 IP 주소 풀이 필요하지 않습니다. 따라서 사용 가능한 주소 풀이 없으므로 테스트 장애 조치에 대한 Windows 네트워크 가상화의 사용이 작동하지 않습니다. 또한 복제본 네트워크가 격리 없음이고 테스트 네트워크가 Windows 네트워크 가상화인 경우 테스트 장애 조치가 작동하지 않습니다. 이것은 격리 없는 네트워크에 Windows 네트워크 가상화 네트워크를 만드는 데 필요한 서브넷이 없기 때문입니다.
* 장애 조치 후에 복제본 가상 컴퓨터가 매핑된 VM 네트워크에 연결된 방식은 VM네트워크가 VMM 콘솔에서 구성된 방식에 따라 다릅니다.
  * **격리 없음 또는 VLAN 격리로 구성된 VM 네트워크**—VM 네트워크에 대해 DHCP가 지정되어 있다면 복제본 가상 컴퓨터가 관련된 논리 네트워크 내 네트워크 사이트에 대해 지정된 설정을 사용하여 VLAN ID에 연결됩니다. 가상 컴퓨터는 사용 가능한 DHCP 서버로부터 IP 주소를 수신합니다. 대상 VM 네트워크에 대해 정의된 고정 IP 주소 풀이 필요 없습니다. VM 네트워크에 대해 고정 IP 주소 풀이 사용되는 경우 복제본 가상 컴퓨터가 관련된 논리 네트워크 내 네트워크 사이트에 대해 지정된 설정을 사용하여 VLAN ID에 연결됩니다. 가상 컴퓨터는 VM 네트워크에 대해 지정된 풀로부터 IP 주소를 수신합니다. 고정 IP 주소 풀이 대상 VM 네트워크에 정의되어 있다면 IP 주소 할당이 실패합니다. 보호 및 복구에 사용하려는 원본과 대상 VMM 서버 모두에 IP 주소 풀을 만들어야 합니다.
  * **Windows 네트워크 가상화를 사용한 VM 네트워크**—VM 네트워크가 이 설정으로 구성된 경우 원본 VM 네트워크가 DHCP 또는 고정 IP 주소 풀을 사용하도록 구성되었는지 여부에 상관 없이 고정 풀이 대상 VM 네트워크에 대해 정의되어야 합니다. DHCP를 정의하는 경우 대상 VMM 서버가 DHCP 서버 역할을 하며, 대상 VM 네트워크에 대해 정의된 풀로부터 IP 주소를 제공합니다. 고정 IP 주소 풀의 사용이 원본 서버에 대해 정의 되어 있다면 대상 VMM 서버가 풀로부터 IP 주소를 할당합니다. 두 경우 모두, 고정 IP 주소 풀이 정의되어 있지 않으면 IP 주소 할당이 실패합니다.


### <a name="prepare-dhcp"></a>DHCP 준비
테스트 장애 조치에 사용되는 가상 컴퓨터가 DHCP를 사용하는 경우 테스트 장애 조치의 목적으로 생성된 격리된 네트워크에 테스트 DHCP 서버를 만들어야 합니다.

### <a name="prepare-active-directory"></a>Active Directory 준비
응용프로그램 테스트를 위해 테스트 장애 조치를 실행하려면 테스트 환경에서 프로덕션 Active Directory 환경의 복사본이 필요합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations) 섹션을 살펴보세요. 

### <a name="prepare-dns"></a>DNS 준비
다음과 같이 테스트 장애 조치에 대한 DNS 서버를 준비합니다.

* **DHCP**—가상 컴퓨터에서 DHCP를 사용하는 경우 테스트 DNS의 IP 주소를 테스트 DHCP 서버에서 업데이트해야 합니다. 네트워크 유형의 Windows 네트워크 가상화를 사용하는 경우 VMM 서버가 DHCP 서버로 작동합니다. 따라서 테스트 장애 조치(failover) 네트워크에서 DNS의 IP 주소가 업데이트되어야 합니다. 이 경우 가상 컴퓨터가 관련 DNS 서버에 자체 등록됩니다.
* **고정 주소**—가상 컴퓨터가 고정 IP 주소를 사용하는 경우 테스트 장애 조치(failover)에서 테스트 DNS 서버의 IP 주소를 업데이트해야 합니다. 테스트 가상 컴퓨터의 IP 주소로 DNS를 업데이트해야 할 수 있습니다. 이러한 용도로 다음 예제 스크립트를 사용할 수 있습니다.

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행
이 절차는 복구 계획에 대한 테스트 장애 조치를 실행하는 방법을 설명합니다. 또는 **가상 컴퓨터** 탭에서 단일 컴퓨터 또는 실제 서버에 대한 장애 조치를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다. **장애 조치(Failover)** > **Test 장애 조치(Failover)**에서 의견이나 질문을 게시합니다.
1. **테스트 장애 조치** 블레이드에서 테스트 장애 조치(failover) 후에 가상 컴퓨터를 네트워크에 연결할 방법을 지정합니다. 자세한 내용은 [네트워크 옵션](#network-options-in-site-recovery)을 참조하세요.
1. **작업** 탭에서 장애 조치 진행 상황을 추적합니다. 
1. 완료되면 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
1. 작업을 완료하면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고** 에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다. 그러면 테스트 장애 조치(failover) 중에 생성된 가상 컴퓨터 및 네트워크가 삭제됩니다. 


## <a name="network-options-in-site-recovery"></a>사이트 복구의 네트워크 옵션

테스트 장애 조치를 실행하면 테스트 복제 컴퓨터에 대한 네트워크 설정을 선택하라는 메시지가 나타납니다. 다양한 옵션이 있습니다.  

| **테스트 장애 조치 옵션** | **설명** | **장애 조치 검사** | **세부 정보** |
| --- | --- | --- | --- |
| **보조 VMM 사이트로 장애 조치 — 네트워크 없이** |VM 네트워크를 선택 하지 마십시오. |장애 조치(failover)에서 테스트 컴퓨터가 생성되었는지 확인<br/><br/>복제 가상 컴퓨터가 존재하는 호스트와 동일한 호스트에 테스트 가상 컴퓨터가 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다. |<p>장애 조치된 컴퓨터는 네트워크에 연결되지 않습니다.<br/><br/>컴퓨터는 생성된 후에 VM 네트워크에 연결될 수 있습니다. |
| **보조 VMM 사이트로 장애 조치—네트워크 사용** |기존 VM 네트워크 선택 |장애 조치가 가상 컴퓨터 만들어졌는지 확인합니다. |복제 가상 컴퓨터가 존재하는 호스트와 동일한 호스트에 테스트 가상 컴퓨터가 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다.<br/><br/>프로덕션 네트워크에서 격리된 VM 네트워크 만들기<br/><br/>권장되는 VLAN 기반 네트워크를 사용하는 경우 이 목적을 위해 VMM에서 별도의 논리 네트워크(프로덕션에 사용되지 않음)를 만드십시오. 이 논리 네트워크는 테스트 장애 조치(failover)의 목적으로 VM 네트워크를 만드는 데 사용됩니다.<br/><br/>논리 네트워크는 가상 컴퓨터를 호스트하는 모든 Hyper-V 서버에 있는 하나 이상의 네트워크 어댑터에 연결해야 합니다.<br/><br/>VLAN 논리 네트워크의 경우 논리 네트워크에 추가한 네트워크 사이트는 격리되어야 합니다.<br/><br/>Windows 네트워크 가상화 기반의 논리 네트워크를 사용한다면 Azure Site Recovery가 격리된 VM 네트워크를 자동으로 생성합니다. |
| **보조 VMM 사이트로 장애 조치—네트워크 만들기** |임시 테스트 네트워크가 **논리 네트워크** 에서 지정한 설정 및 관련 네트워크 사이트를 기반으로 자동 생성됩니다. |장애 조치가 가상 컴퓨터 만들어졌는지 확인합니다. |복구 계획에서 VM 네트워크를 두 개 이상 사용할 경우 이 옵션을 사용합니다. Windows 네트워크 가상화 네트워크를 사용하는 경우 이 옵션을 사용하여 복제 가상 컴퓨터의 네트워크에서 동일한 설정(서브넷 및 IP 주소 풀)으로 VM 네트워크를 자동으로 만들 수 있습니다. 테스트 장애 조치 완료 후 이러한 VM 네트워크는 자동으로 정리됩니다.</p><p>복제 가상 컴퓨터가 존재하는 호스트와 동일한 호스트에 테스트 가상 컴퓨터가 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다. |

> [!TIP]
> 테스트 장애 조치(failover) 중에 가상 컴퓨터에 제공된 IP 주소는 이 IP 주소가 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우, 계획되거나 계획되지 않은 장애 조치(failover)를 수행할 때 얻게 되는 IP 주소와 동일합니다. 동일한 IP 주소를 테스트 장애 조치(failover) 네트워크에서 사용할 수 없는 경우 가상 컴퓨터는 테스트 장애 조치(failover) 네트워크에서 사용 가능한 다른 IP 주소를 받게 됩니다.
>
>


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>복구 사이트의 프로덕션 네트워크로 테스트 장애 조치(failover) 
테스트 장애 조치(Failover)를 수행할 때 **네트워크 매핑**에서 입력한 프로덕션 복구 사이트 네트워크와 다른 네트워크를 선택하는 것이 좋습니다. 그러나 장애 조치(failover)된 가상 컴퓨터에서 종단 간 네트워크 연결의 유효성을 검사하려는 경우 다음 사항에 유의하세요.

1. 테스트 장애 조치(failover)를 수행할 때 기본 가상 컴퓨터를 종료합니다. 기본 가상 컴퓨터를 종료하지 않으면 ID가 같은 두 가상 컴퓨터가 동시에 같은 네트워크에서 실행되므로 원치 않는 결과가 발생할 수 있습니다. 
1. 테스트 장애 조치(failover) 가상 컴퓨터에 수행하는 모든 변경 내용은 테스트 장애 조치(failover) 가상 컴퓨터를 정리할 때 손실됩니다. 이러한 변경 내용은 기본 가상 컴퓨터에 다시 복제되지 않습니다.
1. 이러한 방법으로 테스트를 수행하면 프로덕션 응용 프로그램이 가동 중지되는 결과를 가져옵니다. 재해 복구 훈련이 진행 중인 동안에는 응용 프로그램을 사용하지 말라고 응용 프로그램 사용자에게 알려야 합니다.  


## <a name="next-steps"></a>다음 단계
테스트 장애 조치(failover)를 성공적으로 수행한 후에는 [장애 조치](site-recovery-failover.md)(failover)를 수행해도 됩니다.



<!--HONumber=Jan17_HO4-->


