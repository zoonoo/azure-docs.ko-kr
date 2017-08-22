---
title: "Azure Site Recovery를 사용하여 Azure로의 Hyper-V VM 복제(VMM 포함)를 위한 용량 및 크기 조정 계획 | Microsoft 문서"
description: "이 문서에서는 Azure Site Recovery를 사용하여 VMM 클라우드의 Hyper-V VM을 Azure로 복제할 때 용량과 크기 조정을 계획하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.contentlocale: ko-kr
ms.lasthandoff: 07/26/2017

---

# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>3단계: Azure로의 복제를 위한 Hyper-V(VMM 포함)의 용량 및 크기 조정 계획

[배포 필수 구성 요소](vmm-to-azure-walkthrough-prerequisites.md)를 검토한 후 이 문서의 내용을 참조하여 [Azure Site Recovery](site-recovery-overview.md)를 사용해 System Center Virtual Machine Manager(VMM) 클라우드의 온-프레미스 Hyper-V VM을 Azure로 복제할 때 용량 및 크기 조정을 계획하는 방법을 파악할 수 있습니다.

이 문서를 읽은 후에는 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 기술적인 질문을 합니다.


## <a name="how-do-i-start-capacity-planning"></a>용량 계획을 시작하려면 어떻게 해야 하나요?


복제 환경에 대한 정보를 수집한 다음 해당 데이터를 사용하여 용량을 계획함과 동시에 이 문서에서 중점적으로 설명하는 사항을 고려합니다.


## <a name="gather-information"></a>정보 수집

1. VM, VM당 디스크, 디스크당 저장소를 포함하여 복제 환경에 대한 정보를 수집합니다.
2. 복제된 데이터에 대한 일일 변경(이탈)률을 식별합니다. [Hyper-V 용량 계획 도구](https://www.microsoft.com/download/details.aspx?id=39057)를 다운로드하여 변경률을 얻습니다. 평균을 캡처하기 위해 일주일 이상 이 도구를 실행하는 것이 좋습니다.
 

## <a name="figure-out-capacity"></a>용량 파악

수집한 정보에 따라 [Site Recovery Capacity Planner 도구](http://aka.ms/asr-capacity-planner-excel)를 실행하여 원본 환경 및 워크로드를 분석하고, 대역폭 요구 및 원본 위치에 필요한 서버 리소스 및 대상 위치에 필요한 리소스(가상 컴퓨터 및 저장소 등)를 예상합니다. 두 가지 모드로 도구를 실행할 수 있습니다.

- 빠른 계획: 도구를 이 모드로 실행하면 VM, 디스크, 저장소 및 변경 속도의 평균 수치를 기반으로 네트워크 및 서버 프로젝션을 파악할 수 있습니다.
- 구체적 계획: 도구를 이 모드로 실행하면 VM 수준에서 각 워크로드의 세부 정보를 파악할 수 있습니다. VM 호환성을 분석하고 네트워크 및 서버를 예상합니다.

이제 도구를 실행합니다.

1. [도구](http://aka.ms/asr-capacity-planner-excel)를 다운로드합니다.
2. Quick Planner를 실행하려면 [이러한 지침](site-recovery-capacity-planner.md#run-the-quick-planner)에 따라 시나리오 **Hyper-V에서 Azure로**를 선택합니다.
3. Detailed Planner를 실행하려면 [이러한 지침](site-recovery-capacity-planner.md#run-the-detailed-planner)에 따라 시나리오 **Hyper-V에서 Azure로**를 선택합니다.

## <a name="control-network-bandwidth"></a>네트워크 대역폭 제어

필요한 대역폭이 계산되면 복제에 사용되는 대역폭 양을 제어하기 위한 몇 가지 옵션이 생깁니다.

* **대역폭 제한**: Azure에 복제되는 Hyper-V 트래픽이 특정 Hyper-V 호스트를 통과합니다. 호스트 서버의 대역폭을 제한할 수 있습니다.
* **대역폭 영향**: 몇 가지 레지스트리 키를 사용하는 복제에 사용되는 대역폭에 영향을 줄 수 있습니다.

### <a name="throttle-bandwidth"></a>대역폭 제한
1. Hyper-V 호스트 서버에서 Microsoft Azure 백업 MMC 스냅인을 엽니다. 기본적으로 Microsoft Azure 백업에 대한 바로 가기가 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin에 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.
3. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택하고 근무 시간 및 근무 외 시간에 대한 한도를 설정합니다. 유효 범위는 초당 512Kbps~102Mbp입니다.

    ![대역폭 제한](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet를 사용하여 제한을 설정할 수도 있습니다. 다음은 샘플입니다.

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 은 제한이 필요 없다는 뜻입니다.

### <a name="influence-network-bandwidth"></a>네트워크 대역폭에 영향
1. 레지스트리에서 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**으로 이동합니다.
   * 복제 디스크의 대역폭 트래픽에 영향을 주려면 **UploadThreadsPerVM**값을 수정하거나 없는 경우 키를 만듭니다.
   * Azure에서 장애 복구(failback) 트래픽에 대한 대역폭에 영향을 주려면 **DownloadThreadsPerVM**값을 수정합니다.
2. 기본값은 4입니다. "과도하게 프로비전된" 네트워크에서는 이러한 레지스트리 키를 기본값에서 변경해야 합니다. 최대값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.

## <a name="next-steps"></a>다음 단계

[4단계: 네트워킹 계획](vmm-to-azure-walkthrough-network.md)으로 이동합니다.

