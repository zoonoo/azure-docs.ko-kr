---
title: Azure Site Recovery를 사용한 Hyper-V 복제 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 성공적으로 설치, 구성 및 관리하는 데 도움이 되는 기술 개념을 이해합니다.
services: site-recovery
documentationcenter: ''
author: Rajani-Janaki-Ram
manager: mkjain
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2016
ms.author: rajanaki

---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>Azure Site Recovery를 사용한 Hyper-V 복제
이 문서는 Azure Site Recovery를 사용하여 Hyper-V 사이트 또는 System Center VMM(Virtual Machine Manager) 사이트와 Azure 간 보호를 성공적으로 구성하고 관리하는데 도움이 될 수 있는 기술적인 개념을 설명합니다.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>온-프레미스와 Azure 간 복제를 위한 원본 환경 설정
온-프레미스와 Azure 간 재해 복구 설정의 일환으로 VMM 서버에 Azure Site Recovery 공급자를 다운로드하고 설치해야 합니다. 각 Hyper-V 호스트에 Azure Recovery Services 에이전트를 설치합니다.

![온-프레미스와 Azure 간 복제를 위한 VMM 사이트 배포](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Hyper-V 관리 인프라에 소스 환경을 설정하는 것은 VMM 관리 인프라에 소스 환경을 설정하는 것과 유사합니다. 유일한 차이는 공급자와 에이전트가 Hyper-V 호스트 자체에 설치된다는 점입니다. VMM 환경에서 공급자는 VMM 서버에 설치되고 에이전트는 Hyper-V 호스트에 설치됩니다(Azure로 복제인 경우).

## <a name="workflows"></a>워크플로
### <a name="enable-protection"></a>보호 사용
Azure Portal 또는 온-프레미스의 가상 컴퓨터를 보호한 후에 **보호 사용** 이라고 명명된 Site Recovery 작업이 시작됩니다. **작업** 탭 아래에서 이것을 모니터링할 수 있습니다.

![작업 목록의 “보호 사용" 작업](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

**보호 사용** 작업은 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 메서드를 호출하기 전에 필수 구성 요소를 확인합니다. 이 메서드는 보호하는 동안 구성된 입력을 사용하여 Azure에 복제를 만듭니다.

**보호 사용** 작업은 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 메서드를 호출하여 온-프레미스에서 초기 복제를 시작합니다. 이 메서드는 가상 컴퓨터의 가상 디스크를 Azure에 보냅니다.

![“보호 사용" 작업의 세부 정부](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>가상 컴퓨터에서 보호 완료
초기 복제가 트리거될 때 [Hyper-V VM 스냅숏](https://technet.microsoft.com/library/dd560637.aspx) 이 만들어집니다. 모든 디스크가 Azure에 업로드될 때까지 가상 하드 디스크가 하나씩 처리됩니다. 일반적으로 디스크 크기 및 대역폭에 따라 마치는 데 다소 시간이 소요됩니다. 네트워크 사용을 최적화하려면 [Azure 보호 네트워크 대역폭 사용에 대한 온-프레미스 관리 방법](https://support.microsoft.com/kb/3056159)을 참조하세요.

초기 복제를 마친 후에는 **가상 컴퓨터에 대한 보호 완료** 작업이 네트워크 및 복제 후 설정을 구성합니다. 초기 복제가 진행되는 동안:

* 디스크에 대한 모든 변경은 추적됩니다. 
* 스냅숏 및 HRL(Hyper-V 복제 로그) 파일에 대해 추가적인 디스크 공간이 소비됩니다.

초기 복제가 완료되면, Hyper-V VM 스냅숏이 삭제됩니다. 이러한 삭제는 초기 복제 후 데이터 변경을 부모 디스크에 병합합니다.

![작업 목록의 “가상 컴퓨터에 대한 보호 완료" 작업](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>델타 복제
Hyper-V 복제본 복제 엔진의 일부인 Hyper-V 복제본 복제 추적기는 가상 하드 디스크 변경 내용을 Hyper-V 복제본 로그(*.hrl) 파일로 추적합니다. HRL 파일은 연결된 디스크와 같은 디렉터리에 있습니다.

복제를 위해 구성된 각 디스크에는 연결된 HRL 파일이 있습니다. 이 로그는 초기 복제가 완료된 후 고객의 저장소 계정으로 전송됩니다. 로그가 Azure로 전송 중인 경우 주 디스크의 변경 내용은 같은 디렉터리의 다른 로그 파일에 추적됩니다.

초기 복제 또는 델타 복제 중에 [가상 컴퓨터에 대한 복제 상태 모니터링](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine)에 설명된 것처럼 VM 보기에서 VM 복제 상태를 모니터링할 수 있습니다.  

### <a name="resynchronization"></a>다시 동기화
델타 복제가 실패하고 전체 초기 복제가 네트워크 대역폭 또는 시간적인 면에서 비용이 많이 드는 경우 가상 컴퓨터는 다시 동기화하도록 표시됩니다. 예를 들어 HRL 파일 크기가 총 디스크 크기의 50%까지 누적되는 경우 가상 컴퓨터는 다시 동기화하도록 표시됩니다. 다시 동기화는 원본 및 대상 가상 컴퓨터 디스크의 체크섬을 계산하고 차이만 전송하여 네트워크를 통해 전송되는 데이터 양을 최소화합니다.

다시 동기화를 마치면 일반 델타 복제가 다시 시작됩니다. 네트워크 중단 또는 다른 중단이 발생하면 다시 동기화를 다시 시작할 수 있습니다.

기본적으로 자동으로 예약된 다시 동기화는 업무 시간을 피해 이루어지도록 구성됩니다. 가상 컴퓨터를 수동으로 다시 동기화해야 하는 경우 포털에서 가상 컴퓨터를 선택하고 **다시 동기화**를 클릭합니다.

![수동 다시 동기화](media/site-recovery-understanding-site-to-azure-protection/image04.png)

다시 동기화는 고정 블록 청크 알고리즘을 사용하며 이 경우 원본 및 대상 파일이 고정 청크로 나누어집니다. 각 청크에 대한 체크섬을 생성한 후 이를 비교하여 원본의 어떤 블록을 대상에 적용해야 하는지 결정합니다.

### <a name="retry-logic"></a>재시도 논리
복제 오류에 대한 재시도 논리가 기본 제공됩니다. 이 논리는 두 가지 범주로 분류할 수 있습니다.

| Category | 시나리오 |
| --- | --- |
| 복구할 수 없는 오류 |재시도가 수행되지 않습니다. 가상 컴퓨터 복제 상태가 **위험**으로 표시되고 관리자 개입이 필요합니다. 이러한 예로 다음이 포함됩니다. <ul><li>끊어진 VHD 체인</li><li>복제 가상 컴퓨터의 잘못된 상태</li><li>네트워크 인증 오류</li><li>권한 부여 오류</li><li>독립 실행형 Hyper-V 서버의 경우 찾지 못한 가상 컴퓨터</li></ul> |
| 복구할 수 있는 오류 |첫 번째 시도부터 재시도 간격을 늘리는 기하급수적인 백오프(1, 2, 4, 8, 10분)를 사용하여 복제 간격마다 재시도가 발생합니다. 오류가 계속되면 30분마다 다시 시도하십시오. 이러한 예로 다음이 포함됩니다. <ul><li>네트워크 오류</li><li>디스크 공간 부족</li><li>메모리 부족 상태</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Hyper-V 가상 컴퓨터 보호 및 복구 수명 주기
![Hyper-V 가상 컴퓨터 보호 및 복구 수명 주기](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>기타 참조
* [VMware, VMM, Hyper-V 및 물리적 사이트를 위한 보호 모니터링 및 문제 해결](site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft 지원을 위한 연락](site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
* [일반적인 Azure Site Recovery 오류 및 해결 방법](site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!--HONumber=Oct16_HO2-->


