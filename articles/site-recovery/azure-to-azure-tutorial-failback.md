---
title: 재해 복구하는 동안 Azure Site Recovery를 통해 Azure VM을 주 지역으로 장애 조치(failover)하는 방법에 대한 자습서입니다.
description: Azure Site Recovery를 사용하여 Azure VM을 주 지역으로 장애 복구(failback)하는 방법에 대한 자습서입니다.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393949"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>자습서: Azure VM을 주 지역으로 장애 복구(failback)

Azure VM을 보조 Azure 지역으로 장애 조치(failover)한 후에는 이 자습서에 따라 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 VM을 주 Azure 지역으로 장애 복구(failback)할 수 있습니다.  이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> 
> * 필수 구성 요소를 검토합니다.
> * 보조 지역에서 VM을 장애 복구(failback)합니다.
> * 주 VM을 다시 보조 지역으로 보호합니다.
> 
> [!NOTE]
> 이 자습서에서는 최소한의 단계로 VM을 장애 복구(failback)하는 방법을 보여줍니다. 전체 설정으로 장애 조치(failover)를 실행하려면 Azure VM [네트워킹](azure-to-azure-about-networking.md), [자동화](azure-to-azure-powershell.md) 및 [문제 해결](azure-to-azure-troubleshoot-errors.md)에 대해 알아보세요.



## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하려면 앞에서 다음 단계를 수행했어야 합니다.

1. 하나 이상의 Azure VM에 대한 [복제를 설정](azure-to-azure-tutorial-enable-replication.md)하고, 해당 VM의 [재해 복구 연습](azure-to-azure-tutorial-dr-drill.md)을 실행했습니다.
2. 주 지역에서 보조 지역으로 [VM을 장애 조치(failover)](azure-to-azure-tutorial-failover-failback.md)하고, 보조 지역에서 주 지역으로 복제되도록 VM을 다시 보호했습니다. 
3. 주 지역을 사용할 수 있으며 그 안에 새 리소스를 만들고 액세스할 수 있는지 확인합니다.

## <a name="fail-back-to-the-primary-region"></a>주 지역으로 장애 복구(failback)

VM이 다시 보호되면 필요에 따라 주 지역으로 장애 복구(failback)할 수 있습니다.

1. 자격 증명 모음 > **복제된 항목** 에서 VM을 선택합니다.

2. VM 개요 페이지에서 VM이 정상 상태이고 동기화가 완료되었는지 확인한 후 장애 조치(failover)를 실행합니다. VM이 *보호됨* 상태여야 합니다.

    ![보호됨 상태의 VM을 보여주는 VM 개요 페이지](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. 개요 페이지에서 **장애 조치(failover)** 를 선택합니다. 이번에는 테스트 장애 조치(failover)를 수행하지 않으므로 확인하라는 메시지가 표시됩니다.

    [테스트 장애 조치(failover) 없이 장애 조치(failover)를 실행하는 것에 동의한다는 것을 보여주는 페이지](./media/azure-to-azure-tutorial-failback/no-test.png)

4. **장애 조치(failover)** 에서 방향이 보조에서 기본인 것을 확인하고, 복구 지점을 선택합니다. 대상(주 지역)의 Azure VM은 이 복구 지점의 데이터를 사용하여 생성됩니다.
   - **가장 최근에 처리됨**: Site Recovery에서 처리한 최신 복구 지점을 사용합니다. 타임스탬프가 표시됩니다. 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 목표 시간)가 제공됩니다.
   -  **최신**: Site Recovery로 전송된 모든 데이터를 처리하여 각 VM의 복구 지점을 만든 후에 해당 복구 지점으로 장애 조치(failover)합니다. 장애 조치(failover)가 트리거될 때 모든 데이터가 Site Recovery로 복제되므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
   - **최신 앱 일치**: 이 옵션은 VM을 최신 앱 일치 복구 시점으로 장애 조치(failover)합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 특정 복구 지점으로 장애 조치(failover)합니다. 사용자 지정은 단일 VM을 장애 조치(failover)할 때만 사용할 수 있으며, 복구 계획을 사용하지 않습니다.

    > [!NOTE]
    > VM을 복제하도록 설정한 후에 추가한 디스크에 VM을 장애 조치(failover)하면 복제 지점은 복구에 사용할 수 있는 디스크를 표시합니다. 예를 들어 두 번째 디스크를 추가하기 전에 만든 복제 지점은 "2개 중 1개 디스크"를 표시합니다.

4. 장애 조치(Failover)를 시작하기 전에 Site Recovery에서 원본 VM을 종료하도록 하려면 **장애 조치(Failover)를 시작하기 전에 머신을 종료합니다** 를 선택합니다. 종료하여 데이터 손실을 방지할 수 있습니다. 종료가 실패하더라도 장애 조치는 계속됩니다. 

    ![장애 조치(failover) 설정 페이지](./media/azure-to-azure-tutorial-failback/failover.png)    

3. 장애 조치(failover)를 시작하려면 **확인** 을 선택합니다.
4. 알림에서 장애 조치(failover)를 모니터링합니다.

    ![장애 조치(failover) 진행률 알림](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![장애 조치(failover) 성공 알림](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>VM 다시 보호

VM을 주 지역으로 장애 복구(failback)한 후에는 보조 지역으로 다시 복제를 시작하도록 VM을 다시 보호해야 합니다.

1. VM의 **개요** 페이지에서 **다시 보호** 를 선택합니다.

    ![주 지역에서 다시 보호하는 단추](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. 주 지역의 대상 설정을 검토합니다. 신규로 표시된 리소스는 다시 보호 작업의 일환으로 Site Recovery에서 만든 것입니다.
3. **확인** 을 선택하여 다시 보호 프로세스를 시작합니다. 이 프로세스는 초기 데이터를 대상 위치로 보낸 다음, VM의 델타 정보를 대상에 복제합니다.

     ![복제 설정을 보여주는 페이지](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. 알림에서 다시 보호 진행률을 모니터링합니다. 

    ![다시 보호 진행률 알림](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [다시 보호 진행률 알림](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>리소스 정리

관리 디스크를 사용하는 VM의 경우 장애 복구(failback)가 완료되고 주 지역에서 보조 지역으로 복제하도록 VM을 다시 보호한 후에는 Site Recovery가 보조 재해 복구 지역의 머신을 자동으로 정리합니다. 보조 지역의 VM 및 NIC를 수동으로 삭제할 필요가 없습니다. 비관리 디스크를 사용하는 VM은 정리되지 않습니다.

장애 복구(failback) 후 복제를 완전히 해제하면 Site Recovery는 복제로 보호되는 머신을 정리합니다. 이 경우 관리 디스크를 사용하지 않는 VM의 디스크도 정리됩니다. 
 
## <a name="next-steps"></a>다음 단계

이 자습서에서는 보조 지역에서 주 지역으로 VM을 장애 복구(failback)했습니다. 이 단계는 VM에 복제를 사용하도록 설정하고, 재해 복구 연습을 시도하고, 주 지역에서 보조 지역으로 장애 조치(failover)하고, 마지막으로 장애 복구(failback)하는 프로세스의 마지막 단계입니다.

> [!div class="nextstepaction"]
> 이번에는 [온-프레미스 VM](vmware-azure-tutorial-prepare-on-premises.md)을 Azure로 재해 복구해 보세요.

