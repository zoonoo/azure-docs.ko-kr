---
title: Azure Site Recovery를 사용하여 Azure에서 Hyper-V VM 장애 복구(failback)
description: Azure Site Recovery를 사용하여 Hyper-V VM을 Azure에서 온-프레미스 사이트로 장애 복구(failback)하는 방법
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: 96c3dce17ab78e08b28bb41c0100e51a72a666e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110256"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Hyper-V VM에 대한 장애 복구(failback) 실행

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 사이트에서 Azure로 Hyper-V VM의 장애 조치(failover) 후 생성된 Azure VM을 장애 복구(failback)하는 방법을 설명합니다.

- Azure에서 온-프레미스 사이트로의 계획된 장애 조치(failover)를 실행하여 Azure에서 Hyper-V VM을 장애 복구(failback)합니다. 장애 조치(failover) 방향이 Azure에서 온-프레미스로인 경우 장애 복구(failback)로 간주됩니다.
- Azure는 고가용성 환경이며 VM은 항상 사용할 수 있으므로 Azure에서 장애 복구(failback)는 계획된 작업입니다. 워크로드에서 온-프레미스 실행을 다시 시작할 수 있도록 작은 가동 중지 시간을 계획할 수 있습니다. 
- 계획된 장애 복구(failback)는 Azure에서 VM을 끈 다음, 최신 변경 내용을 다운로드합니다. 데이터 손실은 발생하지 않습니다.

## <a name="before-you-start"></a>시작하기 전에

1. 사용 가능한 [장애 복구(failback) 유형](failover-failback-overview.md#hyper-v-reprotectionfailback) - 원래 위치 복구 및 대체 위치 복구를 검토합니다.
2. Azure VM이 관리 디스크가 아닌 스토리지 계정을 사용하고 있는지 확인합니다. 관리 디스크를 사용하여 Azure 컴퓨터로 장애 조치(failover)된 Hyper-V 가상 머신의 장애 복구(failback)는 지원되지 않습니다.
3. 온-프레미스 Hyper-V 호스트(또는 Site Recovery를 사용하는 경우 System Center VMM 서버)가 실행 중이고 Azure에 연결되어 있는지 확인합니다. 
4. VM에 대한 장애 조치(failover) 및 커밋이 완료되었는지 확인합니다. Azure에서 Hyper-V VM의 장애 복구(failback)에 대한 특정 Site Recovery 구성 요소를 설정할 필요가 없습니다.
5. 데이터 동기화를 완료하고 온-프레미스 VM을 시작하는 데 필요한 시간은 여러 요소에 따라 달라집니다. 데이터 다운로드 속도를 높이려면 다운로드를 병렬화하기 위해 더 많은 스레드를 사용하도록 Microsoft Recovery Services 에이전트를 구성할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>원래 위치로 장애 복구(failback)

Azure의 Hyper-V VM을 원래 온-프레미스 VM으로 장애 복구(failback)하려면 다음과 같이 Azure에서 온-프레미스 사이트로의 계획된 장애 조치(failover)를 실행합니다.

1. 자격 증명 모음 > **복제된 항목** 에서 VM을 선택합니다. VM > **계획된 장애 조치(Failover)** 를 오른쪽 단추로 클릭합니다. 복구 계획을 장애 복구(failback)하는 경우 계획 이름을 선택하고 **장애 조치(failover)**  > **계획된 장애 조치(failover)** 를 클릭합니다.
2. **계획된 장애 조치(failover) 확인** 에서 원본 및 대상 위치를 선택합니다. 장애 조치 방향을 유의하십시오. 기본으로부터 장애 조치(failover)가 예상대로 작동하고 모든 가상 머신이 보조 위치에 있다면 이것은 정보 제공용입니다.
3. **데이터 동기화** 에서 옵션을 선택합니다.
    - **장애 조치(failover) 전 데이터 동기화(델타 변경 내용만 동기화)** —VM을 종료하지 않고 동기화할 때 VM의 가동 중지 시간을 최소화하는 옵션입니다.
        - **1단계**: Azure VM의 스냅샷을 생성한 후 온-프레미스 Hyper-V 호스트로 복사합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
        - **2단계**: Azure VM이 종료되어, 여기서 새로운 변경이 발생하지 않습니다. 최종 델타 변경 집합이 온-프레미스 서버로 전송되고, 온-프레미스 VM이 시작합니다.
    - **장애 조치(failover) 중에만 데이터 동기화(전체 다운로드)** —이 옵션은 대부분의 디스크가 변경되고 체크섬 계산에 시간을 할애하지 않기 때문에 더 빠릅니다. 이 옵션은 체크섬 계산을 수행하지 않습니다.
        - 디스크의 다운로드를 수행합니다. 
        - Azure를 한 달 이상 실행했거나 온-프레미스 VM을 삭제한 경우, 이 옵션을 사용하는 것이 좋습니다.

4. VMM에 대해서만 클라우드에 대해 데이터 암호화를 사용하는 경우 VMM 서버에 공급자를 설치하는 중에 데이터 암호화를 사용하도록 설정할 때 발행된 인증서를 **암호화 키** 에서 선택합니다.
5. 장애 조치를 시작합니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
6. 장애 조치(failover) 이전에 데이터를 동기화하는 옵션을 선택한 경우 초기 데이터 동기화를 완료하고 Azure에서 가상 머신을 종료할 준비가 되었을 때 **작업** > 작업 이름 > **장애 조치(failover) 완료** 를 차례로 클릭합니다. 다음을 수행합니다.
    - Azure 컴퓨터를 종료합니다.
    - 최신 변경 내용을 온-프레미스 VM으로 전송합니다.
    - 온-프레미스 VM을 시작합니다.
7. 이제 온-프레미스 VM 컴퓨터에 로그인하여 예상대로 사용 가능한지 확인할 수 있습니다.
8. 가상 머신이 커밋 보류 상태입니다. **커밋** 을 클릭하여 장애 조치(failover)를 커밋합니다.
9. 장애 복구(failback)를 완료하려면 **역방향 복제** 를 클릭하여 온-프레미스 VM을 Azure로 다시 복제하기 시작합니다.



## <a name="fail-back-to-an-alternate-location"></a>대체 위치로 장애 복구(failback) 

다음과 같이 대체 위치로 장애 복구(failback)합니다.

1. 새 하드웨어를 설정하는 경우 [지원되는 버전의 Windows](hyper-v-azure-support-matrix.md#replicated-vms)를 설치하고 Hyper-V 역할을 컴퓨터에 설치합니다.
2. 원본 서버에 있던 동일한 이름의 가상 네트워크 스위치를 만듭니다.
3. **보호된 항목** > **보호 그룹** > \<ProtectionGroupName> -> \<VirtualMachineName>에서 장애 복구(failback)하려는 VM을 선택하고 **계획된 장애 조치(failover)** 를 선택합니다.
4. **계획된 장애 조치(failover) 확인** 에서 **존재하지 않는 경우 온-프레미스 가상 머신 만들기** 를 선택합니다.
5. **호스트 이름** 에서 VM을 배치하려는 새 Hyper-V 호스트 서버를 선택합니다.
6. **데이터 동기화** 에서 장애 조치(failover) 전에 데이터 동기화 옵션을 선택하는 것이 좋습니다. 이 옵션은 VM을 종료하지 않고 동기화하므로 VM에 대한 가동 중지 시간을 최소화합니다. 메서드는 다음 작업을 수행합니다.
    - **1단계**: Azure VM의 스냅샷을 생성한 후 온-프레미스 Hyper-V 호스트로 복사합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
    - **2단계**: Azure VM이 종료되어, 여기서 새로운 변경이 발생하지 않습니다. 최종 변경 집합이 온-프레미스 서버로 전송되면 온-프레미스 가상 머신이 시작됩니다.
    
7. 확인 표시를 클릭하여 장애 조치(장애 복구)를 시작합니다.
8. 초기 동기화가 완료되고 Azure VM을 종료할 준비가 되면 **작업** > \<planned failover job> > **완전한 장애 조치(failover)** 를 클릭합니다. 그러면 Azure 컴퓨터가 종료되고 최신 변경 내용이 온-프레미스 VM에 전송된 다음, 시작됩니다.
9. 온-프레미스 VM에 로그인하여 모든 것이 예상대로 작동되는지 확인할 수 있습니다.
10. 그다음 **커밋** 을 클릭하여 장애 조치(failover)를 완료합니다. 커밋은 Azure VM과 해당 디스크를 삭제하고 온-프레미스 VM을 다시 보호할 수 있도록 준비합니다.
10. 온-프레미스 VM을 Azure에 복제하기 시작하려면 **역방향 복제** 를 클릭합니다. Azure에서 VM이 꺼진 이후의 델타 변경 내용만 복제됩니다.

    > [!NOTE]
    > 데이터 동기화 중에 장애 복구(failback) 작업을 취소하면 온-프레미스 VM이 손상됩니다. 이는 데이터 동기화가 Azure VM 디스크에서 온-프레미스 데이터 디스크로 최신 데이터를 복사하고, 동기화를 완료할 때까지 디스크 데이터가 일관되지 않을 수 있기 때문입니다. 데이터 동기화가 취소된 후 온-프레미스 VM이 시작되면 부팅되지 않을 수 있습니다. 이 경우 장애 조치(failover)를 다시 실행하여 데이터 동기화를 완료합니다.


## <a name="next-steps"></a>다음 단계
온-프레미스 VM을 Azure에 복제한 후 필요에 따라 Azure로 [다른 장애 조치(failover)를 실행](site-recovery-failover.md)할 수 있습니다.
