---
title: 프리미엄 페이지 blob storage 계정에 대 한 확장성 목표
titleSuffix: Azure Storage
description: 프리미엄 성능 페이지 blob storage 계정은 읽기/쓰기 작업에 최적화 되어 있습니다. 이 유형의 저장소 계정은 Azure virtual machines에 대해 관리 되지 않는 디스크를 백업 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1542204a2ac779a711407ab1a36c6f8ab9669510
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483532"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>프리미엄 페이지 blob storage 계정에 대 한 확장성 및 성능 목표

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>프리미엄 페이지 blob 계정에 대 한 크기 조정 대상

프리미엄 성능 페이지 blob 저장소 계정은 읽기/쓰기 작업에 최적화 되어 있습니다. 이 유형의 저장소 계정은 Azure virtual machines에 대해 관리 되지 않는 디스크를 백업 합니다.

> [!NOTE]
> 가능 하면 Azure Vm (가상 머신)에서 managed disks를 사용 하는 것이 좋습니다. 관리 디스크에 대 한 자세한 내용은 [Windows vm에 대 한 Azure 디스크 저장소 개요](../../virtual-machines/windows/managed-disks-overview.md)를 참조 하세요.

프리미엄 페이지 blob storage 계정의 확장성 목표는 다음과 같습니다.

| 총 계정 용량                            | 로컬 중복 스토리지 계정의 총 대역폭                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 디스크 용량: 35TB <br>스냅샷 용량: 10TB | 인바운드<sup>1</sup> + 아웃바운드<sup>2</sup>에 대해 초당 최대 50기가비트 |

<sup>1</sup> 스토리지 계정으로 전송되는 모든 데이터(요청)

<sup>2</sup> 스토리지 계정에서 수신하는 모든 데이터(응답)

프리미엄 페이지 blob 계정은 프리미엄 성능을 위해 구성 된 범용 계정입니다. 범용 v2 저장소 계정을 권장 합니다.

관리 되지 않는 디스크에 프리미엄 페이지 blob storage 계정을 사용 하 고 응용 프로그램이 단일 저장소 계정의 확장성 목표를 초과 하는 경우 Microsoft는 관리 디스크로 마이그레이션하는 것이 좋습니다. 관리 디스크에 대 한 자세한 내용은 [Windows vm에 대 한 Azure 디스크 저장소 개요](../../virtual-machines/windows/managed-disks-overview.md) 또는 [Linux vm에 대 한 Azure 디스크 저장소 개요](../../virtual-machines/linux/managed-disks-overview.md)를 참조 하세요.

관리 디스크로 마이그레이션할 수 없는 경우 여러 저장소 계정을 사용 하 고 해당 저장소 계정에서 데이터를 분할 하는 응용 프로그램을 빌드합니다. 예를 들어 51TB 디스크를 여러 VM에 연결하려는 경우에는 두 개의 스토리지 계정에 분산합니다. 단일 Premium Storage 계정의 한도는 35TB입니다. 단일 프리미엄 성능 저장소 계정에 35 TB 이상의 프로 비전 된 디스크가 없어야 합니다.

## <a name="see-also"></a>참고 항목

- [Standard storage 계정에 대 한 확장성 및 성능 목표](../common/scalability-targets-standard-account.md)
- [프리미엄 블록 blob storage 계정에 대 한 확장성 목표](../blobs/scalability-targets-premium-block-blobs.md)
