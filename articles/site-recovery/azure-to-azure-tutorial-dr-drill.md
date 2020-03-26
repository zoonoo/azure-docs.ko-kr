---
title: Azure Site Recovery를 사용하여 Azure VM 재해 복구 훈련 실행
description: Azure Site Recovery 서비스를 사용하여 Azure VM의 보조 지역에 대한 재해 복구 훈련을 실행하는 방법을 알아봅니다.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166187"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Azure VM의 보조 영역에 대한 재해 복구 훈련 실행

[Azure Site Recovery](site-recovery-overview.md) 서비스는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 복제, 장애 조치(failover), 복구를 포함하여 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 재해 복구를 오케스트레이션합니다.

이 자습서에서는 테스트 장애 조치(failover)를 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Azure VM의 재해 복구 훈련을 실행하는 방법을 보여줍니다. 훈련은 데이터 손실이나 가동 중지 시간 없이 복제 전략의 유효성을 검사하며 프로덕션 환경에 영향을 미치지 않습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 필수 구성 요소 확인
> * VM에 대해 테스트 장애 조치(failover) 실행

> [!NOTE]
> 이 자습서는 최소 단계로 재해 복구 훈련을 수행하는 데 도움이 됩니다. 재해 복구 훈련 수행과 관련된 다양한 기능에 대한 자세한 내용은 Azure VM [복제](azure-to-azure-how-to-enable-replication.md), [네트워킹](azure-to-azure-about-networking.md), [자동화](azure-to-azure-powershell.md) 또는 [문제 해결](azure-to-azure-troubleshoot-errors.md) 설명서를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 수행하기 전에 다음 항목을 확인해야 합니다.

- 테스트 장애 조치(failover)를 실행하기 전에 VM의 속성을 확인하여 재해 복구에 맞게 구성되었는지 확인하는 것이 좋습니다. VM의 **작업** > **재해 복구** > **속성**으로 이동하여 복제 및 장애 조치(failover) 속성을 확인하세요.
- 복제를 활성화할 때 설정된 기본 네트워크가 아니라 **테스트 장애 조치에 대한 별도의 Azure VM 네트워크를 사용하는 것이 좋습니다**.
- 각 NIC의 원본 네트워킹 구성에 따라 **서브넷**, **개인 IP 주소**, **공용 IP**, **네트워크 보안 그룹** 또는 **Load Balancer**를 지정하여 재해 복구 훈련을 수행하기 전에 **컴퓨팅 및 네트워크**의 테스트 장애 조치(failover) 설정에서 각 NIC에 연결할 수 있습니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

이 예제에서는 Recovery Services 자격 증명 모음을 사용하여 VM 테스트 장애 조치(failover)를 수행하는 방법을 보여줍니다.

1. 자격 증명 모음을 선택하고, **보호된 항목** > **복제된 항목**으로 이동하여 VM을 선택합니다.
1. **테스트 장애 조치(Failover)** 에서 장애 조치에 사용할 복구 지점을 선택합니다.
   - **최신**: Site Recovery의 모든 데이터를 처리하고 가장 낮은 RTO(복구 시간 목표)를 제공합니다.
   - **가장 최근에 처리됨**: VM을 Site Recovery에서 처리된 최신 복구 시점으로 장애 조치(failover)합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO가 제공됩니다.
   - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 시점으로 장애 조치합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 특정 복구 지점으로 장애 조치(failover)합니다. 사용자 지정은 단일 VM을 장애 조치(failover)할 때만 사용할 수 있으며 복구 계획을 사용한 장애 조치(failover)에는 사용할 수 없습니다.
1. 장애 조치(failover)가 발생한 후 보조 지역의 Azure VM이 연결할 대상 Azure 가상 네트워크를 선택합니다.

   > [!NOTE]
   > 복제된 항목에 대한 테스트 장애 조치(failover) 설정이 미리 구성된 경우 Azure 가상 네트워크를 선택하는 드롭다운이 표시되지 않습니다.

1. 장애 조치(failover)를 시작하려면 **확인**을 선택합니다. 자격 증명 모음에서 진행률을 추적하려면 **모니터링** > **Site Recovery 작업**으로 이동하여 **테스트 장애 조치(Failover)** 작업을 선택합니다.
1. 장애 조치(failover)가 완료되면 Azure Portal의 **Virtual Machines**에 Azure VM 복제본이 나타납니다. VM이 실행 중이고, 규모가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인하세요.
1. 테스트 장애 조치(failover) 중에 만든 VM을 삭제하려면 복제된 항목 또는 복구 계획에서 **테스트 장애 조치 정리**를 선택합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 장애 조치(failover) 실행](azure-to-azure-tutorial-failover-failback.md)
