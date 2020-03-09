---
title: Azure Site Recovery를 사용 하 여 보조 사이트로 NHyper 재해 복구 훈련 실행
description: Azure Site Recovery를 사용 하 여 보조 온-프레미스 데이터 센터에 VMM 클라우드의 Hyper-v Vm에 대 한 DR 드릴을 실행 하는 방법에 대해 알아봅니다.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362743"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Hyper-v Vm에 대 한 DR 드릴을 보조 사이트로 실행


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 System Center Virtual Machine Manager V (MM) 클라우드에서 관리 되는 hyper-v vm에 대해 보조 온-프레미스 사이트로의 DR (재해 복구) 훈련을 수행 하는 방법을 설명 합니다.

테스트 장애 조치 (failover)를 실행 하 여 복제 전략의 유효성을 검사 하 고 데이터 손실 또는 가동 중지 시간 없이 DR 드릴을 수행 합니다. 테스트 장애 조치 (failover)는 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않습니다. 

## <a name="how-do-test-failovers-work"></a>테스트 장애 조치 (failover)는 어떻게 작동 하나요?

기본 사이트에서 보조 사이트로 테스트 장애 조치 (failover)를 실행 합니다. 단순히 VM 장애 조치 (failover)를 확인 하려는 경우 보조 사이트에서 아무것도 설정 하지 않고 테스트 장애 조치 (failover)를 실행할 수 있습니다. 앱 장애 조치 (failover)가 예상 대로 작동 하는지 확인 하려는 경우 보조 위치에 네트워킹 및 인프라를 설정 해야 합니다.
- 단일 VM 또는 [복구 계획](site-recovery-create-recovery-plans.md)에서 테스트 장애 조치 (failover)를 실행할 수 있습니다.
- 네트워크, 기존 네트워크 또는 자동으로 만들어진 네트워크를 사용 하지 않고 테스트 장애 조치 (failover)를 실행할 수 있습니다. 이러한 옵션에 대 한 자세한 내용은 아래 표에 나와 있습니다.
    - 네트워크 없이 테스트 장애 조치 (failover)를 실행할 수 있습니다. 이 옵션은 VM이 장애 조치 (failover) 할 수 있는지 확인 하려는 경우에 유용 하지만 네트워크 구성을 확인할 수는 없습니다.
    - 기존 네트워크를 사용 하 여 장애 조치 (failover)를 실행 합니다. 프로덕션 네트워크를 사용 하지 않는 것이 좋습니다.
    - 장애 조치 (failover)를 실행 하 Site Recovery 테스트 네트워크를 자동으로 만들도록 합니다. 이 경우 Site Recovery는 네트워크를 자동으로 만들고 테스트 장애 조치 (failover)가 완료 되 면 정리 합니다.
- 테스트 장애 조치 (failover)에 대 한 복구 지점을 선택 해야 합니다. 
    - **가장 최근에 처리**됨:이 옵션은 VM을 Site Recovery에서 처리 된 최신 복구 지점으로 장애 조치 (failover) 합니다. 이 옵션은 처리 되지 않은 데이터를 처리 하는 데 시간이 걸리지 않으므로 낮은 RTO (복구 시간 목표)를 제공 합니다.
    - **최신 앱 일치**:이 옵션은 VM을 Site Recovery에서 처리 된 최신 응용 프로그램 일치 복구 지점으로 장애 조치 (failover) 합니다. 
    - **최신**:이 옵션은 먼저 Site Recovery 서비스로 보낸 모든 데이터를 처리 하 여 장애 조치 (failover) 전에 각 VM에 대 한 복구 지점을 만듭니다. 장애 조치 (failover) 후 생성 된 VM에는 장애 조치 (failover)가 트리거될 때 Site Recovery에 복제 되는 모든 데이터가 있으므로이 옵션은 가장 낮은 RPO (복구 지점 목표)를 제공 합니다.
    - **최신 다중 vm 처리**: 다중 vm 일관성을 사용 하도록 설정 된 하나 이상의 vm을 포함 하는 복구 계획에 사용할 수 있습니다. 설정이 활성화 된 Vm은 가장 최근의 일반적인 다중 VM 일치 복구 지점으로 장애 조치 (failover) 됩니다. 다른 Vm은 처리 된 최신 복구 지점으로 장애 조치 (failover) 됩니다.
    - **최신 다중 vm 앱 일치**:이 옵션은 다중 vm 일관성을 사용 하도록 설정 된 하나 이상의 vm이 포함 된 복구 계획에 사용할 수 있습니다. 복제 그룹의 일부인 Vm은 일반적인 다중 VM 응용 프로그램 일치 복구 지점으로 장애 조치 (failover) 됩니다. 다른 Vm은 최신 응용 프로그램 일치 복구 지점으로 장애 조치 (failover) 됩니다.
    - **사용자 지정**: 특정 VM을 특정 복구 지점으로 장애 조치 (failover) 하려면이 옵션을 사용 합니다.



## <a name="prepare-networking"></a>네트워킹 준비

테스트 장애 조치 (failover)를 실행 하면 표에 요약 된 것 처럼 테스트 복제 컴퓨터에 대 한 네트워크 설정을 선택 하 라는 메시지가 표시 됩니다.

| **Option** | **대해** | |
| --- | --- | --- |
| **없음을** | 복제본 VM이 있는 호스트에 테스트 VM이 만들어집니다. 클라우드에 추가 되지 않고 어떤 네트워크에도 연결 되지 않습니다.<br/><br/> 컴퓨터를 만든 후에 VM 네트워크에 연결할 수 있습니다.| |
| **기존 사용** | 복제본 VM이 있는 호스트에 테스트 VM이 만들어집니다. 클라우드에 추가 되지 않습니다.<br/><br/>프로덕션 네트워크에서 격리 된 VM 네트워크를 만듭니다.<br/><br/>VLAN 기반 네트워크를 사용 하는 경우이 목적을 위해 VMM에서 별도의 논리 네트워크 (프로덕션에 사용 되지 않음)를 만드는 것이 좋습니다. 이 논리 네트워크는 테스트 장애 조치 (failover)를 위한 VM 네트워크를 만드는 데 사용 됩니다.<br/><br/>논리 네트워크는 가상 컴퓨터를 호스트 하는 모든 Hyper-v 서버의 네트워크 어댑터 중 하나 이상에 연결 되어야 합니다.<br/><br/>VLAN 논리 네트워크의 경우 논리 네트워크에 추가 하는 네트워크 사이트를 격리 해야 합니다.<br/><br/>Windows 네트워크 가상화 기반 논리 네트워크를 사용 하는 경우 Azure Site Recovery에서 격리 된 VM 네트워크를 자동으로 만듭니다. | |
| **네트워크 만들기** | 임시 테스트 네트워크는 **논리 네트워크** 및 해당 관련 네트워크 사이트에서 지정 하는 설정에 따라 자동으로 생성 됩니다.<br/><br/> 장애 조치 (Failover)는 Vm이 만들어졌는지 확인 합니다.<br/><br/> 복구 계획에서 VM 네트워크를 두 개 이상 사용 하는 경우이 옵션을 사용 해야 합니다.<br/><br/> Windows 네트워크 가상화 네트워크를 사용 하는 경우이 옵션을 사용 하면 복제본 가상 컴퓨터의 네트워크에 동일한 설정 (서브넷 및 IP 주소 풀)을 사용 하 여 VM 네트워크를 자동으로 만들 수 있습니다. 이러한 VM 네트워크는 테스트 장애 조치 (failover)가 완료 된 후 자동으로 정리 됩니다.<br/><br/> 복제본 가상 컴퓨터가 있는 호스트에 테스트 VM이 만들어집니다. 클라우드에 추가 되지 않습니다.|

### <a name="best-practices"></a>모범 사례

- 프로덕션 네트워크를 테스트 하면 프로덕션 워크 로드에 대 한 가동 중지 시간이 발생 합니다. 재해 복구 드릴을 진행 중일 때 사용자에 게 관련 앱을 사용 하지 않도록 요청 하세요.

- 테스트 네트워크는 테스트 장애 조치 (failover)에 사용 되는 VMM 논리 네트워크 유형과 일치 하지 않아도 됩니다. 하지만 일부 조합은 작동 하지 않습니다.

     - 복제본에서 DHCP 및 VLAN 기반 격리를 사용 하는 경우 복제본에 대 한 VM 네트워크에 고정 IP 주소 풀이 필요 하지 않습니다. 따라서 사용 가능한 주소 풀이 없으므로 테스트 장애 조치 (failover)에 Windows 네트워크 가상화를 사용 하는 것은 작동 하지 않습니다. 
        
     - 복제본 네트워크가 격리 안 함을 사용 하 고 테스트 네트워크가 Windows 네트워크 가상화를 사용 하는 경우 테스트 장애 조치 (failover)가 작동 하지 않습니다. 이는 격리 되지 않은 네트워크에 Windows 네트워크 가상화 네트워크를 만드는 데 필요한 서브넷이 없기 때문입니다.
        
- 테스트 장애 조치 (failover)를 위해 네트워크 매핑에 대해 선택한 네트워크를 사용 하지 않는 것이 좋습니다.

- 장애 조치 (failover) 후 복제본 가상 머신이 매핑된 VM 네트워크에 연결 되는 방법은 VMM 콘솔에서 VM 네트워크를 구성 하는 방법에 따라 달라 집니다.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>격리 없음 또는 VLAN 격리로 구성 된 VM 네트워크

VM 네트워크가 격리 없음 또는 VLAN 격리를 사용 하 여 VMM에서 구성 된 경우 다음을 참고 하십시오.

- DHCP가 VM 네트워크에 대해 정의 된 경우 복제본 가상 머신은 연결 된 논리 네트워크에서 네트워크 사이트에 대해 지정 된 설정을 통해 VLAN ID에 연결 됩니다. 가상 머신은 사용 가능한 DHCP 서버 로부터 IP 주소를 수신 합니다.
- 대상 VM 네트워크에 대 한 고정 IP 주소 풀을 정의할 필요가 없습니다. VM 네트워크에 고정 IP 주소 풀이 사용 되는 경우 복제본 가상 머신은 연결 된 논리 네트워크에서 네트워크 사이트에 대해 지정 된 설정을 통해 VLAN ID에 연결 됩니다.
- 가상 머신은 VM 네트워크에 대해 정의 된 풀에서 IP 주소를 수신 합니다. 대상 VM 네트워크에 고정 IP 주소 풀이 정의 되어 있지 않은 경우 IP 주소 할당이 실패 합니다. 보호 및 복구에 사용할 원본 및 대상 VMM 서버 모두에 IP 주소 풀을 만듭니다.

### <a name="vm-network-with-windows-network-virtualization"></a>Windows 네트워크 가상화를 사용 하는 VM 네트워크

VM 네트워크가 Windows 네트워크 가상화를 사용 하는 VMM에서 구성 된 경우 다음을 참고 하십시오.

- 원본 VM 네트워크가 DHCP 또는 고정 IP 주소 풀을 사용 하도록 구성 되었는지 여부에 관계 없이 대상 VM 네트워크에 대 한 고정 풀을 정의 해야 합니다. 
- DHCP를 정의 하는 경우 대상 VMM 서버가 DHCP 서버 역할을 하 고 대상 VM 네트워크에 대해 정의 된 풀의 IP 주소를 제공 합니다.
- 고정 IP 주소 풀의 사용이 원본 서버에 대해 정의 된 경우 대상 VMM 서버는 풀에서 IP 주소를 할당 합니다. 두 경우 모두 고정 IP 주소 풀이 정의 되어 있지 않은 경우 IP 주소 할당이 실패 합니다.



## <a name="prepare-the-infrastructure"></a>인프라 준비

단순히 VM이 장애 조치 (failover) 할 수 있는지 확인 하려는 경우 인프라 없이 테스트 장애 조치 (failover)를 실행할 수 있습니다. 응용 프로그램 장애 조치 (failover)를 테스트 하기 위해 전체 DR 드릴을 수행 하려는 경우 보조 사이트에서 인프라를 준비 해야 합니다.

- 기존 네트워크를 사용 하 여 테스트 장애 조치 (failover)를 실행 하는 경우 해당 네트워크에서 Active Directory, DHCP 및 DNS를 준비 합니다.
- VM 네트워크를 자동으로 만드는 옵션을 사용 하 여 테스트 장애 조치 (failover)를 실행 하는 경우 테스트 장애 조치 (failover)를 실행 하기 전에 자동으로 만들어진 네트워크에 인프라 리소스를 추가 해야 합니다. 복구 계획에서 테스트 장애 조치 (failover)에 사용할 복구 계획의 그룹 1 앞에 수동 단계를 추가 하 여이를 용이 하 게 할 수 있습니다. 그런 다음 테스트 장애 조치 (failover)를 실행 하기 전에 자동으로 만들어진 네트워크에 인프라 리소스를 추가 합니다.


### <a name="prepare-dhcp"></a>DHCP 준비
테스트 장애 조치 (failover)에 포함 된 가상 컴퓨터에서 DHCP를 사용 하는 경우 테스트 장애 조치 (failover)를 위해 격리 된 네트워크 내에서 테스트 DHCP 서버를 만듭니다.


### <a name="prepare-active-directory"></a>Active Directory 준비
응용 프로그램 테스트를 위해 테스트 장애 조치 (failover)를 실행 하려면 테스트 환경에서 프로덕션 Active Directory 환경의 복사본이 필요 합니다. 자세한 내용은 [Active Directory에 대 한 테스트 장애 조치 (failover) 고려 사항](site-recovery-active-directory.md#test-failover-considerations)을 참조 하세요.

### <a name="prepare-dns"></a>DNS 준비
다음과 같이 테스트 장애 조치 (failover)를 위한 DNS 서버를 준비 합니다.

* **Dhcp**: 가상 컴퓨터에서 dhcp를 사용 하는 경우 테스트 DNS의 IP 주소를 테스트 dhcp 서버에서 업데이트 해야 합니다. 네트워크 종류의 Windows 네트워크 가상화를 사용 하는 경우 VMM 서버가 DHCP 서버 역할을 합니다. 따라서 테스트 장애 조치 (failover) 네트워크에서 DNS의 IP 주소를 업데이트 해야 합니다. 이 경우 가상 머신이 관련 DNS 서버에 등록 됩니다.
* **고정 주소**: 가상 컴퓨터에서 고정 IP 주소를 사용 하는 경우 테스트 장애 조치 (failover) 네트워크에서 테스트 DNS 서버의 IP 주소를 업데이트 해야 합니다. 테스트 가상 컴퓨터의 IP 주소를 사용 하 여 DNS를 업데이트 해야 할 수도 있습니다. 이 목적을 위해 다음 샘플 스크립트를 사용할 수 있습니다.

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>테스트 장애 조치 (failover) 실행

이 절차에서는 복구 계획에 대해 테스트 장애 조치 (failover)를 실행 하는 방법을 설명 합니다. 또는 **Virtual Machines** 탭에서 단일 가상 컴퓨터에 대 한 장애 조치 (failover)를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*를 선택 합니다. **장애 조치** > **테스트 장애 조치**(failover)를 클릭 합니다.
2. 테스트 장애 **조치** (failover) 블레이드에서 테스트 장애 조치 (failover) 후 복제본 vm이 네트워크에 연결 되는 방법을 지정 합니다.
3. **작업** 탭에서 장애 조치 (failover) 진행률을 추적 합니다.
4. 장애 조치 (failover)가 완료 된 후 Vm이 성공적으로 시작 되는지 확인 합니다.
5. 완료 되 면 복구 계획에서 **테스트 장애 조치 정리** 를 클릭 합니다. **참고**에서 테스트 장애 조치 (failover)와 관련 된 모든 관찰을 기록 하 고 저장 합니다. 이 단계에서는 테스트 장애 조치 (failover) 중에 Site Recovery에 의해 생성 된 모든 Vm 및 네트워크를 삭제 합니다. 

![테스트 장애 조치](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> 테스트 장애 조치 (failover) 중에 가상 머신에 제공 되는 IP 주소는 계획 되거나 계획 되지 않은 장애 조치 (failover)를 위해 가상 머신이 수신 하는 ip 주소와 동일 합니다 (IP 주소를 테스트 장애 조치 (failover) 네트워크에서 사용할 수 있다고 가정). 동일한 IP 주소를 테스트 장애 조치 (failover) 네트워크에서 사용할 수 없는 경우 가상 머신은 테스트 장애 조치 (failover) 네트워크에서 사용할 수 있는 다른 IP 주소를 수신 합니다.



### <a name="run-a-test-failover-to-a-production-network"></a>프로덕션 네트워크로 테스트 장애 조치 (failover) 실행

네트워크 매핑 중에 지정한 프로덕션 복구 사이트 네트워크에 대해 테스트 장애 조치 (failover)를 실행 하지 않는 것이 좋습니다. 그러나 장애 조치 (failover) 된 VM에서 종단 간 네트워크 연결의 유효성을 검사 해야 하는 경우에는 다음 사항에 유의 하세요.

* 테스트 장애 조치 (failover)를 수행할 때 기본 VM이 종료 되었는지 확인 합니다. 그렇지 않으면 동일한 id를 가진 두 가상 머신이 동시에 동일한 네트워크에서 실행 됩니다. 이 경우 원치 않는 결과가 발생할 수 있습니다.
* 테스트 장애 조치 (failover) Vm에 대 한 모든 변경 내용은 테스트 장애 조치 (failover) 가상 머신을 정리할 때 손실 됩니다. 이러한 변경 내용은 기본 Vm에 다시 복제 되지 않습니다.
* 이와 같은 테스트는 프로덕션 응용 프로그램의 가동 중지 시간을 초래 합니다. DR 드릴이 진행 중일 때 응용 프로그램 사용자에 게 응용 프로그램을 사용 하지 않도록 요청 합니다.  


## <a name="next-steps"></a>다음 단계
DR 드릴을 성공적으로 실행 한 후에 [는 전체 장애 조치 (failover)를 실행할](site-recovery-failover.md)수 있습니다.



