---
title: "Azure Site Recovery에서 Azure에 대한 Hyper-V 복제가 작동하는 방식 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 사용하여 온-프레미스 Hyper-V VM을 Azure로 복제할 때 사용되는 구성 요소 및 아키텍처 개요를 제공합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/23/2017
ms.author: raynew
ms.openlocfilehash: 28f775afaf72b11eec0c22f755e4dbd6a485c895
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="how-does-hyper-v-replication-to-azure-work-in-site-recovery"></a>Site Recovery에서 Azure에 대한 Hyper-V 복제가 작동하는 방식


이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 Hyper-V 가상 컴퓨터를 Azure에 복제할 때 포함된 구성 요소 및 프로세스를 설명합니다.

Site Recovery는 System Center VMM(Virtual Machine Manager)의 사용과 상관없이 관리되는 Hyper-V 클러스터 및 독립 실행형 호스트에서 Hyper-V VM을 복제할 수 있습니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.



## <a name="architectural-components"></a>아키텍처 구성 요소

Hyper-V VM을 Azure에 복제하는 경우 여러 가지 구성 요소가 관련됩니다.

**구성 요소** | **위치** | **세부 정보**
--- | --- | ---
**Azure** | Azure에서는 Microsoft Azure 계정, Azure Storage 계정 및 Azure 네트워크가 필요합니다. | 복제된 데이터를 저장소 계정에 저장하고 온-프레미스 사이트에서 장애 조치가 발생한 경우 복제된 데이터를 사용하여 Azure VM을 만듭니다.<br/><br/> Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**VMM 서버** | Hyper-V 호스트는 VMM 클라우드에 위치합니다. | VMM 클라우드에서 Hyper-V 호스트를 관리하는 경우 Recovery Services 자격 증명 모음에 VMM 서버를 등록합니다.<br/><br/> VMM 서버에서 Site Recovery Provider를 설치하여 Azure로 복제를 오케스트레이션합니다.<br/><br/> 논리 및 VM 네트워크를 설정하여 네트워크 매핑을 구성해야 합니다. VM 네트워크는 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
**Hyper-V 호스트** | Hyper-V 호스트 및 클러스터는 VMM 서버를 사용하거나 사용하지 않고 배포할 수 있습니다. | VMM 서버가 없는 경우 Site Recovery 복제를 오케스트레이션하기 위해 Site Recovery Provider가 인터넷을 통해 호스트에 설치됩니다. VMM 서버가 있는 경우, 공급자는 호스트가 아니라 서버에 설치됩니다.<br/><br/> Recovery Services 에이전트는 호스트에 설치되어 데이터 복제를 처리합니다.<br/><br/> 공급자 및 에이전트로부터의 통신은 모두 보호 및 암호화됩니다. Azure 저장소에 복제된 데이터도 암호화됩니다.
**Hyper-V VM** | Hyper-V 호스트 서버에서 실행되는 하나 이상의 VM이 필요합니다. | 명시적으로 VM에 설치해야 하는 것은 없습니다.

[지원 매트릭스](site-recovery-support-matrix-to-azure.md)에서 이러한 구성 요소에 대한 배포 필수 구성 요소 및 요구 사항을 알아봅니다.

**그림 1: Hyper-V 사이트에서 Azure로 복제**

![구성 요소](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**그림 2: VMM 클라우드의 Hyper-V에서 Azure로 복제**

![구성 요소](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>복제 프로세스

**그림 3: Azure로 Hyper-V 복제의 복제 및 복구 프로세스**

![워크플로](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>보호 사용

1. Azure Portal 또는 온-프레미스에서 Hyper-V VM에 대한 보호를 사용하도록 설정하면 **보호 활성화**가 시작됩니다.
2. 이 작업은 사용자가 구성한 설정으로 Azure에 대한 복제를 설정하기 위해 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 메서드를 호출하기 전에 해당 컴퓨터가 전제 조건에 부합하는지 확인합니다.
3. 작업은 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 메서드를 호출하여 초기 복제를 시작하여 전체 VM 복제를 초기화하고 Azure로 VM의 가상 디스크를 전송합니다.
4. **작업** 탭에서 작업을 모니터링할 수 있습니다.
        ![작업 목록](media/site-recovery-hyper-v-azure-architecture/image1.png)![보호 드릴 다운 사용](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>초기 데이터 복제

1. 초기 복제가 트리거될 때 [Hyper-V VM 스냅숏](https://technet.microsoft.com/library/dd560637.aspx)이 만들어집니다.
2. 가상 하드 디스크는 모두 Azure에 복사될 때까지 하나씩 복제됩니다. VM 크기 및 네트워크 대역폭에 따라 시간이 오래 걸릴 수 있습니다. 네트워크 사용을 최적화하려면 [Azure 보호 네트워크 대역폭 사용에 대한 온-프레미스 관리 방법](https://support.microsoft.com/kb/3056159)을 참조하세요.
3. 초기 복제 진행 중에 디스크가 변경될 경우, Hyper-V 복제 로그(.hrl)로 Hyper-V 복제본 복제 추적자가 이러한 변경 내용을 추적합니다. 이러한 파일은 디스크와 동일한 폴더에 있습니다. 각 디스크에는 보조 저장소로 전송되는 .hrl 파일이 연결되어 있습니다.
4. 초기 복제 진행 중에는 스냅숏과 로그 파일이 디스크 리소스를 사용합니다.
5. 초기 복제가 완료되면 VM 스냅숏은 삭제됩니다. 로그의 델타 디스크 변경 내용이 동기화되고 부모 디스크로 병합합니다.


### <a name="finalize-protection"></a>보호 완료

1. 초기 복제를 완료한 후에 **가상 컴퓨터에서 보호 완료** 작업에서 가상 컴퓨터를 보호하도록 네트워크 및 기타 복제 후 설정을 구성합니다.
    ![보호 작업 완료](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Azure에 복제할 경우 장애 조치를 위해 가상 컴퓨터에 대한 설정을 조정해야 할 수 있습니다. 이 시점에서 테스트 장애 조치(Failover)를 실행하여 모든 것이 예상대로 작동하는지 확인할 수 있습니다.

### <a name="replicate-the-delta"></a>델타 복제

1. 초기 복제 후에는 복제 설정에 따라 델타 동기화가 시작됩니다.
2. Hyper-V 복제본 복제 추적자가 .hrl 파일로 가상 하드 디스크에 변경 내용을 추적합니다. 복제를 위해 구성된 각 디스크에는 연결된 .hrl 파일이 있습니다. 이 로그는 초기 복제가 완료된 후 고객의 저장소 계정으로 전송됩니다. 로그가 Azure로 전송 중인 경우 주 디스크의 변경 내용은 같은 디렉터리의 다른 로그 파일에 추적됩니다.
3. 초기 및 델타 복제가 진행되는 동안 VM 보기에서 VM을 모니터링할 수 있습니다. [자세히 알아보기](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="synchronize-replication"></a>복제 동기화

1. 델타 복제에 실패했고 전체 복제에는 대역폭이나 시간이 많이 소모될 경우 VM에서 다시 동기화가 발생합니다. 예를 들어 .hrl 파일이 디스크 크기에 50%에 달한다면 VM이 다시 동기화되도록 표시됩니다.
2.  다시 동기화는 원본 및 대상 가상 컴퓨터의 체크섬을 계산하고 델타 데이터만 전송하므로 보내는 데이터 크기가 최소화됩니다. 다시 동기화는 고정 블록 청크 알고리즘을 사용하며 이 경우 원본 및 대상 파일이 고정 청크로 나누어집니다. 각 청크에 대한 체크섬을 생성한 후 이를 비교하여 원본의 어떤 블록을 대상에 적용해야 하는지 결정합니다.
3. 다시 동기화를 마치면 일반 델타 복제가 다시 시작됩니다. 기본적으로 다시 동기화는 업무 시간 이외에 실행되도록 예약되나 수동으로 가상 컴퓨터를 다시 동기화할 수 있습니다. 예를 들어 네트워크 중단 또는 다른 중단이 발생하면 다시 동기화를 다시 시작할 수 있습니다. 이렇게 하려면 포털 > **다시 동기화**에서 VM을 선택합니다.

    ![수동 다시 동기화](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retry-logic"></a>재시도 논리

복제 오류가 발생한 경우 기본 제공 재시도가 있습니다. 이 논리는 두 가지 범주로 분류할 수 있습니다.

**범주** | **세부 정보**
--- | ---
**복구할 수 없는 오류** | 재시도가 수행되지 않습니다. VM 상태가 **위험**으로 표시되고 관리자 개입이 필요합니다. 이러한 오류의 예로는 VHD 체인 손상, VM 복제본에 대한 잘못된 상태, 네트워크 인증 오류, 권한 부여 오류, VM 찾을 수 없음 오류(독립 실행형 Hyper-V 서버의 경우)가 포함됩니다.
**복구할 수 있는 오류** | 첫 번째 시도부터 재시도 간격을 늘리는 기하급수적인 백오프(1, 2, 4, 8, 10분)를 사용하여 복제 간격마다 재시도가 발생합니다. 오류가 계속되면 30분마다 다시 시도하십시오. 이러한 예로는 네트워크 오류, 디스크 공간 부족, 메모리 부족 상태가 있습니다. |



## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

1. 온-프레미스 Hyper-V VM에서 Azure로 계획된 또는 계획되지 않은 [장애 조치](site-recovery-failover.md)를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 [복구 계획](site-recovery-create-recovery-plans.md)을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
4. 장애 조치를 실행한 후에 Azure에서 만들어진 복제본 VM이 표시되어야 합니다. 필요한 경우 VM에 공용 IP 주소를 할당할 수 있습니다.
5. 그런 다음 복제본 Azure VM에서 워크로드에 액세스하려면 장애 조치를 커밋합니다.
6. 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 [장애 복구](site-recovery-failback-from-azure-to-hyper-v.md)를 수행할 수 있습니다. Azure에서 기본 사이트로 계획된 장애 조치를 시작합니다. 계획된 장애 조치의 경우 데이터가 손실되지 않도록 동일한 VM 또는 대체 위치에 장애 복구를 수행하고 Azure와 온-프레미스 간의 변경 내용을 동기화할 수 있습니다. 온-프레미스에 VM이 만들어지면 장애 조치를 커밋합니다.




## <a name="next-steps"></a>다음 단계

[지원 매트릭스](site-recovery-support-matrix-to-azure.md)를 검토합니다.
