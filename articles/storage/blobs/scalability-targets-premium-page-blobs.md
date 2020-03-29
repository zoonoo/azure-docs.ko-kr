---
title: 프리미엄 페이지 Blob 저장소 계정에 대한 확장성 목표
titleSuffix: Azure Storage
description: 프리미엄 성능 페이지 Blob 저장소 계정은 읽기/쓰기 작업에 최적화되어 있습니다. 이러한 유형의 저장소 계정은 Azure 가상 시스템에 대해 관리되지 않는 디스크를 백업합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756252"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>프리미엄 페이지 Blob 저장소 계정에 대한 확장성 및 성능 목표

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>프리미엄 페이지 Blob 계정의 대상 확장

프리미엄 성능 페이지 Blob 저장소 계정은 읽기/쓰기 작업에 최적화되어 있습니다. 이러한 유형의 저장소 계정은 Azure 가상 시스템에 대해 관리되지 않는 디스크를 백업합니다.

> [!NOTE]
> 가능하면 Azure 가상 시스템(VM)을 사용하여 관리되는 디스크를 사용하는 것이 좋습니다. 관리 디스크에 대한 자세한 내용은 [Windows VM에 대한 Azure 디스크 저장소 개요를](../../virtual-machines/windows/managed-disks-overview.md)참조하십시오.

프리미엄 페이지 Blob 저장소 계정에는 다음과 같은 확장성 목표가 있습니다.

| 총 계정 용량                            | 로컬 중복 스토리지 계정의 총 대역폭                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 디스크 용량: 4TB(개별 디스크) / 35TB(모든 디스크의 누적 합계) <br>스냅샷 용량: 10TB | 인바운드<sup>1</sup> + 아웃바운드<sup>2</sup>에 대해 초당 최대 50기가비트 |

<sup>1</sup> 스토리지 계정으로 전송되는 모든 데이터(요청)

<sup>2</sup> 스토리지 계정에서 수신하는 모든 데이터(응답)

프리미엄 페이지 Blob 계정은 프리미엄 성능을 위해 구성된 범용 계정입니다. 범용 v2 저장소 계정을 권장합니다.

관리되지 않는 디스크에 프리미엄 페이지 Blob 저장소 계정을 사용하고 있고 응용 프로그램이 단일 저장소 계정의 확장성 목표를 초과하는 경우 Microsoft는 관리되는 디스크로 마이그레이션하는 것이 좋습니다. 관리되는 디스크에 대한 자세한 내용은 [Windows VM에 대한 Azure 디스크 저장소 개요](../../virtual-machines/windows/managed-disks-overview.md) 또는 Linux [VM에 대한 Azure 디스크 저장소 개요를](../../virtual-machines/linux/managed-disks-overview.md)참조하십시오.

관리 되는 디스크로 마이그레이션할 수 없는 경우 여러 저장소 계정을 사용 하 여 응용 프로그램을 빌드 하 고 해당 저장소 계정 간에 데이터를 분할 합니다. 예를 들어 51TB 디스크를 여러 VM에 연결하려는 경우에는 두 개의 스토리지 계정에 분산합니다. 단일 Premium Storage 계정의 한도는 35TB입니다. 단일 프리미엄 성능 저장소 계정에 프로비저닝된 디스크가 35TB를 초과하지 않는지 확인합니다.

## <a name="see-also"></a>참조

- [표준 스토리지 계정의 확장성 및 성능 목표](../common/scalability-targets-standard-account.md)
- [프리미엄 블록 Blob 저장소 계정에 대한 확장성 목표](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure 구독 제한 및 할당량](../../azure-resource-manager/management/azure-subscription-service-limits.md)
