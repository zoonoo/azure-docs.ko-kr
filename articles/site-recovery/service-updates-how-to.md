---
title: Azure Site Recovery의 업데이트 및 구성 요소 업그레이드
description: Azure Site Recovery 서비스 업데이트 및 구성 요소 업그레이드에 대 한 개요를 제공 합니다.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257824"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery의 서비스 업데이트

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 업데이트에 대 한 개요를 제공 하 고 Site Recovery 구성 요소를 업그레이드 하는 방법을 설명 합니다.

Site Recovery는 서비스 업데이트를 정기적으로 게시 합니다. 업데이트에는 새로운 기능, 지원 향상, 구성 요소 업데이트 및 버그 수정이 포함 됩니다. 최신 기능과 픽스를 활용 하기 위해 최신 버전의 Site Recovery 구성 요소를 실행 하는 것이 좋습니다. 
 
 
## <a name="updates-support"></a>업데이트 지원

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery에 대한 지원 정책

항상 최신 구성 요소 버전으로 업그레이드 하는 것이 좋습니다.

**릴리스된 Azure Site Recovery 구성 요소의 모든 새 버전 ' n '을 사용 하는 경우 ' n-4 ' 아래의 모든 버전은 지원 되지 않는 것으로 간주 됩니다**. 

> [!IMPORTANT]
> > N-4 버전에서 N 버전으로 업그레이드 하는 것이 공식적으로 지원 됩니다. 예를 들어,를 실행 하는 경우에는 n-6을 먼저 N-4로 업그레이드 한 다음 N으로 업그레이드 해야 합니다.


### <a name="links-to-currently-supported-update-rollups"></a>현재 지원되는 업데이트 롤업에 대한 링크

 [이 문서의](site-recovery-whats-new.md)최신 업데이트 롤업 (버전 N)을 검토 합니다. Site Recovery은 N-4 버전에 대 한 지원을 제공 합니다.



## <a name="component-expiry"></a>구성 요소 만료

Site Recovery는 전자 메일 (전자 메일 알림을 구독 하는 경우) 또는 포털의 자격 증명 모음 대시보드에서 만료 된 구성 요소 (또는 만료가 임박 함)를 알려 줍니다.

- 또한 업데이트를 사용할 수 있는 경우 포털의 시나리오에 대 한 인프라 보기에서 구성 요소 옆에 **업데이트 사용 가능** 단추가 나타납니다. 이 단추를 클릭 하면 최신 구성 요소 버전을 다운로드할 수 있는 링크로 리디렉션됩니다.
-  Hyper-v Vm을 복제 하는 경우 자격 증명 모음 대시보드 알림을 사용할 수 없습니다. 

전자 메일 알림은 다음과 같이 전송 됩니다.

**Time** | **빈도**
--- | ---
구성 요소 만료 전 60 일 | Bi-주별
다음 53 일 | 일주일에 한 번
최근 7일 | 하루 한 번
만료 후 | Bi-주별


### <a name="upgrading-outside-official-support"></a>공식 지원 외부에서 업그레이드

구성 요소 버전과 최신 릴리스 버전의 차이가 4 보다 큰 경우이는 지원 되지 않는 것으로 간주 됩니다. 이 경우 다음과 같이 업그레이드 합니다. 

1. 현재 설치 된 구성 요소를 현재 버전 + 4로 업그레이드 합니다. 예를 들어 버전 9.16 인 경우 9.20으로 업그레이드 합니다.
2. 그런 다음 호환 되는 다음 버전으로 업그레이드 합니다. 따라서이 예제에서는 9.16를 9.20로 업그레이드 한 후 9.24로 업그레이드 합니다. 

모든 관련 구성 요소에 대해 동일한 프로세스를 수행 합니다.

### <a name="support-for-latest-operating-systemskernels"></a>최신 운영 체제/커널 지원

> [!NOTE]
> 유지 관리 기간을 예약 하 고 다시 부팅이 포함 된 경우에는 먼저 Site Recovery 구성 요소를 업그레이드 한 후 유지 관리 창에서 예약 된 나머지 작업을 계속 진행 하는 것이 좋습니다.

1. 운영 체제/커널 버전을 업그레이드 하기 전에 Site Recovery 대상 버전이 지원 되는지 확인 합니다. 

    - [AZURE VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) 지원.
    - [VMware/물리적 서버](vmware-physical-azure-support-matrix.md#replicated-machines) 지원
    - [Hyper-v](hyper-v-azure-support-matrix.md#replicated-vms) 지원.
2. 업그레이드 하려는 항목을 확인 하려면 [사용 가능한 업데이트](site-recovery-whats-new.md) 를 검토 하세요.
3. 최신 Site Recovery 버전으로 업그레이드 합니다.
4. 운영 체제/커널을 필요한 버전으로 업그레이드 합니다.
5. 부팅이.


이 프로세스를 통해 컴퓨터 운영 체제/커널이 최신 버전으로 업그레이드 되 고 새 버전을 지 원하는 데 필요한 최신 Site Recovery 변경 내용이 컴퓨터에 로드 됩니다.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure로 Azure VM 재해 복구

이 시나리오에서는 [자동 업데이트를 사용 하도록 설정](azure-to-azure-autoupdate.md)하는 것이 좋습니다. 다음과 같이 Site Recovery 업데이트를 관리할 수 있습니다.

- 복제를 사용 하도록 설정 하는 중입니다.
- 자격 증명 모음 내에서 확장 업데이트 설정을 설정 합니다.

업데이트를 수동으로 관리 하려면 다음을 수행 합니다.

1. 자격 증명 모음 > **복제 된 항목**에서 화면 맨 위에 있는 다음 알림을 클릭 합니다. 
    
    **새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 클릭 하 여 설치->**

4. 업데이트를 적용 하려는 Vm을 선택 하 고 **확인**을 클릭 합니다.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Azure에 대 한 VMware v m/물리적 서버 재해 복구

1. 현재 버전 및 [지원 정책](#support-statement-for-azure-site-recovery)에 따라 [다음 지침](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)을 사용 하 여 온-프레미스 구성 서버에 먼저 업데이트를 설치 합니다. 
2. 스케일 아웃 프로세스 서버를 사용 하는 경우 다음 [지침](vmware-azure-manage-process-server.md#upgrade-a-process-server)을 사용 하 여 해당 서버를 업데이트 합니다.
3. 각 보호 된 컴퓨터에서 모바일 에이전트를 업데이트 하려면 [이](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) 문서를 참조 하세요.

### <a name="reboot-after-mobility-service-upgrade"></a>모바일 서비스 업그레이드 후 다시 부팅

모든 최신 변경 내용이 원본 컴퓨터에 로드 되도록 하려면 모바일 서비스를 업그레이드할 때마다 다시 부팅 하는 것이 좋습니다.

마지막으로 다시 부팅 하는 동안 에이전트 버전과 현재 버전의 차이가 4 보다 큰 경우를 제외 하 고는 다시 부팅이 필수가 아닙니다.

이 표의 예에서는이 작업을 수행 하는 방법을 보여 줍니다.

|**에이전트 버전 (마지막 다시 부팅)** | **업그레이드 대상** | **필수 재부팅**|
|---------|---------|---------|
|9.16 |  9.18 | 필수 아님|
|9.16 | 9.19 | 필수 아님|
| 9.16 | 9.20 | 필수 아님
 | 9.16 | 9.21 | 필수.<br/><br/> 9\.20로 업그레이드 한 후 9.21로 업그레이드 하기 전에 다시 부팅 합니다.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Azure로 Hyper-V VM 재해 복구

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-v 사이트와 Azure 간

1. Microsoft Azure Site Recovery 공급자에 대 한 업데이트를 다운로드 합니다.
2. Site Recovery에 등록 된 각 Hyper-v 서버에 공급자를 설치 합니다. 클러스터를 실행 하는 경우 모든 클러스터 노드에서을 업그레이드 합니다.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>온-프레미스 VMM 사이트 및 Azure 간
1. Microsoft Azure Site Recovery 공급자에 대 한 업데이트를 다운로드 합니다.
2. VMM 서버에 공급자를 설치 합니다. VMM이 클러스터에 배포 된 경우 모든 클러스터 노드에 공급자를 설치 합니다.
3. 모든 Hyper-v 호스트 또는 클러스터 노드에 최신 Microsoft Azure Recovery Services 에이전트를 설치 합니다.


## <a name="between-two-on-premises-vmm-sites"></a>두 개의 온-프레미스 VMM 사이트 간
1. Microsoft Azure Site Recovery 공급자에 대 한 최신 업데이트를 다운로드 합니다.
2. 보조 복구 사이트를 관리 하는 VMM 서버에 최신 공급자를 설치 합니다. VMM이 클러스터에 배포 된 경우 모든 클러스터 노드에 공급자를 설치 합니다.
3. 복구 사이트가 업데이트 된 후 기본 사이트를 관리 하는 VMM 서버에 공급자를 설치 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지를 따라 새 업데이트 및 릴리스를 추적할 수 있습니다.
