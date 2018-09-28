---
title: 포함 파일
description: 포함 파일
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2ab2ac1e98e25da4065611d32fb50aa8ce809361
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060977"
---
## <a name="about-vhds"></a>VHD에 대하여

Azure에서 사용되는 VHD는 Azure의 표준 또는 프리미엄 저장소 계정에서 페이지 blob으로 저장되는 .vhd 파일입니다. 페이지 Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해하기](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/)를 참조하세요. 프리미엄 저장소에 대한 자세한 내용은 [고성능 프리미엄 저장소와 Azure VM](../articles/virtual-machines/windows/premium-storage.md)을 참조하세요.

Azure는 고정 디스크 VHD 형식을 지원합니다. 고정 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장되도록 파일 내에 선형적으로 논리적 디스크를 배치합니다. blob 끝의 작은 바닥글에서는 VHD의 속성을 설명합니다. 고정 형식은 대부분의 디스크에서 사용되지 않는 공간 범위가 크기 때문에 공간이 낭비되는 경우가 많습니다. 하지만 Azure에서는 .vhd 파일을 스파스 형식으로 저장하기 때문에 고정 및 동적 디스크의 장점을 동시에 얻을 수 있습니다. 자세한 내용은 [가상 하드 디스크 시작](https://technet.microsoft.com/library/dd979539.aspx)을 참조하세요.

사용자가 Azure 저장소로 업로드하거나 복사한 .vhd 파일을 제외하고, Azure에서 디스크 또는 이미지를 만드는 데 원본으로 사용하려는 모든 VHD 파일은 읽기 전용입니다(읽기-쓰기 또는 읽기 전용일 수 있음). 사용자가 디스크나 이미지를 만들 때, Azure는 원본 .vhd 파일의 복사본을 만듭니다. 이러한 복사본들은 읽기 전용이거나 사용자가 VHD를 사용하는 방법에 따라 읽기 또는 쓰기 모두를 할 수 있습니다.

사용자가 이미지에서 가상 컴퓨터를 만들 때, Azure는 원본 .vhd 파일의 복사본인, 가상 머신에 대한 디스크를 만듭니다. 실수로 삭제되지 않도록 보호하기 위해, Azure는 이미지, 운영 체제 디스크 또는 데이터 디스크를 만드는 데 사용되는 모든 원본 .vhd 파일에 임대를 설정합니다.

원본 .vhd 파일을 삭제하기 전에, 디스크 또는 이미지를 삭제하여 임대를 제거해야 합니다. 운영 체제 디스크로 가상 머신이 사용중인 .vhd 파일을 삭제하려면, 사용자는 가상 머신 및 모든 연결된 디스크를 삭제함으로써 가상 머신, 운영 체제 디스크 및 원본 .vhd 파일을 한번에 삭제할 수 있습니다. 그러나 데이터 디스크의 원본인 .vhd 파일을 삭제하려면 설정된 순서대로 몇 가지 단계를 수행해야 합니다. 먼저 가상 머신에서 디스크를 분리한 다음 디스크를 삭제하고 .vhd 파일을 삭제합니다.

> [!WARNING]
> 저장소에서 원본 .vhd 파일을 삭제하거나, 사용자의 저장소 계정을 삭제한 경우, Microsoft는 해당 데이터를 복구할 수 없습니다.

## <a name="types-of-disks"></a>디스크 유형

Azure 디스크는 99.999% 가용성을 위해 설계되었습니다. Azure 디스크는 업계 최고의 0% 연간 실패율(Annualized Failure Rate)로 엔터프라이즈급 내구성을 일관되게 제공합니다.

디스크(프리미엄 SSD 디스크, 표준 SSD(미리 보기) 및 표준 HDD Storage)를 만들 때 선택할 수 있는 저장소에는 세 가지 성능 계층이 있습니다. 또한 두 가지 유형의 디스크(비관리 및 관리)가 있습니다.

### <a name="standard-hdd-disks"></a>표준 HDD 디스크

표준 HDD 디스크는 HDD에 의해 지원되며, 비용 효율적인 저장소를 제공합니다. 표준 HDD 저장소는 하나의 데이터 센터에 로컬로 복제되거나 기본 및 보조 데이터 센터와 함께 지역 중복 저장소가 될 수 있습니다. 저장소 복제에 대한 자세한 내용은 [Azure Storage 복제](../articles/storage/common/storage-redundancy.md)를 참조하세요. 

표준 HDD 디스크를 사용하는 방법에 대한 자세한 내용은 [표준 저장소 및 디스크](../articles/virtual-machines/windows/standard-storage.md)를 참조하세요.

### <a name="standard-ssd-disks"></a>표준 SSD 디스크

표준 SSD 디스크는 표준 HDD 디스크와 같은 종류의 워크로드를 처리하도록 설계되었지만 HDD보다 더욱 일관된 성능과 안정성을 제공합니다. 표준 SSD 디스크는 디스크에 높은 IOPS가 필요하지 않은 웹 서버와 같은 응용 프로그램에 가장 적합한 비용 효율적인 솔루션을 형성하도록 프리미엄 SSD 디스크 및 표준 HDD 디스크의 요소를 결합합니다. 사용할 수 있는 경우 표준 SSD 디스크는 대부분의 워크로드에 권장되는 배포 옵션입니다. 표준 SSD 디스크는 모든 지역에서 Managed Disks로 사용할 수 있으나 현재 LRS(로컬 중복 저장소) 복원 유형으로만 사용할 수 있습니다.

### <a name="premium-ssd-disks"></a>프리미엄 SSD 디스크

프리미엄 SSD 디스크는 SSD에 의해 지원되며 I/O 사용량이 많은 작업을 실행하는 VM을 위해 고성능의 낮은 대기 시간의 디스크를 지원합니다. 일반적으로 시리즈 이름에 “s”가 포함되는 크기에 프리미엄 SSD 디스크를 사용할 수 있습니다. 예를 들어 Dv3 및 Dsv3 시리즈가 있다면 Dsv3 시리즈에서 프리미엄 SSD 디스크를 사용할 수 있습니다.  자세한 내용은 [Premium Storage](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.

### <a name="unmanaged-disks"></a>관리되지 않는 디스크

관리되지 않는 디스크는 VM에서 사용되는 전통적인 유형의 디스크입니다. 이러한 디스크를 통해 자신만의 저장소 계정을 만들어 디스크를 만들 때 해당 저장소 계정을 지정합니다. 동일한 저장소 계정에 너무 많은 디스크를 배치하지 않도록 합니다. 그 이유는 저장소 계정의 [확장성 목표](../articles/storage/common/storage-scalability-targets.md)(예: 20,000개의 IOPS)를 초과하여, VM이 제한됩니다. 관리되지 않는 디스크를 사용하는 경우 VM에서 최상의 성능을 얻을 수 있도록 하나 이상의 저장소 계정 사용을 극대화하는 방법을 파악해야 합니다.

### <a name="managed-disks"></a>관리 디스크

Managed Disks는 백그라운드에서 저장소 계정 만들기/관리 작업을 처리하기 때문에 저장소 계정의 확장성 제한에 걱정할 필요가 없습니다. 디스크 크기와 성능 계층(Standard/Premium)을 지정하면, Azure가 디스크를 만들고 관리합니다. 디스크를 추가하거나 VM을 확장하고 축소하면 사용 중인 저장소에 대해 걱정할 필요가 없습니다.

또한 Azure 지역당 하나의 저장소 계정에서 사용자 지정 이미지를 관리하고 동일한 구독에서 수백 개의 VM을 만드는 데 사용할 수도 있습니다. 관리 디스크에 대한 자세한 내용은 [Managed Disks 개요](../articles/virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

새 VM에 Azure Managed Disks를 사용하고 이전의 관리되지 않은 디스크를 관리 디스크로 변환하여 Managed Disks에서 사용할 수 있는 많은 기능을 활용하는 것이 좋습니다.

### <a name="disk-comparison"></a>디스크 비교

다음 표는 용도에 맞게 관리되지 않는 디스크를 사용할지 관리 디스크를 사용할지 결정할 수 있도록 표준 HDD, 표준 SSD 및 프리미엄 SSD를 비교한 내용을 제공합니다. 별표로 표시되는 크기는 현재 미리 보기로 제공됩니다.

|    | Azure Premium Disk |Azure 표준 SSD 디스크 | Azure 표준 HDD 디스크
|--- | ------------------ | ------------------------------- | -----------------------
| 디스크 유형 | 반도체 드라이브(SSD) | 반도체 드라이브(SSD) | 하드 디스크 드라이브 (HDD)  
| 개요  | IO 사용량이 많은 작업을 실행하거나 중요 업무용 프로덕션 환경을 호스팅하는 VM을 위해 SSD 기반의 고성능, 낮은 대기 시간 디스크 지원 |HDD보다 더욱 일관된 성능 및 안정성 낮은 IOPS 작업용으로 최적화됨| 드문 액세스에 대한 HDD 기반 비용 효율적인 디스크
| 시나리오  | 프로덕션 및 성능이 중요한 워크로드 |웹 서버, 조금 사용되는 엔터프라이즈 응용 프로그램 및 개발/테스트| 백업, 중요하지 않음, 드문 액세스
| 디스크 크기 | P4: 32GiB(Managed Disks에만 해당)<br>P6: 64GiB(Managed Disks에만 해당)<br>P10: 128GiB<br>P15: 256GiB(Managed Disks에만 해당)<br>P20: 512GiB<br>P30: 1024GiB<br>P40: 2048GiB<br>P50: 4,095GiB<br>P60: 8,192GiB *(8TiB)<br>P70: 16,384GiB *(16TiB)<br>P80: 32,767GiB *(32TiB) |Managed Disks에만 해당:<br>E10: 128GiB<br>E15: 256GiB<br>E20: 512GiB<br>E30: 1024GiB<br>E40: 2048GiB<br>E50: 4095GiB<br>E60: 8,192GiB *(8TiB)<br>E70: 16,384GiB *(16TiB)<br> E80: 32,767GiB *(32TiB) | 관리되지 않는 디스크: 1GiB – 4TiB(4095GiB) <br><br>관리 디스크:<br> S4: 32GiB <br>S6: 64GiB <br>S10: 128GiB <br>S15: 256GiB <br>S20: 512GiB <br>S30: 1024GiB <br>S40: 2048GiB<br>S50: 4095GiB<br>S60: 8,192GiB *(8TiB)<br>S70: 16,384GiB *(16TiB)<br>S80: 32,384GiB *(32TiB)
| 디스크당 최대 처리량 | P4: 25MiB/초<br> P6: 50MiB/초<br> P10: 100MiB/초<br> P15: 200MiB/초<br> P20: 150MiB/초<br> P30: 200MiB/초<br> P40~P50: 250MiB/초<br> P60: 480MiB/초<br> P70~P80: 750MiB/초 | E10~E50: 최대 60MiB/초<br> E60: 최대 300MiB/초*<br> E70~E80: 500MiB/초*| S4~S50: 최대 o 60MiB/초<br> S60: 최대 300MiB/초*<br> S70~S80: 최대 500MiB/초*
| 디스크당 최대 IOPS | P4: 120IOPS<br> P6: 240IOPS<br> P10: 500IOPS<br> P15: 1100IOPS<br> P20: 2300IOPS<br> P30: 500IOPS<br> P40~P50: 7500IOPS<br> P60: 12,500IOPS *<br> P70: 15,000IOPS *<br> P80: 20,000IOPS * | E10~E50: 최대 500IOPS<br> E60: 최대 1300IOPS *<br> E70~E80: 최대 2000IOPS * | S4~S50: 최대 500IOPS<br> S60: 최대 1300IOPS *<br> S70~S80: 최대 2000IOPS *