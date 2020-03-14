---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299188"
---
증분 스냅숏은 관리 디스크의 지정 시간 백업으로, 마지막 스냅숏 이후의 모든 변경 내용 으로만 구성 됩니다. 증분 스냅숏을 다운로드 하거나 사용 하지 않으면 전체 VHD가 사용 됩니다. 관리 디스크 스냅숏에 대 한이 새로운 기능을 사용 하면를 선택 하지 않는 한 개별 스냅숏이 있는 전체 디스크를 저장할 필요가 없기 때문에 더욱 비용 효율적일 수 있습니다. 일반 스냅숏과 마찬가지로 증분 스냅숏은 전체 관리 디스크를 만들거나 일반 스냅숏을 만드는 데 사용할 수 있습니다.

증분 스냅숏과 일반 스냅숏 간에는 몇 가지 차이점이 있습니다. 증분 스냅숏은 디스크의 저장소 유형과 관계 없이 항상 표준 Hdd 저장소를 사용 하는 반면, 일반 스냅숏은 프리미엄 Ssd를 사용할 수 있습니다. Premium Storage에서 정기적인 스냅숏을 사용 하 여 VM 배포를 강화 하는 경우 [공유 이미지 갤러리](../articles/virtual-machines/linux/shared-image-galleries.md)의 standard Storage에서 사용자 지정 이미지를 사용 하는 것이 좋습니다. 이는 저렴 한 비용으로 더 광범위 한 확장을 실현 하는 데 도움이 됩니다. 또한 증분 스냅숏은 ZRS ( [영역 중복 저장소](../articles/storage/common/storage-redundancy-zrs.md) )를 사용 하 여 더 높은 안정성을 제공할 수 있습니다. 선택한 지역에서 ZRS을 사용할 수 있는 경우 증분 스냅숏은 ZRS을 자동으로 사용 합니다. ZRS를 지역에서 사용할 수 없는 경우 스냅숏은 기본적으로 LRS ( [로컬 중복 저장소](../articles/storage/common/storage-redundancy-lrs.md) )로 사용 됩니다. 이 동작을 재정의 하 고 하나를 수동으로 선택할 수 있지만 권장 되지는 않습니다.

또한 증분 스냅숏은 관리 디스크에만 사용할 수 있는 차등 기능을 제공 합니다. 이를 통해 동일한 관리 디스크의 두 증분 스냅숏 간의 변경 내용을 블록 수준까지 가져올 수 있습니다. 이 기능을 사용 하 여 지역 간에 스냅숏을 복사할 때 데이터 공간을 줄일 수 있습니다.
