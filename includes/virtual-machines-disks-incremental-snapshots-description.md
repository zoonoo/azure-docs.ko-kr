---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/21/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b9b09012237d8f519322c927f8f2bbca1d3edef2
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107925228"
---
증분 스냅샷은 관리 디스크의 특정 시점 백업으로, 마지막 스냅샷 이후의 변경 내용으로만 구성됩니다. 증분 스냅샷에서 디스크를 복원하는 경우 시스템은 증분 스냅샷을 만들 때 디스크의 특정 시점 백업을 나타내는 전체 디스크를 재구성합니다. 관리 디스크 스냅샷에 대한 이 기능은 선택하지 않는 한 각 개별 스냅샷과 함께 전체 디스크를 저장할 필요가 없기 때문에 잠재적으로 비용 효율성을 높일 수 있습니다. 전체 스냅샷과 마찬가지로 증분 스냅샷을 사용하여 전체 관리 디스크 또는 전체 스냅샷을 만들 수 있습니다. 수행된 후 즉시 전체 스냅샷과 증분 스냅샷을 모두 사용할 수 있습니다. 즉, 스냅샷을 만든 후에는 기본 VHD를 즉시 읽고 디스크를 복원하는 데 사용할 수 있습니다.

증분 스냅샷과 전체 스냅샷 간에는 몇 가지 차이점이 있습니다. 증분 스냅샷은 디스크의 스토리지 유형과 관계없이 항상 표준 HDD 스토리지를 사용하는 반면, 전체 스냅샷은 프리미엄 SSD를 사용할 수 있습니다. Premium Storage에서 전체 스냅샷을 사용하여 VM 배포를 스케일 업하는 경우 [Shared Image Gallery](../articles/virtual-machines/shared-image-galleries.md)의 표준 스토리지에서 사용자 지정 이미지를 사용하는 것이 좋습니다. 이는 더 저렴한 비용으로 더 큰 규모를 달성하는 데 도움이 됩니다. 또한 증분 스냅샷은 ZRS([영역 중복 스토리지](../articles/storage/common/storage-redundancy.md))를 사용하여 더 높은 안정성을 제공할 수 있습니다. 선택한 지역에서 ZRS를 사용할 수 있는 경우 증분 스냅샷은 ZRS를 자동으로 사용합니다. 해당 지역에서 ZRS를 사용할 수 없는 경우 스냅샷은 기본적으로 LRS([로컬 중복 스토리지](../articles/storage/common/storage-redundancy.md))로 설정됩니다. 이 동작을 재정의하고 수동으로 선택할 수 있지만 권장하지 않습니다.

또한 증분 스냅샷은 관리 디스크에만 사용할 수 있는 차등 기능을 제공합니다. 이를 통해 동일한 관리 디스크의 두 증분 스냅샷 간의 변경 내용을 블록 수준까지 가져올 수 있습니다. 이 기능을 사용하여 지역 간에 스냅샷을 복사할 때 데이터 공간을 줄일 수 있습니다.  예를 들어, 첫 번째 증분 스냅샷을 다른 지역의 기본 BLOB으로 다운로드할 수 있습니다. 후속 증분 스냅샷의 경우 마지막 스냅샷 이후의 변경 내용만 기본 BLOB에 복사할 수 있습니다. 변경 내용을 복사한 후에는 다른 지역에 있는 디스크의 특정 시점 백업을 나타내는 기본 BLOB에서 스냅샷을 만들 수 있습니다. 기본 BLOB 또는 다른 지역에 있는 기본 BLOB의 스냅샷에서 디스크를 복원할 수 있습니다.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="지역 간에 복사된 증분 스냅샷을 나타내는 다이어그램입니다. 스냅샷은 최종적으로 각 스냅샷당 페이지 Blob을 형성할 때까지 다양한 API 호출을 수행합니다.":::

증분 스냅샷은 사용된 크기에 대해서만 요금이 청구됩니다. [Azure 사용량 보고서](../articles/cost-management-billing/understand/review-individual-bill.md)를 살펴보면 사용된 스냅샷의 크기를 확인할 수 있습니다. 예를 들어 스냅샷의 사용된 데이터 크기가 10GiB이면 **일별** 사용량 보고서에 10GiB/(31일) = 0.3226GiB가 소비량으로 표시됩니다.