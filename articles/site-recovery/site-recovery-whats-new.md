---
title: Azure 사이트 복구의 새로운 내용
description: Azure 사이트 복구 서비스의 새 기능 및 최신 업데이트에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257434"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery의 새로운 기능

[Azure Site Recovery](site-recovery-overview.md) 서비스는 지속적으로 업데이트되고 개선됩니다. 서비스를 최신 상태로 유지할 수 있도록 이 문서에서는 최신 릴리스, 새 기능 및 새 콘텐츠에 대한 정보를 제공합니다. 이 페이지는 정기적으로 업데이트됩니다.

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 채널에서 사이트 복구 업데이트 알림을 팔로우하고 구독할 수 있습니다.

## <a name="supported-updates"></a>지원되는 업데이트

사이트 복구 구성 요소의 경우 N이 최신 릴리스 버전인 N-4 버전을 지원합니다. 각각은 다음 표에 요약되어 있습니다.

**업데이트** |  **통합 설치** | **구성 서버 오바** | **모빌리티 서비스 에이전트** | **Site Recovery Provider** | **Recovery Services 에이전트**
--- | --- | --- | --- | --- | ---
[롤업 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[롤업 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[롤업 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[롤업 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[롤업 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

업데이트 설치 및 지원에 대해 [자세히 알아보세요.](service-updates-how-to.md)

> [!NOTE]
> 업데이트 롤업 44는 사이트 복구 공급자 및 에이전트에 대한 업데이트를 포함하지 않았기 때문에 테이블에 표시되지 않습니다.

## <a name="updates-march-2020"></a>업데이트 (2020년 3월)

### <a name="update-rollup-45"></a>롤업 업데이트 45

[업데이트 롤업 45는](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) 다음과 같은 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 롤업에 자세히 설명된 대로 여러 가지 수정 및 개선 사항입니다.

## <a name="updates-january-2020"></a>업데이트 (2020년 1월)

### <a name="update-rollup-44"></a>롤업 업데이트 44

[업데이트 롤업 44는](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 공급자 및 에이전트에 대한 업데이트가 없습니다.
**문제 수정/개선 사항** | 롤업에 자세히 설명된 대로 여러 가지 수정 및 개선 사항입니다.

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware 재해 복구

이제 Azure 가상 머신은 고객 관리 키를 사용하여 미사용 암호화를 사용하도록 지원하는 VM을 지원합니다. [자세히 알아봅니다](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>롤업 업데이트 43

[업데이트 롤업 43은](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)


## <a name="updates-november-2019"></a>업데이트(2019년 11월)

### <a name="update-rollup-42"></a>롤업 업데이트 42

[업데이트 롤업 42는](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)


### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대한 새로운 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**UEFI** | 이제 사이트 복구는 UEFI 기반 부팅 아키텍처를 통해 Azure VM에 대한 재해 복구를 지원합니다.
**Linux** | 사이트 복구는 이제 Azure 디스크 암호화(ADE)를 통해 Linux를 실행하는 Azure VM을 지원합니다.
**2세대** | 이제 모든 세대 2 Azure VM이 재해 복구에 지원됩니다.
**지역** | 이제 노르웨이 지역의 Azure VM에 대해 재해 복구를 활성화할 수 있습니다.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware에서 Azure로 재해 복구

VMware에서 Azure 재해 복구에 대한 새로운 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**UEFI** | 사이트 복구는 이제 UEFI 기반 부팅 아키텍처를 통해 VMware VM에 대한 재해 복구를 지원합니다.<br/><br/> 지원되는 운영 체제에는 Windows Server 2019, Windows 서버 2016, Windows 서버 2012 R2, Windows 서버 2012, SLES 12 SP4, RHEL 8이 포함됩니다.

## <a name="update-to-servicing-stack-updatesha-2"></a>서비스 스택 업데이트/SHA-2로 업데이트

Azure VM을 보조 지역으로 복구하거나 온-프레미스 VM 또는 Azure에 대한 물리적 서버를 복구하려면 다음을 기록하십시오.

- 모빌리티 서비스 확장(Azure VM) 및 모빌리티 서비스 에이전트(VMware/물리적 컴퓨터의 경우)의 버전 9.30.5407.1에서 일부 컴퓨터 운영 체제는 서비스 스택 업데이트 및 SHA-2를 실행해야 합니다. 자세한 내용은 아래 표에 나와 있습니다.
- 링크된 KB에 따라 업데이트 및 SHA-2를 설치한다. SHA-1은 2019년 9월부터 지원되지 않으며 SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다.
- [SHA-2 업그레이드 및 요구 사항에](https://aka.ms/SHA-2KB)대해 자세히 알아보십시오.

**운영 체제** | **Azure VM** | **VM웨어 VM/물리적 컴퓨터**
--- | --- | ---
**윈도우 2008 R2 SP1** | [스택 업데이트 서비스](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [스택 업데이트 서비스](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**윈도우 2008 SP2** | [스택 업데이트 서비스](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [스택 업데이트 서비스](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**윈도우 7 SP1** | [스택 업데이트 서비스](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [스택 업데이트 서비스](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>업데이트 (2019년 10월)

### <a name="update-rollup-41"></a>롤업 업데이트 41

[업데이트 롤업 41은](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)



### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대한 새로운 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**장애 조치 설정 테스트** | 테스트 장애 조치(FAILOVER)를 설정할 때 이제 각 컴퓨터 NIC에 대한 IP 주소, NSG, 내부 부하 분산 및 공용 IP 주소를 포함하여 테스트 장애 조치 VM 및 네트워크에 대한 설정을 구성할 수 있습니다. 이러한 설정은 선택 사항이며 현재 동작은 변경되지 않습니다. 이러한 설정을 구성하지 않은 경우 테스트 장애 조치 시점에 Azure VNet을 선택할 수 있습니다. [자세히 알아봅니다](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**복구 계획** | 이제 복구 계획은 장애 조치 안정성을 보장하기 위해 100VM으로 제한됩니다.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware에서 Azure로 재해 복구

VMware에서 Azure 재해 복구에 대한 새로운 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**복구 계획** | 이제 복구 계획은 장애 조치 안정성을 보장하기 위해 100VM으로 제한됩니다.


## <a name="updates-september-2019"></a>업데이트 (2019년 9월)

### <a name="update-rollup-40"></a>롤업 업데이트 40

[업데이트 롤업 40은](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)




### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대한 새로운 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**장애 조치 후 정리** | 보조 Azure로 장애 복구한 다음 기본 지역으로 다시 장애 복구한 후 사이트 복구는 보조 지역의 컴퓨터를 자동으로 정리합니다. VM 및 NIC를 수동으로 삭제할 필요가 없습니다.
**테스트 장애 조치로 IP 주소 유지** | 이제 재해 복구 드릴 동안 원본 VM의 IP 주소를 유지하고 테스트 장애 조치(failover)에 대한 정적 IP 주소를 선택할 수 있습니다.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
새 프로세스 서버 경고 | 새 프로세스 서버 경고를 추가했습니다. [자세히 알아봅니다](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>하이퍼-V 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
스토리지 계정 | 사이트 복구는 이제 하이퍼-V에서 Azure 재해 복구에 대해 방화벽이 활성화된 저장소 계정사용을 지원합니다.  방화벽 지원 저장소 계정을 대상 계정또는 캐시 저장소로 선택할 수 있습니다. 방화벽 지원 계정을 사용하는 경우 신뢰할 수 있는 Microsoft 서비스를 허용하는 옵션을 사용하도록 설정해야 합니다.<br/><br/> 시스템 센터 VMM유무에 관계없이 하이퍼 V VM에 지원됩니다.


## <a name="updates-august-2019"></a>업데이트 (2019년 8월)

### <a name="update-rollup-39"></a>롤업 업데이트 39

[업데이트 롤업 39는](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)


### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대한 새로운 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Azure AD가 없는 암호화** | 이제 Azure AD 앱이 없는 암호화는 Windows를 실행하는 관리디스크에 대한 Azure VM 복제에 대해 지원됩니다.
**장애 조치(failover)를 위한 네트워크 리소스** | 이제 다른 지역으로 장애 처리되면 네트워크 리소스 설정(NSG, 로드 분산, 공용 IP 주소)을 VM에 연결할 수 있습니다.

## <a name="updates-july-2019"></a>업데이트 (2019년 7월)

### <a name="update-rollup-38"></a>롤업 업데이트 38

[업데이트 롤업 38은](https://support.microsoft.com/help/4513507/) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)


### <a name="general"></a>일반

사이트 복구는 이제 캐시 저장소 또는 대상 저장소에 대한 범용 v2 저장소 계정의 사용을 지원합니다. 이전에는 v1만 지원되었습니다.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware에서 Azure로 재해 복구

이제 관리되는 디스크를 사용하여 Azure VM에 복제할 때 최대 8TB의 디스크를 복제할 수 있습니다.


## <a name="updates-june-2019"></a>업데이트 (2019년 6월)

### <a name="update-rollup-37"></a>롤업 업데이트 37

[업데이트 롤업 37은](https://support.microsoft.com/help/4508614/) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**GPT 파티션** | 업데이트 롤업 37 이후(모빌리티 서비스 버전 9.25.5241.1)부터 는 UEFI에서 최대 5개의 GPT 파티션이 지원됩니다. 이 업데이트 이전에는 4개가 지원되었습니다.



## <a name="updates-may-2019"></a>업데이트 (2019년 5월)

### <a name="update-rollup-36"></a>롤업 업데이트 36

[업데이트 롤업 36은](https://support.microsoft.com/help/4503156) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**추가된 디스크 복제** | 재해 복구에 이미 활성화된 Azure VM에 추가된 데이터 디스크에 대한 복제를 사용하도록 설정합니다. [자세히 알아봅니다](azure-to-azure-enable-replication-added-disk.md).
**자동 업데이트** | 재해 복구에 사용하도록 설정된 Azure VM에서 실행되는 Mobility 서비스 확장에 대한 자동 업데이트를 구성할 때 사이트 복구에서 만든 기본 계정을 사용하는 대신 사용할 기존 자동화 계정을 선택할 수 있습니다. [자세히 알아봅니다](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**프로세스 서버 모니터링** | 온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 위해 향상된 서버 상태 보고 및 경고를 통해 프로세스 서버 문제를 모니터링하고 문제를 해결합니다. [자세히 알아봅니다](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>업데이트 (2019년 3월)

### <a name="update-rollup-35"></a>롤업 업데이트 35

[업데이트 롤업 35는](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 사이트 복구 에이전트 및 공급자에 대한 업데이트(롤업에 자세히 설명)
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**관리되는 디스크** | 온-프레미스 VMware VM 및 물리적 서버의 복제는 이제 Azure에서 관리되는 디스크로 직접 연결됩니다. 온-프레미스 데이터는 Azure의 캐시 저장소 계정으로 전송되고 복구 지점은 대상 위치의 관리 디스크에 만들어집니다. 이렇게 하면 여러 대상 저장소 계정을 관리할 필요가 없습니다.
**구성 서버** | 사이트 복구는 이제 여러 NIC가 있는 구성 서버를 지원합니다. 볼트에 구성 서버를 등록하기 전에 구성 서버 VM에 어댑터를 추가합니다. 나중에 추가하는 경우 볼트에 서버를 다시 등록해야 합니다.


## <a name="updates-february-2019"></a>업데이트 (2019년 2월)

### <a name="update-rollup-34"></a>롤업 업데이트 34

[업데이트 롤업 34는](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).


### <a name="update-rollup-33"></a>롤업 업데이트 33

[업데이트 롤업 33은](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).


### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**네트워크 매핑** | Azure VM 재해 복구의 경우 복제를 활성화할 때 사용 가능한 모든 대상 네트워크를 사용할 수 있습니다.
**표준 SSD** | 이제 [표준 SSD 디스크를](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)사용하여 Azure VM에 대한 재해 복구를 설정할 수 있습니다.
**직접 스토리지 공간** | 고가용성을 위해 [저장소 공간 직접을](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 사용하여 Azure VM 앱에서 실행되는 앱에 대한 재해 복구를 설정할 수 있습니다.  S2D(저장소 공간 직접)를 사이트 복구와 함께 사용하면 Azure VM 워크로드를 포괄적으로 보호할 수 있습니다. S2D를 사용하면 Azure에서 게스트 클러스터를 호스트할 수 있습니다. 이 기능은 VM이 SAP ASCS 계층, SQL Server 또는 확장 파일 서버와 같은 중요한 응용 프로그램을 호스팅할 때 특히 유용합니다.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**리눅스 BRTFS 파일 시스템** | 사이트 복구는 이제 BRTFS 파일 시스템으로 VMware VM의 복제를 지원합니다. 다음과 같은 경우에는 복제가 지원되지 않습니다.<br/><br/>- BTRFS 파일 시스템 하위 볼륨은 복제를 활성화 한 후 변경됩니다.<br/><br/>- 파일 시스템이 여러 디스크에 분산되어 있습니다.<br/><br/>- BTRFS 파일 시스템은 RAID를 지원합니다.
**Windows Server 2019** | Windows Server 2019를 실행하는 컴퓨터에 대한 지원이 추가되었습니다.


## <a name="updates-january-2019"></a>업데이트 (2019년 1월)


### <a name="accelerated-networking-azure-vms"></a>빠른 네트워킹(Azure VM)

가속화된 네트워킹을 통해 단일 루트 I/O 가상화(SR-IOV)를 VM에 구현하여 네트워킹 성능을 향상시키게 됩니다. Azure VM에 복제를 사용하도록 설정하면 Site Recovery는 가속화된 네트워킹의 사용 여부를 감지합니다. 사용되는 경우 장애 조치(failover) 후 Site Recovery는 자동으로 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 및 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)의 대상 복제본 Azure VM에서 가속화된 네트워킹을 구성합니다.

[자세히 알아봅니다](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>롤업 업데이트 32

[업데이트 롤업 32는](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 레드햇 워크스테이션 6/7, 우분투, 데비안, SUSE의 새로운 커널 버전에 대한 지원이 추가되었습니다.
**직접 스토리지 공간** | 사이트 복구는 S2D(저장소 공간 직접)를 사용하여 Azure VM을 지원합니다.

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 레드햇 엔터프라이즈 리눅스 7.6, 레드햇 워크스테이션 6/7, 오라클 리눅스 6.10 및 오라클 리눅스 7.6, 우분투, 데비안, SUSE용 새로운 커널 버전에 대한 지원이 추가되었다.


### <a name="update-rollup-31"></a>롤업 업데이트 31

[업데이트 롤업 31은](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).

### <a name="vmware-vmsphysical-servers-replication"></a>VM웨어 VM/물리적 서버 복제

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** |  오라클 리눅스 6.8, 오라클 리눅스 6.9, 오라클 리눅스 7.0, 레드햇 호환 커널, 언브레이크 엔터프라이즈 커널(UEK) 릴리스 5에 대한 지원이 추가되었다.
**LVM** | LVM 및 LVM2 볼륨에 대한 지원이 추가되었습니다.<br/><br/> 이제 디스크 파티션 및 LVM 볼륨의 /boot 디렉터리가 지원됩니다.
**디렉터리** | 별도의 파티션으로 설정된 이러한 디렉터리 또는 동일한 시스템 디스크에 없는 파일 시스템에 대한 지원이 추가되었습니다.<br/><br/> /(루트), /boot, /usr, /usr/local, /var, /etc.
**윈도우 서버 2008** | 동적 디스크에 대한 지원이 추가되었습니다.
**장애 조치** | storvsc 및 vsbus가 부팅 드라이버가 아닌 VMware VM의 장애 조치 시간이 개선되었습니다.
**UEFI 지원** | Azure VM은 부팅 유형 UEFI를 지원하지 않습니다. 이제 사이트 복구를 사용하여 UEFI를 사용하여 온-프레미스 물리적 서버를 Azure로 마이그레이션할 수 있습니다. 사이트 복구는 마이그레이션 하기 전에 부팅 유형을 BIOS로 변환 하 여 서버를 마이그레이션 합니다. 사이트 복구는 이전에 VM에 대해서만 이 변환을 지원했습니다. Windows Server 2012 이상을 실행하는 물리적 서버에 대한 지원이 제공됩니다.

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 오라클 리눅스 6.8, 오라클 리눅스 6.9, 오라클 리눅스 7.0, 레드햇 호환 커널, 언브레이크 엔터프라이즈 커널(UEK) 릴리스 5에 대한 지원이 추가되었다.
**리눅스 BRTFS 파일 시스템** | Azure VM에 대해 지원됩니다.
**가용성 영역의 Azure VM** | 가용성 영역에 배포된 Azure VM에 대해 다른 지역으로 복제를 활성화할 수 있습니다. 이제 Azure VM에 대한 복제를 설정하고, 단일 VM 인스턴스, 가용성 집합의 VM 또는 가용성 영역의 VM으로의 장애 조치(failover) 대상을 설정할 수 있습니다. 이 설정은 복제에 영향을 주지 않습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/).
**방화벽 지원 스토리지(포털/PowerShell)** | [방화벽 지원 저장소 계정에](https://docs.microsoft.com/azure/storage/common/storage-network-security)대한 지원이 추가되었습니다.<br/><br/> 재해 복구를 위해 방화벽 지원 저장소 계정에서 관리되지 않는 디스크가 있는 Azure VM을 다른 Azure 지역으로 복제할 수 있습니다.<br/><br/> 방화벽 지원 저장소 계정을 관리되지 않는 디스크의 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> 포털 및 PowerShell 사용에서 지원됩니다.

## <a name="updates-december-2018"></a>업데이트 (2018년 12월)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>모빌리티 서비스(Azure VM)에 대한 자동 업데이트

Site Recovery는 모바일 서비스 확장의 자동 업데이트 옵션을 추가했습니다. 모바일 서비스 확장은 Site Recovery에서 복제한 각 Azure VM에 설치됩니다. 복제를 사용하도록 설정할 때 Site Recovery에서 확장 업데이트를 관리하도록 허용할 것인지 여부를 선택합니다.

업데이트 후 VM을 다시 시작할 필요가 없으며 복제에 영향을 주지 않습니다. [자세히 알아봅니다](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 재해 복구의 새 가격 책정 계산기

Azure VM의 재해 복구에는 VM 라이선스 비용이 발생하며 네트워크 및 저장소 비용이 발생합니다. Azure는 이러한 비용을 계산할 수 있는 [가격 책정 계산기](https://aka.ms/a2a-cost-estimator)를 제공합니다. 이제 Site Recovery는 표준 HDD 디스크 12개와 프리미엄 SSD 디스크 6개가 포함된 VM을 사용하는 3계층 앱을 기반으로 샘플 배포의 가격을 정하는 [가격 견적 예제](https://aka.ms/a2a-cost-estimator)를 제공합니다.

- 이 샘플은 표준의 경우 하루에 10GB, 프리미엄은 20GB의 데이터 변경을 가정합니다.
- 특정 배포에 맞게 변수를 예상 비용으로 변경할 수 있습니다.
- VM 수, 관리 디스크의 수 및 유형, VM에서 예상되는 총 데이터 변경률을 지정할 수 있습니다.
- 또한 압축 비율을 적용하여 대역폭 비용을 예측할 수 있습니다.

공지를 [읽어보세요](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).


## <a name="updates-october-2018"></a>업데이트 (2018년 10월)

### <a name="update-rollup-30"></a>롤업 업데이트 30

[업데이트 롤업(30)은](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구
이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**지역 지원** | 오스트레일리아 중부 1 및 오스트레일리아 중부 2에 대한 사이트 복구 지원이 추가되었습니다.
**디스크 암호화 지원** | Azure AD 앱에서 ADE(Azure Disk 암호화)로 암호화된 Azure VM의 재해 복구에 대한 지원이 추가되었습니다. [자세히 알아봅니다](azure-to-azure-how-to-enable-replication-ade-vms.md).
**디스크 제외** | 이제 Azure VM 복제 중에 초기화되지 않은 디스크가 자동으로 제외됩니다.
**방화벽 지원 스토리지(PowerShell)** | [방화벽 지원 저장소 계정에](https://docs.microsoft.com/azure/storage/common/storage-network-security)대한 지원이 추가되었습니다.<br/><br/> 재해 복구를 위해 방화벽 지원 저장소 계정에서 관리되지 않는 디스크가 있는 Azure VM을 다른 Azure 지역으로 복제할 수 있습니다.<br/><br/> 방화벽 지원 저장소 계정을 관리되지 않는 디스크의 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> PowerShell을 통해서만 지원됩니다.


### <a name="update-rollup-29"></a>롤업 업데이트 29

[업데이트 롤업 29는](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).


## <a name="updates-august-2018"></a>업데이트 (2018년 8월)

### <a name="update-rollup-28"></a>업데이트 롤업 28

[업데이트 롤업 28은](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구
이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | RedHat 엔터프라이즈 리눅스 6.10에 대 한 지원 추가; 센트OS 6.10.<br/><br/>
**클라우드 지원** | 독일 클라우드에서 Azure VM에 대한 재해 복구지원
**구독 간 재해 복구** | 동일한 Azure Active Directory 테넌트 내에서 한 지역의 Azure VM을 다른 구독의 다른 리전으로 복제하는 지원입니다. [자세히 알아봅니다](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/물리적 서버 재해 복구
이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 레드 햇 엔터프라이즈 리눅스에 대 한 추가 지원 6.10, 센트 OS 6.10.<br/><br/> 레거시 BIOS 호환성 모드에서 GUID 파티션 테이블(GPT) 파티션 스타일을 사용하는 Linux 기반 VM이 지원됩니다. 자세한 내용은 [Azure VM FAQ를](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) 참조하십시오.
**마이그레이션 후 VM에 대한 재해 복구** | 복제를 활성화하기 전에 VM에서 모빌리티 서비스를 제거할 필요 없이 온-프레미스 VMware VM이 Azure로 마이그레이션된 보조 지역에 재해 복구를 사용하도록 설정하는 지원입니다.
**윈도우 서버 2008** | Windows Server 2008 R2/2008 64비트 및 32비트를 실행하는 컴퓨터 마이그레이션 지원<br/><br/> 마이그레이션전용(복제 및 장애 조치). 장애 조치(Failback)는 지원되지 않습니다.

## <a name="updates-july-2018"></a>업데이트 (2018년 7월)

### <a name="update-rollup-27-july-2018"></a>업데이트 롤업 27(2018년 7월)

[업데이트 롤업 27은](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 다음 업데이트를 제공합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 자세히 설명된 대로 사이트 복구 에이전트 및 공급자에 대한 업데이트입니다.
**문제 수정/개선 사항** | 여러 가지 수정 사항 및 개선 사항(롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 레드 햇 엔터프라이즈 리눅스 7.5에 대 한 추가 지원.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/물리적 서버 재해 복구

이번 달에 추가된 기능은 표에 요약되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 지원 레드 햇 엔터프라이즈 리눅스에 대 한 추가 7.5, SUSE 리눅스 엔터프라이즈 서버 12.



## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지에서 업데이트를 항상 최신 상태로 유지하세요.
