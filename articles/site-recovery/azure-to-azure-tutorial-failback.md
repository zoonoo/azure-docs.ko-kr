---
title: Azure Site Recovery 서비스를 사용하여 재해 복구용 보조 Azure 지역에 복제된 Azure VM을 장애 복구(failback)합니다.
description: Azure Site Recovery 서비스를 사용하여 Azure VM을 장애 복구(failback)하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a3b67e9b0dc41eeb14000400912892fbf29acfe2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399492"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Azure 지역 간에 Azure VM 장애 복구(failback)

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 단일 Azure VM을 장애 복구(failback)하는 방법을 설명합니다. 장애 조치(Failover)한 후에 주 지역이 사용 가능해지면 장애 복구(failback)해야 합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 보조 지역에서 VM을 장애 복구(failback)합니다.
> * 주 VM을 보조 지역으로 다시 보호합니다.
> 
> [!NOTE]
> 
> 이 자습서에서는 최소한의 사용자 지정 작업을 통해 VM 몇 개를 대상 지역으로 장애 조치(failover)한 후 다시 원본 지역으로 장애 조치(failover)하는 방법을 안내합니다. 자세한 지침은 [Azure VM에 대한 방법 가이드](https://docs.microsoft.com/azure/virtual-machines/windows/)를 검토하세요.

## <a name="before-you-start"></a>시작하기 전에

* VM이 **장애 조치(failover) 커밋됨** 상태인지 확인합니다.
* 주 지역을 사용할 수 있으며 그 안에 새 리소스를 만들고 액세스할 수 있는지 확인합니다.
* 다시 보호가 사용하도록 설정되어 있는지 확인합니다.

## <a name="fail-back-to-the-primary-region"></a>주 지역으로 장애 복구(failback)

VM이 다시 보호되면 필요에 따라 주 지역으로 장애 복구(failback)할 수 있습니다.

1. 자격 증명 모음에서 **복제된 항목**을 선택하고 다시 보호된 VM을 선택합니다.

    ![주 지역으로 장애 복구(failback)](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. 주 지역으로 테스트 장애 조치(failover)를 수행하려면 **테스트 장애 조치(failover)** 를 선택합니다.
4. 테스트 장애 조치(failover)의 복구 지점 및 가상 네트워크를 선택하고, **확인**을 선택합니다. 주 지역에 만든 테스트 VM을 검토할 수 있습니다.
5. 테스트 장애 조치(failover)가 성공적으로 완료되면 **테스트 장애 조치(failover) 정리**를 선택하여 테스트 장애 조치(failover)의 원본 지역에 만든 리소스를 정리합니다.
6. **복제된 항목**에서 VM을 선택하고 **장애 조치(failover)** 를 선택합니다.
7. **장애 조치(Failover)** 에서 장애 조치(failover)할 복구 지점을 선택합니다.
    - **최신(기본값)** : Site Recovery 서비스의 모든 데이터를 처리하고 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
    - **가장 최근에 처리됨**: VM을 Site Recovery에서 처리한 최신 복구 지점으로 되돌립니다.
    - **사용자 지정**: 특정 복구 지점으로 장애 조치(failover)합니다. 이 옵션은 테스트 장애 조치(failover)를 수행하는 데 유용합니다.

8. 장애 조치(failover)를 트리거하기 전에 Site Recovery에서 원본 VM을 종료하려고 시도하는 경우 **장애 조치(Failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. 종료가 실패하더라도 장애 조치(failover)는 계속됩니다. Site Recovery는 장애 조치(failover) 후 원본을 정리하지 않습니다.
9. **작업** 페이지에서 장애 조치(failover) 진행 상황을 확인합니다.
10. 장애 조치(failover)가 완료된 후에는 VM에 로그인하여 유효성을 검사합니다. 필요에 따라 복구 지점을 변경할 수 있습니다.
11. 장애 조치(failover)를 확인한 후에는 **장애 조치(failover) 커밋**을 선택합니다. 커밋하면 사용 가능한 복구 지점이 모두 삭제됩니다. 복구 지점 변경 옵션은 더 이상 사용할 수 없습니다.
12. VM이 장애 조치(failover) 및 장애 복구(failback)된 것으로 표시됩니다.

    ![주 지역 및 보조 지역의 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> 재해 복구 VM은 종료/할당 취소됨 상태로 유지됩니다. 이는 Site Recovery가 VM 정보를 저장하기 때문에 설계상 의도된 동작이며, 나중에 주 지역에서 보조 지역으로 장애 조치(failover)할 때 유용할 수 있습니다. 할당 취소된 VM은 요금이 부과되지 않으므로 그대로 유지해야 합니다.

## <a name="next-steps"></a>다음 단계

다시 보호 흐름에 대해 [자세히 알아봅니다](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).
