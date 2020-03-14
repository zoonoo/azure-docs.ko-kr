---
title: Azure Site Recovery를 사용 하 여 Azure에서 Hyper-v Vm 장애 복구
description: Azure Site Recovery를 사용 하 여 Azure에서 Hyper-v Vm을 온-프레미스 사이트로 장애 복구 하는 방법입니다.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281783"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Hyper-V VM에 대한 장애 복구(failback) 실행

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 온-프레미스 사이트에서 Azure로 hyper-v vm의 장애 조치 (failover) 후 생성 된 azure vm을 장애 복구 하는 방법을 설명 합니다.

- Azure에서 온-프레미스 사이트로의 계획 된 장애 조치 (failover)를 실행 하 여 Azure에서 Hyper-v Vm을 장애 복구 (failback) 합니다. 장애 조치 (failover) 방향이 Azure에서 온-프레미스로 인 경우 장애 복구 (failback)로 간주 됩니다.
- Azure는 항상 사용 가능한 환경 이며 Vm은 항상 사용할 수 있으므로 Azure에서 장애 복구는 계획 된 작업입니다. 워크 로드에서 온-프레미스 실행을 다시 시작할 수 있도록 작은 가동 중지 시간을 계획할 수 있습니다. 
- 계획 된 장애 복구는 Azure에서 Vm을 끄고 최신 변경 내용을 다운로드 합니다. 데이터 손실이 필요 하지 않습니다.

## <a name="before-you-start"></a>시작하기 전에

1. 사용할 수 있는 장애 복구 유형 (원래 위치 복구 및 대체 위치 복구 [)을 검토](failover-failback-overview.md#hyper-v-reprotectionfailback) 합니다.
2. Azure Vm이 관리 디스크를 사용 하지 않고 저장소 계정을 사용 하 고 있는지 확인 합니다. 관리 디스크를 사용 하 여 복제 된 Hyper-v Vm의 장애 복구 (Failback)는 지원 되지 않습니다.
3. 온-프레미스 Hyper-v 호스트 (또는 Site Recovery를 사용 하는 경우 System Center VMM 서버가 실행 중이 고 Azure에 연결 되어 있는지 확인 합니다. 
4. Vm에 대 한 장애 조치 (failover) 및 커밋이 완료 되었는지 확인 합니다. Azure에서 Hyper-v Vm의 장애 복구 (failback)에 대 한 특정 Site Recovery 구성 요소를 설정할 필요가 없습니다.
5. 데이터 동기화를 완료 하 고 온-프레미스 VM을 시작 하는 데 필요한 시간은 여러 요소에 따라 달라 집니다. 데이터 다운로드 속도를 높이려면 다운로드를 병렬화 하기 위해 더 많은 스레드를 사용 하도록 Microsoft Recovery Services 에이전트를 구성할 수 있습니다. [자세히 알아봅니다](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>원래 위치로 장애 복구(failback)

Azure의 Hyper-v Vm을 원래 온-프레미스 VM으로 장애 복구 (failback) 하려면 다음과 같이 Azure에서 온-프레미스 사이트로의 계획 된 장애 조치 (failover)를 실행 합니다.

1. 자격 증명 모음 > **복제 된 항목**에서 VM을 선택 합니다. VM > **계획 된 장애 조치 (Failover)** 를 마우스 오른쪽 단추로 클릭 합니다. 복구 계획을 장애 복구 (failback) 하는 경우 계획 이름을 선택 하 고 **장애 조치** > **계획 된 장애 조치**(failover)를 클릭 합니다.
2. **계획 된 장애 조치 (Failover) 확인**에서 원본 및 대상 위치를 선택 합니다. 장애 조치 방향을 유의하십시오. 기본에서 장애 조치 (failover)가 예상 대로 작동 하 고 모든 가상 머신이 보조 위치에 있는 경우이는 정보를 위한 것입니다.
3. **데이터 동기화**에서 옵션을 선택 합니다.
    - **장애 조치 (failover) 전 데이터 동기화 (델타 변경 내용만 동기화)** -이 옵션은 vm을 종료 하지 않고 동기화 될 때 vm의 가동 중지 시간을 최소화 합니다.
        - **1 단계**: Azure VM의 스냅숏을 만들어 온-프레미스 hyper-v 호스트에 복사 합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
        - **2 단계**: Azure VM을 종료 하 여 새로운 변경이 발생 하지 않도록 합니다. 최종 델타 변경 집합은 온-프레미스 서버에 전송 되 고 온-프레미스 VM이 시작 됩니다.
    - **장애 조치 (failover) 중에만 데이터 동기화 (전체 다운로드)** -대부분의 디스크가 변경 되 고 체크섬 계산에 시간을 할애 하지 않기 때문에이 옵션은 더 빠릅니다. 이 옵션은 체크섬 계산을 수행하지 않습니다.
        - 디스크의 다운로드를 수행합니다. 
        - 잠시 동안 (한 달 이상) Azure를 실행 했거나 온-프레미스 VM이 삭제 된 경우이 옵션을 사용 하는 것이 좋습니다.

4. VMM에만 해당, 클라우드에 대해 데이터 암호화를 사용 하는 경우 **암호화 키**에서 VMM 서버에 공급자를 설치 하는 동안 데이터 암호화를 사용 하도록 설정할 때 발행 된 인증서를 선택 합니다.
5. 장애 조치를 시작합니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
6. 장애 조치 (failover) 전에 데이터를 동기화 하는 옵션을 선택한 경우 초기 데이터 동기화가 완료 되 고 Azure에서 가상 머신을 종료할 준비가 되 면 **작업 > 작업** 이름 > **장애 조치 (failover) 완료**를 클릭 합니다. 다음을 수행합니다.
    - Azure 컴퓨터를 종료 합니다.
    - 최신 변경 내용을 온-프레미스 VM으로 전송 합니다.
    - 온-프레미스 VM을 시작 합니다.
7. 이제 온-프레미스 VM 컴퓨터에 로그인 하 여 예상 대로 사용 가능한 지 확인할 수 있습니다.
8. 가상 머신이 커밋 보류 상태입니다. **커밋** 을 클릭하여 장애 조치를 커밋합니다.
9. 장애 복구를 완료 하려면 **역방향 복제** 를 클릭 하 여 온-프레미스 VM을 Azure로 다시 복제를 시작 합니다.



## <a name="fail-back-to-an-alternate-location"></a>대체 위치로 장애 복구(failback) 

다음과 같이 대체 위치로 장애 복구 (failback) 합니다.

1. 새 하드웨어를 설정 하는 경우 [지원 되는 버전의 Windows](hyper-v-azure-support-matrix.md#replicated-vms)를 설치 하 고 hyper-v 역할을 컴퓨터에 설치 합니다.
2. 원본 서버에 있던 동일한 이름의 가상 네트워크 스위치를 만듭니다.
3. 보호 **된 항목** > **보호 그룹** > \<ProtectionGroupName >-> \<virtualmachinename >에서 장애 복구 (failback) 할 VM을 선택한 다음 계획 된 **장애 조치 (failover)** 를 선택 합니다.
4. **계획 된 장애 조치 (Failover) 확인**에서 **온-프레미스 가상 머신**(없는 경우) 만들기를 선택 합니다.
5. **호스트 이름**에서 VM을 추가할 새 hyper-v 호스트 서버를 선택 합니다.
6. **데이터 동기화**에서 장애 조치 (failover) 전에 데이터를 동기화 하는 옵션을 선택 하는 것이 좋습니다. Vm을 종료 하지 않고 동기화 될 때 Vm의 가동 중지 시간을 최소화 합니다. 메서드는 다음 작업을 수행합니다.
    - **1 단계**: Azure VM의 스냅숏을 만들어 온-프레미스 hyper-v 호스트에 복사 합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
    - **2 단계**: Azure VM을 종료 하 여 새로운 변경이 발생 하지 않도록 합니다. 최종 변경 집합이 온-프레미스 서버로 전송되면 온-프레미스 가상 머신이 시작됩니다.
    
7. 확인 표시를 클릭하여 장애 조치(장애 복구)를 시작합니다.
8. 초기 동기화가 완료 되 고 Azure VM을 종료할 준비가 되 면 **작업** > 클릭 하 여 계획 된 장애 조치 (failover) 작업 \<계획 된 장애 **조치 (failover**) > > 합니다. 그러면 Azure 컴퓨터가 종료 되 고, 최신 변경 내용이 온-프레미스 VM으로 전송 되 고, 시작 됩니다.
9. 온-프레미스 VM에 로그인 하 여 모든 것이 예상 대로 작동 하는지 확인할 수 있습니다.
10. **커밋** 을 클릭 하 여 장애 조치 (failover)를 완료 합니다. Commit은 Azure VM 및 해당 디스크를 삭제 하 고 다시 보호할 온-프레미스 VM을 준비 합니다.
10. **역방향 복제** 를 클릭 하 여 온-프레미스 VM을 Azure로 복제를 시작 합니다. Azure에서 VM이 꺼진 이후의 델타 변경 내용만 복제 됩니다.

    > [!NOTE]
    > 데이터 동기화 중에 장애 복구 작업을 취소 하면 온-프레미스 VM이 손상 된 상태가 됩니다. 이는 데이터 동기화가 Azure VM 디스크에서 온-프레미스 데이터 디스크로 최신 데이터를 복사 하 고, 동기화가 완료 될 때까지 디스크 데이터의 상태가 일관 되지 않을 수 있기 때문입니다. 데이터 동기화가 취소 된 후 온-프레미스 VM이 시작 되 면 부팅 되지 않을 수 있습니다. 이 경우 장애 조치 (failover)를 다시 실행 하 여 데이터 동기화를 완료 합니다.


## <a name="next-steps"></a>다음 단계
온-프레미스 VM을 Azure에 복제 한 후 필요에 따라 Azure로 [다른 장애 조치 (failover)를 실행할](site-recovery-failover.md) 수 있습니다.
