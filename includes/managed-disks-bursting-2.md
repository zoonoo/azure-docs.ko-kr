---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: cc0dd5e76227c0814659b74afc2ac46c8ca4de73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102833"
---
## <a name="common-scenarios"></a>일반적인 시나리오
다음 시나리오는 버스트에서 크게 이점을 누릴 수 있습니다.
- **부팅 시간 개선** – 버스트를 사용 하면 인스턴스가 매우 빠른 속도로 부팅 됩니다. 예를 들어 premium 사용 Vm에 대 한 기본 OS 디스크는 P4 디스크 이며, 최대 120 IOPS 및 250MB/s의 프로 비전 된 성능입니다. 버스트를 사용 하는 경우 P4는 최대 3500 IOPS 및 170 m b/s로 이동 하 여 부팅 시간을 6X으로 가속화할 수 있습니다.
- **일괄 작업 처리** – 일부 응용 프로그램의 작업은 기본적으로 순환 되며 대부분의 시간에 대 한 기준 성능이 필요 하며 짧은 시간 동안 성능이 더 높습니다. 이에 대 한 예는 매일 적은 양의 디스크 트래픽이 필요한 트랜잭션을 처리 하는 회계 프로그램입니다. 그런 다음이 월의 끝에는 훨씬 더 많은 양의 디스크 트래픽이 필요한 보고서를 조정 합니다.
- **트래픽 급증에 대 한 준비** – 웹 서버 및 해당 응용 프로그램은 언제 든 지 트래픽이 발생 될 수 있습니다. 웹 서버가 버스트를 사용 하는 Vm 또는 디스크에 의해 지원 되는 경우 서버는 트래픽 급증을 처리 하는 데 더 적합 합니다. 

## <a name="bursting-flow"></a>버스트 흐름
버스트 크레딧 시스템은 가상 머신 수준과 디스크 수준에서 동일한 방식으로 적용 됩니다. 리소스 (VM 또는 디스크)는 완전히 보충 크레딧을 시작 합니다. 이러한 크레딧을 통해 최대 버스트 속도로 30 분 동안 버스트를 수행할 수 있습니다. 버스트 크레딧은 리소스가 성능 디스크 저장소 제한에서 실행 되는 경우 누적 됩니다. 리소스가 성능 제한을 초과 하 여 사용 하는 모든 IOPS 및 MB/s에 대해 크레딧을 축적 하기 시작 합니다. 리소스에서 버스트에 사용할 크레딧을 계산 하 고 워크 로드에 추가 성능이 필요한 경우 리소스는 해당 크레딧을 사용 하 여 성능 제한을 초과 하 여 수요를 충족 하는 데 필요한 디스크 IO 성능을 제공할 수 있습니다.



![버스트 버킷 다이어그램](media/managed-disks-bursting/bucket-diagram.jpg)

30 분 버스트를 사용 하는 방법에 대 한 최대입니다. 30 분 동안이를 하루 종일 또는 산발적으로 사용할 수 있습니다. 제품이 배포 되 면 전체 크레딧을 준비 하 고 크레딧을 depletes 때 전체 크레딧을 완전히 보충 하는 데 하루 미만이 걸립니다. 사용자의 재량에 따라 버스트 크레딧을 축적 하 고 소비할 수 있으며 30 분 버킷을 다시 버스트에 다시 사용할 필요가 없습니다. 버스트 누적에 대해 기억해 야 할 한 가지 사항은 리소스는 사용 되지 않는 IOPS를 기준으로 하 고 성능 금액 보다 낮은 m/s를 기준으로 하기 때문입니다. 즉, 더 높은 기준 성능 제품은 낮은 기준 제품을 실행 하는 것 보다 버스트 속도를 더 빠르게 계산 합니다. 예를 들어, 활동이 없는 P1 디스크 유휴 상태는 초당 120 IOPS를 계산 하는 반면, P20 디스크는 초당 2300 IOPS를 발생 하 고 작업을 하지 않고 유휴 상태를 계산 합니다.

## <a name="bursting-states"></a>버스트 상태
버스트를 사용 하 여 리소스를 사용할 수 있는 세 가지 상태가 있습니다.
- **발생** – 리소스의 IO 트래픽이 성능 목표 보다 더 작은 값을 사용 합니다. IOPS의 누적 버스트 크레딧 및 m b/초는 서로 별도로 수행 됩니다. 리소스는 발생 IOPS 크레딧을 소비 하 고, m b/s 크레딧을 사용할 수 있으며, 그 반대의 경우도 가능 합니다.
- **버스트** – 리소스의 트래픽이 성능 목표를 초과 하 여 사용 하 고 있습니다. 버스트 트래픽은 IOPS 또는 대역폭의 크레딧을 독립적으로 사용 합니다.
- **상수** – 리소스의 트래픽은 성능 목표에 정확히 맞습니다.

## <a name="examples-of-bursting"></a>버스트의 예
다음 예에서는 다양 한 가상 컴퓨터 및 디스크 조합에서 버스트가 작동 하는 방법을 보여 줍니다. 예제를 쉽게 수행할 수 있도록 MB/s에 집중 하지만 동일한 논리가 IOPS에 독립적으로 적용 됩니다.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>안정적인 디스크를 사용 하는 비 삼로 안정화 된 가상 머신
**VM 및 디스크 조합:** 
- Standard_D8as_v4 
    - 캐시 되지 않은 m b/초: 192
- P4 OS 디스크
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170 
- 2 P10 데이터 디스크 
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170

 VM이 부팅 되 면 OS 디스크에서 데이터를 검색 합니다. OS 디스크는 시작 되는 VM의 일부 이기 때문에 OS 디스크는 버스트 크레딧을 차지 합니다. 이러한 크레딧을 통해 OS 디스크는 아래에 표시 된 대로 170 m b/초 후에 시작을 버스트 합니다.

![비 버스트 vm 버스트 디스크 시작](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

부팅이 완료 되 면 응용 프로그램은 VM에서 실행 되 고 중요 하지 않은 작업을 포함 합니다. 이 워크 로드에는 모든 디스크에서 균등 하 게 분산 되는 15 m b/S가 필요 합니다.

![비 버스트 vm 버스트 디스크 유휴 상태](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

그런 다음 응용 프로그램에서 192 m b/s가 필요한 일괄 처리 된 작업을 처리 해야 합니다. 2 m b/s는 OS 디스크에서 사용 되며 나머지는 데이터 디스크 간에 균등 하 게 분할 됩니다.

![비 버스트 vm 버스트 디스크 버스트](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>비 다른 안정적인 디스크로 안정화 된 안정적인 가상 머신
**VM 및 디스크 조합:** 
- Standard_L8s_v2 
    - 캐시 되지 않은 m b/초: 160
    - 최대 버스트 MB/s: 1280
- P50 OS 디스크
    - 프로 비전 된 MB/s: 250 
- 2 P10 데이터 디스크 
    - 프로 비전 된 MB/s: 250

 초기 부팅 후 응용 프로그램은 VM에서 실행 되 고 중요 하지 않은 워크 로드를 포함 합니다. 이 워크 로드에는 모든 디스크에 균등 하 게 분산 되는 30mb/s가 필요 합니다. ![ 버스트 vm 비 버스트 디스크 유휴 상태](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

그런 다음 응용 프로그램에서 600 m b/s가 필요한 일괄 처리 된 작업을 처리 해야 합니다. 이 수요를 충족 하는 Standard_L8s_v2 버스트 된 다음 디스크에 대 한 요청은 P50 디스크에 고르게 분산 됩니다.

![버스트 vm 비 버스트 디스크 버스트](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>안정적인 디스크를 사용 하는 안정적인 가상 머신
**VM 및 디스크 조합:** 
- Standard_L8s_v2 
    - 캐시 되지 않은 m b/초: 160
    - 최대 버스트 MB/s: 1280
- P4 OS 디스크
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170 
- 2 P4 데이터 디스크 
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170 

VM이 부팅 되 면 OS 디스크에서 1280 m b/s의 버스트 제한을 요청 하기 위해 버스트 하 고 OS 디스크는 170 m b/초 버스트 성능으로 응답 합니다.

![버스트 vm 버스트 디스크 시작](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

그런 다음 부팅이 완료 되 면 응용 프로그램이 VM에서 실행 됩니다. 응용 프로그램에는 모든 디스크에 균등 하 게 분산 되는 15 m b/초를 필요로 하는 중요 하지 않은 워크 로드가 있습니다.

![버스트 vm 버스트 디스크 유휴 상태](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

그런 다음 응용 프로그램에서 360 m b/s가 필요한 일괄 처리 된 작업을 처리 해야 합니다. 이 수요와 요청을 충족 하는 Standard_L8s_v2 버스트 됩니다. OS 디스크에는 20mb/s만 필요 합니다. 나머지 340 m b/s는 버스트 P4 데이터 디스크에 의해 처리 됩니다.  

![버스트 vm 버스트 디스크 버스트](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
