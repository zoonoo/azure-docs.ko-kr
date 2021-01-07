---
title: Azure Site Recovery를 사용하여 Azure VM 재해 복구 연습을 실행하는 방법에 대한 자습서
description: 이 자습서에서는 Site Recovery를 사용하여 다른 지역으로 Azure VM을 재해 복구하는 연습을 실행합니다.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395653"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>자습서: Azure VM의 재해 복구 연습 실행

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 복제되는 Azure VM을 다른 Azure 지역으로 재해 복구하는 연습을 실행하는 방법을 알아봅니다. 이 문서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 필수 구성 요소 확인
> * 연습을 시작하기 전에 VM 설정 확인
> * 테스트 장애 조치(failover) 실행
> * 연습을 마친 후 정리


> [!NOTE]
> 이 자습서에서는 재해 복구 연습을 실행하는 데 필요한 최소한의 단계를 제공합니다. 전체 인프라 테스트로 연습을 실행하려면 Azure VM [네트워킹](azure-to-azure-about-networking.md), [자동화](azure-to-azure-powershell.md) 및 [문제 해결](azure-to-azure-troubleshoot-errors.md)에 대해 알아보세요.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하려면 먼저 하나 이상의 Azure VM에 대해 재해 복구를 사용하도록 설정해야 합니다. 이렇게 하려면 이 시리즈의 [첫 번째 자습서를 완료](azure-to-azure-tutorial-enable-replication.md)하세요.

## <a name="verify-vm-settings"></a>VM 설정 확인

1. 자격 증명 모음 > **복제된 항목** 에서 VM을 선택합니다.

    ![VM 속성에서 [재해 복구] 페이지를 여는 옵션](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. **개요** 페이지에서 VM이 보호되고 있으며 정상 상태인지 확인합니다.
3. 테스트 장애 조치(failover)를 실행할 때 대상 지역에서 Azure 가상 네트워크를 선택합니다. 장애 조치(failover) 후에 만들어진 Azure VM은 이 네트워크에 배치됩니다. 

    - 이 자습서에서는 테스트 장애 조치(failover)를 실행할 때 기존 네트워크를 선택합니다.
    - IP 주소 및 네트워킹 구성 요소를 프로덕션 네트워크에서 계속 사용할 수 있도록, 이 연습에서는 비-프로덕션 네트워크를 선택하는 것이 좋습니다.
   - 테스트 장애 조치(failover)에 사용할 네트워크 설정을 미리 구성할 수도 있습니다. 각 NIC에 할당할 수 있는 세부적인 설정으로는 서브넷, 개인 IP 주소, 공용 IP 주소, 부하 분산 장치 및 네트워크 보안 그룹이 있습니다. 여기서는 이 방법을 사용하지 않지만, [이 문서를 검토](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations)하여 자세한 내용을 알아볼 수 있습니다.


## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행


1. **개요** 페이지에서 **테스트 장애 조치(failover)** 를 선택합니다.

    
    ![복제된 항목의 테스트 장애 조치(failover) 단추](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. **테스트 장애 조치(failover)** 에서 복구 지점을 선택합니다. 대상 지역의 Azure VM은 이 복구 지점의 데이터를 사용하여 생성됩니다.
  
   - **가장 최근에 처리됨**: Site Recovery에서 처리한 최신 복구 지점을 사용합니다. 타임스탬프가 표시됩니다. 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 목표 시간)가 제공됩니다.
   -  **최신**: Site Recovery로 전송된 모든 데이터를 처리하여 각 VM의 복구 지점을 만든 후에 해당 복구 지점으로 장애 조치(failover)합니다. 장애 조치(failover)가 트리거될 때 모든 데이터가 Site Recovery로 복제되므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
   - **최신 앱 일치**: 이 옵션은 VM을 최신 앱 일치 복구 시점으로 장애 조치(failover)합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 특정 복구 지점으로 장애 조치(failover)합니다. 사용자 지정은 단일 VM을 장애 조치(failover)할 때만 사용할 수 있으며, 복구 계획을 사용하지 않습니다.

3. **Azure 가상 네트워크** 에서 장애 조치(failover) 후에 만들어진 Azure VM을 배치할 대상 네트워크를 선택합니다. 되도록이면 복제를 사용하도록 설정할 때 만든 네트워크가 아닌 비-프로덕션 네트워크를 선택합니다.

    ![테스트 장애 조치(failover) 설정 페이지](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. 장애 조치(failover)를 시작하려면 **확인** 을 선택합니다.
5. 알림에서 테스트 장애 조치(failover)를 모니터링합니다.

    ![진행률 알림](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![성공 알림](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. 장애 조치(failover)가 완료된 후 대상 지역에 생성된 Azure VM은 Azure Portal **Virtual Machines** 에 표시됩니다. VM이 실행 중이고, 크기가 적절히 조정되었으며, 선택한 네트워크에 연결되어 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

1. **기본 정보** 페이지에서 **테스트 장애 조치(failover) 정리** 를 선택합니다.

    ![정리 프로세스를 시작하는 단추](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. **테스트 장애 조치(failover) 정리** > **참고** 에서 테스트 장애 조치(failover)와 연관된 모든 관찰 내용을 기록하고 저장합니다. 
3. **테스트가 완료되었습니다** 를 선택하여 테스트 장애 조치(failover) 동안 만든 VM을 삭제합니다.

    ![정리 옵션을 제공하는 페이지](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. 알림에서 정리 진행률을 모니터링합니다.

    ![정리 진행률 알림](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![정리 성공 알림](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 재해 복구 연습을 실행하여 장애 조치(failover) 작업이 예상대로 작동하는지 확인했습니다. 이제 전체 장애 조치(failover)를 시도할 수 있습니다.

> [!div class="nextstepaction"]
> [프로덕션 장애 조치(failover) 실행](azure-to-azure-tutorial-failover-failback.md)
