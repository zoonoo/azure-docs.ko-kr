---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182183"
---
Azure는 주기적으로 안정성, 성능 및 가상 머신에 대 한 호스트 인프라의 보안을 개선 하기 위해 해당 플랫폼을 업데이트 합니다. 이러한 업데이트의 목적은 범위에서 네트워킹 구성 요소 업그레이드 하거나 하드웨어 서비스 해제 호스팅 환경의 소프트웨어 구성 요소를 패치 하는 것입니다. 

업데이트는 호스트 된 Vm에 거의 영향을 줍니다. 업데이트 적용에 Azure 업데이트에 대 한 최소 영향력이 강한 메서드를 선택 합니다.

- 업데이트는 다시 부팅이 필요 하지 않습니다 VM 호스트를 업데이트 하거나 VM이 이미 업데이트 된 호스트로 실시간 마이그레이션할 동안 일시 중지 됩니다.

- 유지 관리 다시 부팅에 필요한 경우 계획 된 유지 관리 알림을 받으면 합니다. 또한 azure는 시작할 수 있습니다 유지 관리를 직접 시간에 적합 한 기간을 제공 합니다. 유지 관리는 긴급 하지 않는 한 셀프 유지 관리 기간은 일반적으로 30 일입니다. Azure는 플랫폼 계획 된 유지 관리 Vm 다시 부팅 필요로 하는 사례의 수를 줄이는 기술에 투자 합니다. 

이 페이지에서는 Azure에서 두 가지 유형의 유지 관리를 모두 수행하는 방법을 설명합니다. 계획 되지 않은 이벤트 (중단)에 대 한 자세한 내용은 참조 하세요. [에 대 한 Windows Vm의 가용성 관리](../articles/virtual-machines/windows/manage-availability.md) 에 대 한 해당 문서 [Linux](../articles/virtual-machines/linux/manage-availability.md)합니다.

VM 내에서 예정 된 유지 관리에 대 한 알림을 가져올 수 있습니다 [Windows에 대 한 예약 된 이벤트를 사용 하 여](../articles/virtual-machines/windows/scheduled-events.md) 용인지 [Linux](../articles/virtual-machines/linux/scheduled-events.md)합니다.

계획 된 유지 관리를 관리에 대 한 자세한 내용은 참조 하세요. [계획 된 Linux에 대 한 유지 관리 알림 처리](../articles/virtual-machines/linux/maintenance-notifications.md) 에 대 한 해당 문서 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)합니다.

## <a name="maintenance-that-doesnt-require-a-reboot"></a>다시 부팅이 필요 하지 않은 유지 관리

앞에서 설명한 대로 대부분의 플랫폼 업데이트 고객 Vm 영향을 주지 않습니다. 에 영향을 미치지 업데이트 수 없는 경우 Azure는 고객 Vm에 최소 취합니다 된 업데이트 메커니즘을 선택 합니다. 

10 초 미만의 VM 일시 중지 하는 대부분의 0이 아닌 영향 유지 관리 됩니다. 경우에 따라 Azure 메모리 보존 유지 관리 메커니즘을 사용합니다. 이러한 메커니즘은 최대 30 초 동안 VM을 일시 중지 및 RAM의 메모리를 유지 합니다. VM이 다시 시작 후, 및 해당 클록을 자동으로 동기화 됩니다. 

메모리 보존 유지 관리 Azure Vm의 90% 이상에 적합합니다. G, M, N, 및 H 시리즈에 대 한 작동 하지 않습니다. Azure는 점점 더 실시간 마이그레이션 기술을 사용 하 고 일시 중지 기간을 줄이기 위해 메모리 보존 유지 관리 메커니즘을 향상 시킵니다.  

이러한 유지 관리 작업을 다시 부팅 되지 않아도 되는 한 번 적용 된 하나 이상의 오류 도메인 됩니다. 모든 경고 상태 신호를 받은 것을 중지 합니다. 

이러한 유형의 업데이트 일부 응용 프로그램에 영향을 줄 수 있습니다. VM를 다른 호스트로 실시간 마이그레이션할 경우 일부 중요 한 워크 로드 VM 일시 중지에 이르는 몇 분 후에는 약간의 성능 저하를 표시할 수 있습니다. Azure 유지 관리 하는 동안 영향을 줄이는 VM 유지 관리에 대 한 준비를 하려면 보십시오 [Windows에 대 한 예약 된 이벤트를 사용 하 여](../articles/virtual-machines/windows/scheduled-events.md) 또는 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 이러한 응용 프로그램에 대 한 합니다. Azure는 이러한 중요 한 응용 프로그램에 대 한 유지 관리 제어 기능에 작동 합니다. 

### <a name="live-migration"></a>실시간 마이그레이션

실시간 마이그레이션에는 다시 부팅이 필요 하지 않습니다 하 고 VM에 대 한 메모리를 유지 하는 작업입니다. 일시 중지 또는 중지, 일반적으로 5 초 동안 지속 되 발생 합니다. G를 제외 하 고 모든 infrastructure as a service (IaaS) Vm, M, N 및 H 시리즈 실시간 마이그레이션이 가능합니다. 적합 한 Vm을 Azure 전체 조직에 배포 된 IaaS Vm의 90% 이상 나타냅니다. 

다음 시나리오에서 실시간 마이그레이션을 시작 하는 Azure 플랫폼:
- 계획된 유지 보수
- 하드웨어 오류
- 할당 최적화

Live migration을 사용 하는 몇 가지 계획 된 유지 관리 시나리오 및 예약 된 이벤트를 사용 하 여 라이브 상태일 때 미리 알고 마이그레이션 작업이 시작 됩니다.

실시간 마이그레이션은 하드웨어 오류 발생 가능성을 또는 VM 할당을 최적화 하려는 경우 Azure Machine Learning 알고리즘을 예측 하는 경우 Vm을 이동 하려면 데도 사용할 수 있습니다. 성능이 저하 된 하드웨어 인스턴스를 검색 하는 예측 모델링에 대 한 자세한 내용은 참조 하세요. [예측 기계 학습 및 실시간 마이그레이션을 사용 하 여 개선 하는 Azure VM 복원 력을](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)합니다. 이러한 서비스를 사용 하는 경우 모니터 및 서비스 상태 로그에도 예약 된 이벤트와 같이 Azure portal에서 실시간 마이그레이션 알림이 나타납니다.

## <a name="maintenance-that-requires-a-reboot"></a>다시 부팅이 필요한 유지 관리

Vm 계획 된 유지 관리에 대 한 다시 부팅 해야 하는 드문 경우에서 알려 주도록 미리 합니다. 계획 된 유지 관리에는 두 단계가 있습니다: 셀프 서비스 단계 및 예약 된 유지 관리 단계입니다.

중 합니다 *셀프 서비스 단계*, 일반적으로 지속 되는 4 주, Vm에서 유지 관리를 시작 합니다. 셀프 서비스의 일환으로, 해당 상태 및 마지막 유지 관리 요청의 결과 확인 하려면 각 VM을 쿼리할 수 있습니다.

셀프 서비스 유지 관리를 시작하면 VM이 이미 업데이트된 노드에 다시 배포됩니다. VM이 다시 부팅 때문에 임시 디스크가 손실 되 고 가상 네트워크 인터페이스에 연결 된 동적 IP 주소가 업데이트 됩니다.

셀프 서비스 유지 관리 작업을 중지 하는 동안 오류가 발생 하는 경우 VM이 업데이트 되지 않습니다 및 셀프 서비스 유지 관리를 다시 시도 하는 옵션이 제공 됩니다. 

셀프 서비스 단계 종료 되 면 합니다 *예약 된 유지 관리 단계* 시작 합니다. 이 단계에서는 유지 관리 단계를 계속 쿼리할 수 있습니다 하지만 유지 관리를 직접 시작할 수 없습니다.

다시 부팅이 필요한 유지 관리를 관리에 대 한 자세한 내용은 참조 하세요. [계획 된 Linux에 대 한 유지 관리 알림 처리](../articles/virtual-machines/linux/maintenance-notifications.md) 에 대 한 해당 문서 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)합니다. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>예약 된 유지 관리 기간의 가용성 고려 사항 

예약 된 유지 관리 단계까지 대기 하려는 경우 몇 가지 방법으로 Vm의 최고 가용성을 유지 하는 것이 좋습니다. 

#### <a name="paired-regions"></a>쌍을 이루는 지역

각 Azure 지역은 동일한 지리적 위치 주변 내의 다른 지역과 쌍을 이룹니다. 지역 쌍을 활용 합니다. 예약 된 유지 관리 단계에서 Azure 지역 쌍의 단일 지역의 Vm만을 업데이트합니다. 예를 들어, 미국 중 북부에서에서 VM을 업데이트 하는 동안 Azure 업데이트 되지 않습니다 미국 중남부의에서 모든 VM을 동시에. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다. 지역 쌍의 작동 방식을 이해하면 지역에 VM을 더 잘 배포할 수 있습니다. 자세한 내용은 [Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

#### <a name="availability-sets-and-scale-sets"></a>가용성 집합 및 확장 집합

Azure Vm에서 워크 로드를 배포 하는 경우에 내에서 Vm을 만들 수 있습니다는 *가용성 집합* 응용 프로그램에 고가용성을 제공 합니다. 가용성 집합을 사용 하 여, 재부팅이 필요한 가동 중단 또는 유지 관리 이벤트 중 적어도 하나의 VM를 사용할 수 있는지 확인할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 UD(업데이트 도메인)에 걸쳐 분산됩니다. 예약 된 유지 관리 하는 동안 지정된 된 시간에 한 UD만 업데이트 됩니다. Ud는 순차적으로 업데이트 반드시 되지 않습니다. 

가상 머신 *확장 집합* 은 Azure 계산 리소스를 사용 하 여 배포 하 고 동일한 vm 집합을 단일 리소스로 관리할 수 있습니다. 확장 집합은 가용성 집합의 Vm과 같은 Ud에서 자동으로 배포 됩니다. 으로 가용성 집합을 사용 하 여 확장 집합을 사용 하는 경우 한 UD만 업데이트 됩니다 예약 된 유지 관리 하는 동안 언제 든 지.

고가용성을 위해 Vm 설정에 대 한 자세한 내용은 참조 하세요. [에 대 한 Windows Vm의 가용성 관리](../articles/virtual-machines/windows/manage-availability.md) 에 대 한 해당 문서 [Linux](../articles/virtual-machines/linux/manage-availability.md)합니다.
