---
title: 유지 관리 및 업데이트
description: Azure에서 실행되는 가상 시스템에 대한 유지 관리 및 업데이트 개요입니다.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250232"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure에서 가상 머신 유지 관리

Azure는 가상 시스템에 대한 호스트 인프라의 안정성, 성능 및 보안을 개선하기 위해 정기적으로 플랫폼을 업데이트합니다. 이러한 업데이트의 용도는 호스팅 환경의 소프트웨어 구성 요소 패치에서 네트워킹 구성 요소 업그레이드 또는 하드웨어 서비스 해제에 이르기까지 다양합니다. 

업데이트는 호스트된 VM에 거의 영향을 주지 않습니다. 업데이트에 영향을 미치는 경우 Azure는 업데이트에 가장 영향을 미치지 않는 방법을 선택합니다.

- 업데이트에 재부팅이 필요하지 않으면 호스트가 업데이트되는 동안 VM이 일시 중지되거나 VM이 이미 업데이트된 호스트로 라이브 마이그레이션됩니다. 
- 유지 관리를 다시 부팅해야 하는 경우 계획된 유지 관리에 대한 알림을 받게 됩니다. Azure는 또한 자신에게 적합한 시간에 유지 관리를 직접 시작할 수 있는 시간 창을 제공합니다. 자체 유지 관리 기간은 일반적으로 유지 관리가 긴급하지 않는 한 30일입니다. Azure는 계획된 플랫폼 유지 관리를 통해 VM을 재부팅해야 하는 경우수를 줄이기 위해 기술에 투자하고 있습니다. 계획된 유지 관리 방법에 대한 지침은 Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) 또는 [포털을](maintenance-notifications-portal.md)사용하여 계획된 유지 관리 알림 처리를 참조하십시오.

이 페이지에서는 Azure에서 두 가지 유형의 유지 관리를 모두 수행하는 방법을 설명합니다. 계획되지 않은 이벤트(중단)에 대한 자세한 내용은 [Windows용 VM의 가용성 또는](./windows/manage-availability.md) [Linux용](./linux/manage-availability.md)해당 문서 관리를 참조하십시오.

VM 내에서 Windows 또는 [Linux용](./linux/scheduled-events.md) [예약 이벤트를 사용하여](./windows/scheduled-events.md) 예정된 유지 관리에 대한 알림을 받을 수 있습니다.



## <a name="maintenance-that-doesnt-require-a-reboot"></a>재부팅이 필요하지 않은 유지 관리

대부분의 플랫폼 업데이트는 고객 VM에 영향을 주지 않습니다. 영향을 전혀 받지 않는 업데이트가 불가능한 경우 Azure는 고객 VM에 가장 큰 영향을 미치지 않는 업데이트 메커니즘을 선택합니다. 

대부분의 비영향 유지 관리는 VM을 10초 미만 동안 일시 중지합니다. 경우에 따라 Azure는 메모리 보존 유지 관리 메커니즘을 사용합니다. 이러한 메커니즘은 VM을 최대 30초 동안 일시 중지하고 RAM의 메모리를 보존합니다. 그러면 VM이 다시 시작되고 해당 클럭이 자동으로 동기화됩니다. 

메모리 보존 유지 관리는 Azure VM의 90% 이상에서 작동합니다. G, M, N 및 H 시리즈에서는 작동하지 않습니다. Azure는 점점 더 라이브 마이그레이션 기술을 사용하고 메모리 보존 유지 관리 메커니즘을 개선하여 일시 중지 기간을 줄입니다.  

재부팅이 필요하지 않은 이러한 유지 관리 작업은 한 번에 하나의 오류 도메인에 적용됩니다. 경고 상태 신호를 받으면 중지됩니다. 

이러한 유형의 업데이트는 일부 응용 프로그램에 영향을 줄 수 있습니다. VM이 다른 호스트로 라이브 마이그레이션되는 경우 일부 중요한 워크로드는 VM 일시 중지로 이어지는 몇 분 안에 약간의 성능 저하를 나타낼 수 있습니다. VM 유지 관리를 준비하고 Azure 유지 관리 중에 영향을 줄이려면 이러한 응용 프로그램에 대해 Windows 또는 [Linux용](./linux/scheduled-events.md) [예약 이벤트를 사용해](./windows/scheduled-events.md) 보십시오. 

또한 공용 미리 보기에는 재부팅이 필요하지 않은 유지 관리를 관리하는 데 도움이 되는 기능인 유지 관리 제어 기능이 있습니다. [Azure 전용 호스트](./linux/dedicated-hosts.md) 또는 [격리된 VM을](../security/fundamentals/isolation-choices.md)사용해야 합니다. 유지 보수 제어를 통해 플랫폼 업데이트를 건너뛰고 35일 기간 내에 원하는 시간에 업데이트를 적용할 수 있습니다. 자세한 내용은 [유지 관리 제어 및 Azure CLI를 위한 컨트롤 업데이트를](maintenance-control-cli.md)참조하십시오.


### <a name="live-migration"></a>실시간 마이그레이션

라이브 마이그레이션은 다시 부팅할 필요가 없고 VM에 대한 메모리를 보존하는 작업입니다. 일반적으로 5초 이상 지속되지 않는 일시 정지 또는 정지를 일으킵니다. G, M, N 및 H 계열을 제외한 모든 서비스(IaaS) VM은 라이브 마이그레이션을 받을 수 있습니다. 적격 VM은 Azure 플릿에 배포되는 IaaS VM의 90% 이상을 나타냅니다. 

Azure 플랫폼은 다음 시나리오에서 라이브 마이그레이션을 시작합니다.
- 계획된 유지 보수
- 하드웨어 오류
- 할당 최적화

일부 계획된 유지 관리 시나리오에서는 라이브 마이그레이션을 사용하며 예약된 이벤트를 사용하여 라이브 마이그레이션 작업이 시작되는 시기를 미리 알 수 있습니다.

Azure 기계 학습 알고리즘이 임박한 하드웨어 오류를 예측하거나 VM 할당을 최적화하려는 경우 라이브 마이그레이션을 사용하여 VM을 이동할 수도 있습니다. 성능이 저하된 하드웨어의 인스턴스를 검색하는 예측 모델링에 대한 자세한 내용은 [예측 기계 학습 및 라이브 마이그레이션을 통해 Azure VM 복원력 향상을](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)참조하십시오. 라이브 마이그레이션 알림은 이러한 서비스를 사용하는 경우 예약된 이벤트뿐만 아니라 모니터 및 서비스 상태 로그의 Azure 포털에도 표시됩니다.

## <a name="maintenance-that-requires-a-reboot"></a>재부팅이 필요한 유지 관리

계획된 유지 관리를 위해 VM을 다시 부팅해야 하는 드문 경우는 사전에 알림을 받게 됩니다. 계획된 유지 관리는 셀프 서비스 단계와 예약된 유지 관리 단계의 두 단계로 있습니다.

일반적으로 4주 동안 지속되는 *셀프 서비스 단계에서VM에서*유지 관리를 시작합니다. 셀프 서비스의 일부로 각 VM을 쿼리하여 상태와 마지막 유지 관리 요청의 결과를 확인할 수 있습니다.

셀프 서비스 유지 관리를 시작하면 VM이 이미 업데이트된 노드에 다시 배포됩니다. VM이 재부팅되므로 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다.

셀프 서비스 유지 관리 중에 오류가 발생하면 작업이 중지되고 VM이 업데이트되지 않으며 셀프 서비스 유지 관리를 다시 시도할 수 있습니다. 

셀프 서비스 단계가 끝나면 *예약된 유지 관리 단계가* 시작됩니다. 이 단계에서는 유지 관리 단계를 쿼리할 수 있지만 유지 관리를 직접 시작할 수는 없습니다.

재부팅이 필요한 유지 관리 관리에 대한 자세한 내용은 Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) 또는 [포털을](maintenance-notifications-portal.md)사용하여 **계획된 유지 관리 알림 처리를** 참조하십시오. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>예약된 유지 관리 중 가용성 고려 사항 

예약된 유지 관리 단계까지 기다려야 하는 경우 VM의 최고 가용성을 유지하기 위해 고려해야 할 몇 가지 사항이 있습니다. 

#### <a name="paired-regions"></a>쌍을 이루는 지역

각 Azure 지역은 동일한 지리적 위치 내의 다른 리전과 페어링됩니다. 함께, 그들은 지역 쌍을 합니다. 예약된 유지 관리 단계에서 Azure는 지역 쌍의 단일 리전에서만 VM을 업데이트합니다. 예를 들어 미국 중북부에서 VM을 업데이트하는 동안 Azure는 미국 중남부의 VM을 동시에 업데이트하지 않습니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다. 지역 쌍의 작동 방식을 이해하면 지역에 VM을 더 잘 배포할 수 있습니다. 자세한 내용은 [Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

#### <a name="availability-sets-and-scale-sets"></a>가용성 집합 및 확장 집합

Azure VM에 워크로드를 배포할 때 응용 프로그램에 고가용성을 제공하기 위해 *가용성 집합* 내에서 VM을 만들 수 있습니다. 가용성 집합을 사용하면 재부팅이 필요한 가동 중단 또는 유지 관리 이벤트 중에 하나 이상의 VM을 사용할 수 있는지 확인할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 업데이트 도메인에 걸쳐 분산됩니다. 예약된 유지 관리 중에 지정된 시간에 하나의 업데이트 도메인만 업데이트됩니다. 업데이트 도메인이 반드시 순차적으로 업데이트되지는 않습니다. 

가상 시스템 *크기 집합은* 동일한 VM 집합을 단일 리소스로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. 확장 집합은 가용성 집합의 VM과 같이 UD에 자동으로 배포됩니다. 가용성 집합과 마찬가지로 확장 집합을 사용할 때 예약된 유지 관리 중에 지정된 시간에 UD가 하나만 업데이트됩니다.

고가용성을 위해 VM을 설정하는 것에 대한 자세한 내용은 [Windows용 VM의 가용성 관리](./windows/manage-availability.md) 또는 [Linux용](./linux/manage-availability.md)해당 문서를 참조하십시오.

#### <a name="availability-zones"></a>가용성 영역

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 

가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. Azure 지역의 세 영역에서 세 개 이상의 VM을 만드는 경우 VM은 세 개의 장애 도메인과 3개의 업데이트 도메인에 효과적으로 분산됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 다른 영역에 있는 VM이 동시에 업데이트되지 않게 합니다.

각 인프라 업데이트는 단일 리전 내에서 영역별로 롤아웃됩니다. 그러나 영역 1에서 배포가 진행되고 영역 2에서 다른 배포가 동시에 진행될 수 있습니다. 배포가 모두 직렬화되지는 않습니다. 그러나 단일 배포는 위험을 줄이기 위해 한 번에 하나의 영역만 롤아웃합니다.

## <a name="next-steps"></a>다음 단계 

[Azure CLI,](maintenance-notifications-cli.md)Azure [PowerShell](maintenance-notifications-powershell.md) 또는 [포털을](maintenance-notifications-portal.md) 사용하여 계획된 유지 관리를 관리할 수 있습니다. 