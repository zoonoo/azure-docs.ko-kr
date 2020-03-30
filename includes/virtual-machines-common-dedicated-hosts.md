---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129558"
---
## <a name="limitations"></a>제한 사항

- 가상 시스템 크기 집합은 현재 전용 호스트에서 지원되지 않습니다.

## <a name="benefits"></a>이점 

전체 호스트를 예약하면 다음과 같은 이점이 있습니다.

-   물리적 서버 수준에서 하드웨어 격리. 호스트에는 다른 VM이 배치되지 않습니다. 전용 호스트는 동일한 데이터 센터에 배포되며 격리되지 않은 다른 호스트와 동일한 네트워크 및 기본 스토리지 인프라를 공유합니다.
-   Azure 플랫폼에서 시작된 유지 관리 이벤트를 제어합니다. 대부분의 유지 관리 이벤트는 가상 시스템에 거의 영향을 미치지 않지만 일시 중지할 때마다 영향을 줄 수 있는 몇 가지 중요한 워크로드가 있습니다. 전용 호스트를 사용하면 유지 관리 기간을 옵트인하여 서비스에 미치는 영향을 줄일 수 있습니다.
-   Azure 하이브리드 혜택을 통해 Windows 및 SQL에 대한 고유한 라이선스를 Azure로 가져올 수 있습니다. 하이브리드 혜택을 사용하면 추가 혜택을 얻을 수 있습니다. 자세한 내용은 [Azure 하이브리드 이점을](https://azure.microsoft.com/pricing/hybrid-benefit/)참조하십시오.


## <a name="groups-hosts-and-vms"></a>그룹, 호스트 및 VM  

![전용 호스트에 대한 새 리소스 보기](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**호스트 그룹은** 전용 호스트의 컬렉션을 나타내는 리소스입니다. 리전 및 가용성 영역에서 호스트 그룹을 만들고 호스트를 추가합니다.

**호스트는** Azure 데이터 센터의 물리적 서버에 매핑된 리소스입니다. 호스트를 만들 때 실제 서버가 할당됩니다. 호스트가 호스트 그룹 내에 만들어집니다. 호스트에는 만들 수 있는 VM 크기를 설명하는 SKU가 있습니다. 각 호스트는 동일한 크기의 시리즈에서 온 경우 서로 다른 크기의 여러 VM을 호스팅할 수 있습니다.

Azure에서 VM을 만들 때 VM에 사용할 전용 호스트를 선택할 수 있습니다. 호스트에 배치되는 VM을 완전히 제어할 수 있습니다.


## <a name="high-availability-considerations"></a>고가용성 고려 사항 

고가용성의 경우 여러 호스트에 분산된 여러 VM을 배포해야 합니다(최소 2개). Azure 전용 호스트를 사용하면 오류 격리 경계를 형성하기 위해 인프라를 프로비전할 수 있는 몇 가지 옵션이 있습니다.

### <a name="use-availability-zones-for-fault-isolation"></a>오류 격리를 위해 가용성 영역 사용

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 호스트 그룹은 단일 가용성 영역에서 만들어집니다. 생성되면 모든 호스트가 해당 영역 내에 배치됩니다. 영역 간에 고가용성을 얻으려면 여러 호스트 그룹(영역당 하나)을 만들고 그에 따라 호스트를 분산시켜야 합니다.

가용성 영역에 호스트 그룹을 할당하는 경우 해당 호스트에서 만든 모든 VM을 동일한 영역에 만들어야 합니다.

### <a name="use-fault-domains-for-fault-isolation"></a>오류 격리를 위해 오류 도메인 사용

호스트는 특정 장애 도메인에서 만들 수 있습니다. 규모 집합 또는 가용성 집합의 VM과 마찬가지로 서로 다른 장애 도메인의 호스트는 데이터 센터의 다른 물리적 랙에 배치됩니다. 호스트 그룹을 만들 때 오류 도메인 수를 지정해야 합니다. 호스트 그룹 내에서 호스트를 만들 때 각 호스트에 대해 오류 도메인을 할당합니다. VM은 장애 도메인 할당이 필요하지 않습니다.

장애 도메인은 콜로케이션과 동일하지 않습니다. 두 호스트에 대해 동일한 오류 도메인을 갖는다고 해서 호스트가 서로 근접하다는 의미는 아닙니다.

장애 도메인은 호스트 그룹에 범위가 정해지습니다. 두 호스트 그룹 간의 선호도 방지에 대해 가정해서는 안 됩니다(가용성 영역이 다른 경우).

장애 도메인이 다른 호스트에 배포된 VM은 오류 격리 보호를 높이기 위해 여러 저장소 스탬프에 기본 관리 디스크 서비스를 갖습니다.

### <a name="using-availability-zones-and-fault-domains"></a>가용성 영역 및 장애 도메인 사용

두 기능을 함께 사용하여 더 많은 오류 격리를 수행할 수 있습니다. 이 경우 각 호스트 그룹에 대한 가용성 영역 및 장애 도메인 수를 지정하고, 그룹의 각 호스트에 장애 도메인을 할당하고, 각 VM에 가용성 영역을 할당합니다.

[여기에서 찾은](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) 리소스 관리자 샘플 템플릿은 영역 및 오류 도메인을 사용하여 리전의 복원력을 극대화하기 위해 호스트를 확산시킵니다.

## <a name="maintenance-control"></a>유지 관리 제어

가상 컴퓨터를 지원하는 인프라가 때때로 업데이트되어 안정성, 성능, 보안을 개선하고 새로운 기능을 시작할 수 있습니다. Azure 플랫폼은 가능하면 플랫폼 유지 관리의 영향을 최소화하려고 하지만 *유지 관리에 민감한* 워크로드를 가진 고객은 유지 관리를 위해 VM을 고정하거나 연결을 끊어야 하는 몇 초도 용납할 수 없습니다.

**유지 관리 제어는** 고객에게 전용 호스트에서 예약된 정기적인 플랫폼 업데이트를 건너뛰고 35일 롤링 기간 내에 선택한 시간에 적용할 수 있는 옵션을 제공합니다.

> [!NOTE]
>  유지 관리 제어는 현재 공개 미리 보기상태입니다. 자세한 내용은 ** [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) 또는 [PowerShell을](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)사용하여 유지 관리 제어를 사용한 제어 업데이트를**참조하십시오.

## <a name="capacity-considerations"></a>용량 고려 사항

전용 호스트가 프로비전되면 Azure는 이를 실제 서버에 할당합니다. 이렇게 하면 VM을 프로비전해야 할 때 용량의 가용성이 보장됩니다. Azure는 리전(또는 영역)의 전체 용량을 사용하여 호스트에 대한 실제 서버를 선택합니다. 또한 고객이 클러스터의 공간이 부족할 염려 없이 전용 호스트 공간을 늘릴 수 있을 것으로 기대할 수 있습니다.

## <a name="quotas"></a>할당량

리전당 전용 호스트에 대한 기본 할당량 제한은 3000vCPU입니다. 그러나 배포할 수 있는 호스트 의 수는 호스트에 사용되는 VM 크기 패밀리의 할당량에 의해 제한됩니다. 예를 들어 **종량제** 구독은 미국 동부 지역의 Dsv3 크기 계열에 사용할 수 있는 10vCPU의 할당량만 있을 수 있습니다. 이 경우 전용 호스트를 배포하려면 최소 64vCPU로 할당량 증가를 요청해야 합니다. 필요한 경우 오른쪽 상단 모서리에 있는 **증가 요청** 버튼을 선택하여 요청을 제출합니다.

![포털의 사용 및 할당량 페이지의 스크린샷](./media/virtual-machines-common-dedicated-hosts/quotas.png)

자세한 내용은 [가상 컴퓨터 vCPU 할당량을](/azure/virtual-machines/windows/quotas)참조하십시오.

무료 평가판 및 MSDN 구독Azure 전용 호스트에 대 한 할당량이 없습니다.

## <a name="pricing"></a>가격 책정

배포되는 VM 수에 관계없이 전용 호스트당 사용자에게 요금이 부과됩니다. 월별 명세서에 새로운 청구 가능한 리소스 유형의 호스트가 표시됩니다. 전용 호스트의 VM은 여전히 명세서에 표시되지만 가격은 0입니다.

호스트 가격은 VM 제품군, 유형(하드웨어 크기) 및 지역을 기준으로 설정됩니다. 호스트 가격은 호스트에서 지원되는 가장 큰 VM 크기를 기준으로 합니다.

소프트웨어 라이선스, 스토리지 및 네트워크 사용량은 호스트 및 VM과 별도로 청구됩니다. 청구 가능한 항목에는 변경되지 않습니다.

자세한 내용은 [Azure 전용 호스트 가격 책정을](https://aka.ms/ADHPricing)참조하십시오.

[또한 Azure 전용 호스트의 예약 인스턴스를](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)사용하면 비용을 절감할 수 있습니다.
 
## <a name="sizes-and-hardware-generations"></a>크기 및 하드웨어 세대

SKU는 호스트에 대해 정의되며 VM 크기 계열 및 형식을 나타냅니다. 동일한 크기의 계열에 속하는 경우 단일 호스트 내에서 여러 크기의 VM을 혼합할 수 있습니다. 

*유형은* 하드웨어 생성입니다. 동일한 VM 시리즈에 대한 다른 하드웨어 유형은 서로 다른 CPU 공급업체에서 온 것이며 CPU 세대와 코어 수가 다릅니다. 

크기와 하드웨어 유형은 지역에 따라 다릅니다. 자세한 내용은 호스트 [가격 페이지를](https://aka.ms/ADHPricing) 참조하십시오.


## <a name="host-life-cycle"></a>호스트 수명 주기


Azure는 호스트의 상태를 모니터링하고 관리합니다. 호스트를 쿼리할 때 다음 상태가 반환됩니다.

| 성능 상태   | 설명       |
|----------|----------------|
| 호스트 사용 가능     | 호스트에 알려진 문제가 없습니다.   |
| 조사 중인 호스트  | 우리는 우리가 찾고있는 호스트에 몇 가지 문제가 있습니다. 이 상태는 Azure에서 식별된 문제의 범위와 근본 원인을 식별하는 데 필요한 전환 상태입니다. 호스트에서 실행되는 가상 시스템이 영향을 받을 수 있습니다. |
| 호스트 보류 중인 거래 할당   | Azure는 호스트를 정상 상태로 복원할 수 없으며 이 호스트에서 가상 컴퓨터를 다시 배포하도록 요청할 수 없습니다. 활성화된 경우 `autoReplaceOnFailure` 가상 시스템이 정상 하드웨어로 *복구됩니다.* 그렇지 않으면 가상 시스템이 실패하려고 하는 호스트에서 실행중일 수 있습니다.|
| 호스트 할당 할당  | 모든 가상 시스템이 호스트에서 제거되었습니다. 하드웨어가 회전에서 제거되었기 때문에 이 호스트에 대한 요금이 더 이상 청구되지 않습니다.   |

