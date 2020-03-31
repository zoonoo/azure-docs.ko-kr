---
title: Azure 사이트 복구의 업데이트 및 구성 요소 업그레이드
description: Azure 사이트 복구 서비스 업데이트 및 구성 요소 업그레이드에 대한 개요를 제공합니다.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257824"
---
# <a name="service-updates-in-site-recovery"></a>사이트 복구의 서비스 업데이트

이 문서에서는 Azure [사이트 복구](site-recovery-overview.md) 업데이트에 대한 개요를 제공하고 사이트 복구 구성 요소를 업그레이드하는 방법에 대해 설명합니다.

사이트 복구는 정기적으로 서비스 업데이트를 게시합니다. 업데이트에는 새로운 기능, 지원 개선 사항, 구성 요소 업데이트 및 버그 수정이 포함됩니다. 최신 기능 과 수정 사항을 활용하려면 최신 버전의 사이트 복구 구성 요소를 실행하는 것이 좋습니다. 
 
 
## <a name="updates-support"></a>업데이트 지원

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery에 대한 지원 정책

항상 최신 구성 요소 버전으로 업그레이드하는 것이 좋습니다.

**릴리스된 Azure 사이트 복구 구성 요소의 모든 새 버전 'N'에서는 'N-4' 미만의 모든 버전이 지원되지 않는 것으로 간주됩니다.** 

> [!IMPORTANT]
> 공식 지원은 > N 버전에서 N 버전으로 업그레이드하는 것입니다. 예를 들어 N-6을 실행하는 경우 먼저 N-4로 업그레이드한 다음 N으로 업그레이드해야 합니다.


### <a name="links-to-currently-supported-update-rollups"></a>현재 지원되는 업데이트 롤업에 대한 링크

 이 문서의 최신 업데이트 롤업(버전 N)을 [검토합니다.](site-recovery-whats-new.md) 사이트 복구는 N-4 버전에 대한 지원을 제공한다는 점을 기억하십시오.



## <a name="component-expiry"></a>구성 요소 만료

사이트 복구는 이메일(전자 메일 알림을 구독한 경우) 또는 포털의 볼트 대시보드에서 만료된 구성 요소(또는 만료 에 가까워짐)를 알려주습니다.

- 또한 업데이트를 사용할 수 있는 경우 포털의 시나리오에 대한 인프라 보기에서 **사용 가능한 업데이트** 단추가 구성 요소 옆에 나타납니다. 이 단추는 최신 구성 요소 버전을 다운로드하기 위한 링크로 리디렉션됩니다.
-  Hyper-V VM을 복제하는 경우 볼트 대시보드 알림을 사용할 수 없습니다. 

이메일 알림은 다음과 같이 전송됩니다.

**시간** | **주파수**
--- | ---
부품 만료 60일 전 | 격주 1회
다음 53일 | 한 주에 한 번
최근 7일 | 하루 한 번
만료 후 | 격주 1회


### <a name="upgrading-outside-official-support"></a>외부 공식 지원 업그레이드

구성 요소 버전과 최신 릴리스 버전 간의 차이가 4개보다 크면 지원이 되지 않습니다. 이 경우 다음과 같이 업그레이드하십시오. 

1. 현재 설치된 구성 요소를 현재 버전과 4개 버전으로 업그레이드합니다. 예를 들어 버전이 9.16인 경우 9.20으로 업그레이드합니다.
2. 그런 다음 호환되는 다음 버전으로 업그레이드합니다. 따라서 이 예제에서는 9.16을 9.20으로 업그레이드한 후 9.24로 업그레이드합니다. 

모든 관련 구성 요소에 대해 동일한 프로세스를 따릅니다.

### <a name="support-for-latest-operating-systemskernels"></a>최신 운영 체제/커널 지원

> [!NOTE]
> 유지 관리 기간이 예약되어 있고 재부팅이 포함된 경우 먼저 사이트 복구 구성 요소를 업그레이드한 다음 유지 관리 기간에서 예약된 나머지 활동을 진행하는 것이 좋습니다.

1. 운영 체제/커널 버전을 업그레이드하기 전에 대상 버전이 사이트 복구를 지원하는지 확인합니다. 

    - [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) 지원.
    - [VMware/물리적 서버](vmware-physical-azure-support-matrix.md#replicated-machines) 지원
    - [하이퍼-V](hyper-v-azure-support-matrix.md#replicated-vms) 지원.
2. [사용 가능한 업데이트를](site-recovery-whats-new.md) 검토하여 업그레이드할 내용을 알아보십시오.
3. 최신 사이트 복구 버전으로 업그레이드합니다.
4. 운영 체제/커널을 필요한 버전으로 업그레이드합니다.
5. 다시 부팅합니다.


이 프로세스를 통해 컴퓨터 운영 체제/커널이 최신 버전으로 업그레이드되고 새 버전을 지원하는 데 필요한 최신 사이트 복구 변경 내용이 컴퓨터에 로드됩니다.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure로 Azure VM 재해 복구

이 시나리오에서는 자동 업데이트를 [사용 하는](azure-to-azure-autoupdate.md)것이 좋습니다. 다음과 같이 사이트 복구에서 업데이트를 관리하도록 허용할 수 있습니다.

- 사용 복제 프로세스 중.
- 볼트 내부에 확장 업데이트 설정을 설정합니다.

업데이트를 수동으로 관리하려면 다음을 수행합니다.

1. **복제된 항목>** 볼트에서 화면 상단에서 이 알림을 클릭합니다. 
    
    **새 사이트 복구 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하십시오>**

4. 업데이트를 적용할 VM을 선택한 다음 **확인을**클릭합니다.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Azure에 대한 VMware VM/물리적 서버 재해 복구

1. 현재 버전 및 [지원 설명에](#support-statement-for-azure-site-recovery)따라 [다음 지침을](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)사용하여 온-프레미스 구성 서버에 업데이트를 먼저 설치합니다. 
2. 확장 프로세스 서버가 있는 경우 [다음 지침을](vmware-azure-manage-process-server.md#upgrade-a-process-server)사용하여 업데이트합니다.
3. 보호된 각 컴퓨터에서 모빌리티 에이전트를 업데이트하려면 [이](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) 문서를 참조하십시오.

### <a name="reboot-after-mobility-service-upgrade"></a>모빌리티 서비스 업그레이드 후 재부팅

모빌리티 서비스를 업그레이드할 때마다 모든 최신 변경 사항이 원본 컴퓨터에 로드되도록 재부팅하는 것이 좋습니다.

마지막 재부팅 중에 에이전트 버전과 현재 버전간의 차이가 4개보다 크지 않으면 재부팅이 필수가 아닙니다.

표의 예제에서는 이 작동 방식을 보여 주며 이 방법을 보여 주시고 있습니다.

|**에이전트 버전(마지막 재부팅)** | **로 업그레이드** | **필수 재부팅?**|
|---------|---------|---------|
|9.16 |  9.18 | 필수 아님|
|9.16 | 9.19 | 필수 아님|
| 9.16 | 9.20 | 필수 아님
 | 9.16 | 9.21 | 필수.<br/><br/> 9.20으로 업그레이드한 다음 9.21로 업그레이드하기 전에 재부팅합니다.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Azure로 Hyper-V VM 재해 복구

### <a name="between-a-hyper-v-site-and-azure"></a>하이퍼 V 사이트와 Azure 사이

1. Microsoft Azure 사이트 복구 공급자에 대한 업데이트를 다운로드합니다.
2. 사이트 복구에 등록된 각 Hyper-V 서버에 공급자를 설치합니다. 클러스터를 실행 중인 경우 모든 클러스터 노드에서 업그레이드합니다.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>온-프레미스 VMM 사이트 및 Azure 간
1. Microsoft Azure 사이트 복구 공급자에 대한 업데이트를 다운로드합니다.
2. VMM 서버에 공급자를 설치합니다. VMM이 클러스터에 배포된 경우 모든 클러스터 노드에 공급자를 설치합니다.
3. 모든 Hyper-V 호스트 또는 클러스터 노드에 최신 Microsoft Azure 복구 서비스 에이전트를 설치합니다.


## <a name="between-two-on-premises-vmm-sites"></a>두 개의 온-프레미스 VMM 사이트 간
1. Microsoft Azure 사이트 복구 공급자에 대한 최신 업데이트를 다운로드합니다.
2. 보조 복구 사이트를 관리하는 VMM 서버에 최신 공급자를 설치합니다. VMM이 클러스터에 배포된 경우 모든 클러스터 노드에 공급자를 설치합니다.
3. 복구 사이트가 업데이트된 후 기본 사이트를 관리하는 VMM 서버에 공급자를 설치합니다.

## <a name="next-steps"></a>다음 단계

Azure [업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지를 따라 새 업데이트 및 릴리스를 추적합니다.
