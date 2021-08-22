---
title: Azure Site Recovery의 업데이트 및 구성 요소 업그레이드
description: Azure Site Recovery 서비스 업데이트 및 구성 요소 업그레이드에 대해 대략적으로 설명합니다.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 08/11/2021
ms.openlocfilehash: 7f4dad014a1904de8068dd9cf6f0f95a53eb87e2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528485"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery의 서비스 업데이트

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 업데이트에 대한 개요를 제공하고 Site Recovery 구성 요소를 업그레이드하는 방법을 설명합니다.

Site Recovery는 서비스 업데이트를 정기적으로 게시합니다. 업데이트에는 새로운 기능, 향상된 지원 기능, 구성 요소 업데이트 및 버그 수정이 포함됩니다. 최신 기능과 수정 사항을 활용하기 위해 최신 버전의 Site Recovery 구성 요소를 실행하는 것이 좋습니다. 
 
 
## <a name="updates-support"></a>업데이트 지원

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery에 대한 지원 정책

항상 최신 구성 요소 버전으로 업그레이드하는 것이 좋습니다.

**릴리스된 Azure Site Recovery 구성 요소의 모든 새 버전 ‘N’에서 ‘N-4’ 이전의 모든 버전은 지원되지 않는 것으로 간주됩니다**. 

> [!IMPORTANT]
> N-4보다 큰 버전에서 N 버전으로 업그레이드하는 것이 공식적으로 지원됩니다. 예를 들어, N-6에서 실행하는 경우 먼저 N-4로 업그레이드한 후 N으로 업그레이드해야 합니다.


### <a name="links-to-currently-supported-update-rollups"></a>현재 지원되는 업데이트 롤업에 대한 링크

 [이 문서](site-recovery-whats-new.md)의 최신 업데이트 롤업(버전 N)을 검토합니다. Site Recovery는 N-4 버전에 대한 지원을 제공합니다.



## <a name="component-expiry"></a>구성 요소 만료

Site Recovery는 메일(메일 알림을 구독하는 경우) 또는 Portal의 자격 증명 모음 대시보드에서 만료된(또는 만료가 임박한) 구성 요소를 알려 줍니다.

- 또한 업데이트를 사용할 수 있는 경우 Portal의 시나리오에 대한 인프라 보기에서 구성 요소 옆에 **업데이트 사용 가능** 단추가 나타납니다. 이 단추를 클릭하면 최신 구성 요소 버전을 다운로드할 수 있는 링크로 리디렉션됩니다.
-  Hyper-V VM을 복제하는 경우 자격 증명 모음 대시보드 알림을 사용할 수 없습니다. 

메일 알림은 다음과 같이 전송됩니다.

**Time** | **빈도**
--- | ---
구성 요소가 만료되기 60일 전 | 격주마다
다음 53일 | 일주일에 한 번
최근 7일 | 하루에 한 번
만료 후 | 격주마다


### <a name="upgrading-outside-official-support"></a>공식 지원 외에 업그레이드

구성 요소 버전과 최신 릴리스 버전의 차이가 4보다 큰 경우 지원되지 않는 것으로 간주됩니다. 이 경우 다음과 같이 업그레이드합니다. 

1. 현재 설치된 구성 요소를 현재 버전+4로 업그레이드합니다. 예를 들어, 버전이 9.16인 경우 9.20으로 업그레이드합니다.
2. 그런 다음, 호환되는 다음 버전으로 업그레이드합니다. 따라서 이 예제에서는 9.16을 9.20으로 업그레이드한 후 9.24로 업그레이드합니다. 

모든 관련 구성 요소에 대해 동일한 프로세스를 수행합니다.

### <a name="support-for-latest-operating-systemskernels"></a>최신 운영 체제/커널 지원

> [!NOTE]
> 유지 관리 기간이 예약되어 있고 이 기간 안에 다시 부팅도 포함될 경우 먼저 Site Recovery 구성 요소를 업그레이드한 후 유지 관리 기간에 예약된 나머지 작업을 계속 진행하는 것이 좋습니다.

1. 운영 체제/커널 버전을 업그레이드하기 전에 대상 버전이 지원되는 Site Recovery인지 확인합니다. 

    - [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) 지원
    - [VMware/물리적 서버](vmware-physical-azure-support-matrix.md#replicated-machines) 지원
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) 지원
2. 업그레이드하려는 항목을 확인하려면 [사용 가능한 업데이트](site-recovery-whats-new.md)를 검토하세요.
3. 최신 Site Recovery 버전으로 업그레이드합니다.
4. 운영 체제/커널을 필요한 버전으로 업그레이드합니다.
5. 다시 부팅합니다.


이 프로세스를 통해 머신 운영 체제/커널이 최신 버전으로 업그레이드되고 새 버전을 지원하는 데 필요한 최신 Site Recovery 변경 내용이 머신에 로드됩니다.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure로 Azure VM 재해 복구

이 시나리오에서는 [자동 업데이트를 사용](azure-to-azure-autoupdate.md)할 것을 강력히 권장합니다. 다음과 같이 Site Recovery를 통해 업데이트를 관리할 수 있습니다.

- 복제를 사용하도록 설정하는 동안
- 자격 증명 모음 내에서 확장 업데이트 설정을 지정하여

업데이트를 수동으로 관리하려는 경우 다음 옵션 중 하나를 선택할 수 있습니다.

1. 새 에이전트 업데이트를 사용할 수 있는 경우 Site Recovery는 페이지 맨 위에 자격 증명 모음의 알림을 표시합니다. 자격 증명 모음 > **복제된 항목** 에서 화면 맨 위에 있는 다음 알림을 클릭합니다. 
    
    **새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.>** <br/><br/>업데이트를 적용하려는 VM을 선택하고 **확인** 을 클릭합니다.

2. VM 재해 복구 개요 페이지에서 에이전트가 만료될 예정인 경우 '에이전트 상태' 필드에 '중요 업그레이드'라고 표시됩니다. 이를 클릭하고 다음 지침에 따라 가상 머신을 수동으로 업그레이드합니다.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Azure로 VMware VM/물리적 서버 재해 복구

1. 현재 버전 및 [지원 정책](#support-statement-for-azure-site-recovery)에 따라 [다음 지침](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)을 사용하여 온-프레미스 구성 서버에 먼저 업데이트를 설치합니다. 
2. 스케일 아웃 프로세스 서버를 사용하는 경우 [다음 지침](vmware-azure-manage-process-server.md#upgrade-a-process-server)을 사용하여 해당 서버를 업데이트합니다.
3. 보호된 각 머신에서 모바일 에이전트를 업데이트하려면 [이](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) 문서를 참조하세요.

### <a name="reboot-after-mobility-service-upgrade"></a>모바일 서비스 업그레이드 후 다시 부팅

모든 최신 변경 내용이 원본 컴퓨터에 로드되도록 모바일 서비스를 업그레이드할 때마다 다시 부팅하는 것이 좋습니다.

마지막으로 다시 부팅하는 동안 에이전트 버전과 현재 버전의 차이가 4보다 큰 경우를 제외하고는 반드시 다시 부팅이 필요한 것은 아닙니다.

이 표의 예에서는 이 작업의 작동 방식을 보여 줍니다.

|**에이전트 버전(마지막 다시 부팅)** | **다음 버전으로 업그레이드** | **다시 부팅 필수 여부**|
|---------|---------|---------|
|9.16 |  9.18 | 필수 아님|
|9.16 | 9.19 | 필수 아님|
| 9.16 | 9.20 | 필수 아님
 | 9.16 | 9.21 | 필수.<br/><br/> 9\.20로 업그레이드한 후 9.21로 업그레이드하기 전에 다시 부팅합니다.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Azure로 Hyper-V VM 재해 복구

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-V 사이트와 Azure 간

1. Microsoft Azure Site Recovery Provider 업데이트를 다운로드합니다.
2. Site Recovery에 등록된 각 Hyper-V 서버에 공급자를 설치합니다. 클러스터를 실행하는 경우 모든 클러스터 노드에서 업그레이드합니다.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>온-프레미스 VMM 사이트 및 Azure 간
1. Microsoft Azure Site Recovery Provider 업데이트를 다운로드합니다.
2. VMM 서버에 공급자를 설치합니다. VMM이 클러스터에 배포된 경우 모든 클러스터 노드에 공급자를 설치합니다.
3. 모든 Hyper-V 호스트 또는 클러스터 노드에 최신 Microsoft Azure Recovery Services 에이전트를 설치합니다.


## <a name="between-two-on-premises-vmm-sites"></a>두 개의 온-프레미스 VMM 사이트 간
1. Microsoft Azure Site Recovery Provider에 대한 최신 업데이트를 다운로드합니다.
2. 보조 복구 사이트를 관리하는 VMM 서버에 최신 공급자를 설치합니다. VMM이 클러스터에 배포된 경우 모든 클러스터 노드에 공급자를 설치합니다.
3. 복구 사이트를 업데이트한 후 기본 사이트를 관리하는 VMM 서버에서 공급자를 설치합니다.

## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지에 따라 새 업데이트 및 릴리스를 추적합니다.
