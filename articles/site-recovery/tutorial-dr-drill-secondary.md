---
title: "Azure Site Recovery를 사용하여 보조 온-프레미스 사이트로 재해 복구 드릴 실행 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 보조 온-프레미스 사이트로 재해 복구 드릴을 실행하는 방법에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Hyper-V VM에 대해 보조 온-프레미스 사이트로 재해 복구 드릴 실행

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 Hyper-V VM에 대해 보조 온-프레미스 사이트로 재해 복구 드릴을 실행하는 방법을 설명합니다. Hyper-V VM은 System Center VMM(Virtual Machine Manager) 사설 클라우드에서 관리해야 합니다. 훈련은 데이터 손실이나 가동 중지 시간 없이 복제 전략의 유효성을 검사하며 프로덕션 환경에 영향을 미치지 않습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 테스트 장애 조치(failover)를 위한 필수 조건 준비
> * 단일 컴퓨터에 대해 테스트 장애 조치(failover) 실행


## <a name="prerequisites"></a>필수 조건

- 보조 사이트의 네트워킹 옵션을 몇 가지로 설정하여 테스트 장애 조치(failover)를 실행할 수 있습니다. 네트워크 없이 또는 기존 네트워크를 사용하여 장애 조치(failover)를 실행하거나, Site Recovery에서 테스트 네트워크를 자동 생성하도록 할 수 있습니다. 
**테스트 장애 조치(failover)를 위해 기존 프로덕션 네트워크를 사용하는 경우** 다음을 고려하세요.
    - 테스트 장애 조치(failover)를 수행할 때는 주 VM을 종료해야 합니다. 이렇게 하지 않으면 ID가 같은 두 VM이 동시에 동일한 네트워크에서 실행됩니다. 
    - 테스트 VM을 변경하는 경우 테스트 장애 조치(failover)를 정리하면 해당 변경 내용이 손실됩니다. 이러한 변경 내용이 기본 VM에 다시 복제되지 않습니다.
    - 프로덕션 네트워크를 테스트하면 프로덕션 작업에서 가동 중지 시간이 발생합니다. 따라서 재해 복구 드릴을 진행 중일 때는 사용자들이 관련 앱을 사용하지 않도록 요청하세요. 
- 복제본 테스트 네트워크는 테스트 장애 조치(failover)에 사용되는 VMM 논리 네트워크 유형과 일치할 필요가 없습니다. 하지만 일부 조합이 작동하지 않습니다. 예를 들어 복제본에서 DHCP 및 VLAN 기반 격리가 사용되는 경우, 테스트 장애 조치(failover) 네트워크에 Windows 네트워크 가상화를 사용할 수 없습니다(IP 주소 풀이 필요하므로). 
- 네트워크 매핑에 선택한 네트워크를 테스트 장애 조치(failover)용으로 사용하지 않는 것이 좋습니다.


## <a name="run-a-test-failover-for-a-vm"></a>VM에 대해 테스트 장애 조치(failover) 실행

1. **복제된 항목**에서 VM > **테스트 장애 조치(Failover)**를 클릭합니다.
2. **테스트 장애 조치(failover)**에서 테스트 장애 조치(failover) 후에 테스트 VM을 네트워크에 연결할 방법을 지정합니다. 이 자습서의 목적에 맞도록, Site Recovery에서 테스트 네트워크를 자동으로 만들게 하는 것이 좋습니다. [자세히 알아봅니다](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery).
3. **확인**을 클릭하여 장애 조치(failover)를 시작합니다. **작업** 탭에서 진행 상황을 추적합니다.
4. 장애 조치(failover)가 완료되면 테스트 VM이 정상적으로 시작되는지 확인합니다.
5. 작업이 완료되면 **테스트 장애 조치(failover) 정리**를 클릭합니다. 그러면 테스트 VM뿐 아니라 테스트 장애 조치(failover) 중에 만들어진 모든 네트워크가 삭제됩니다.
6. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다. 


## <a name="next-steps"></a>다음 단계

[프로덕션 장애 조치(failover) 실행](tutorial-vmm-to-vmm-failover-failback.md)







