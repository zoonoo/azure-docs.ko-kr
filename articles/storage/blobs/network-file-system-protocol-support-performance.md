---
title: Azure Blob storage (미리 보기)의 NFS 3.0 성능 고려 사항 | Microsoft Docs
description: 이 문서의 권장 사항을 사용 하 여 NFS (네트워크 파일 시스템) 3.0 저장소 요청의 성능을 최적화 합니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: f8a780afba1f5703fbe457e113ed1b455f1e9b64
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745416"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>NFS (네트워크 파일 시스템) 3.0 Azure Blob storage의 성능 고려 사항 (미리 보기)

이제 Blob storage는 NFS (네트워크 파일 시스템) 3.0 프로토콜을 지원 합니다. 이 문서에는 저장소 요청의 성능을 최적화 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다. Azure Blob Storage에서 NFS 3.0 지원에 대 한 자세한 내용은 [Azure Blob Storage에서 nfs (네트워크 파일 시스템) 3.0 프로토콜 지원 (미리 보기)](network-file-system-protocol-support.md)을 참조 하세요.

> [!NOTE]
> NFS 3.0 Azure Blob storage의 프로토콜 지원은 공개 미리 보기 상태입니다. 오스트레일리아 동부, 대한민국 중부 및 미국 서 부 지역에서 표준 계층 성능을 갖춘 GPV2 storage 계정을 지원 합니다. 미리 보기는 모든 공용 지역에서 프리미엄 성능 계층을 포함 하는 블록 blob도 지원 합니다.

## <a name="add-clients-to-increase-throughput"></a>클라이언트를 추가 하 여 처리량 늘리기 

Azure Blob Storage 최대 저장소 계정 송신 및 수신 제한에 도달할 때까지 선형으로 확장 됩니다. 따라서 응용 프로그램은 더 많은 클라이언트를 사용 하 여 더 높은 처리량을 달성할 수 있습니다.  저장소 계정 송신 및 수신 제한을 보려면 [standard storage 계정에 대 한 확장성 및 성능 목표](../common/scalability-targets-standard-account.md)를 참조 하세요.

다음 차트에서는 더 많은 클라이언트를 추가할 때 대역폭을 늘리는 방법을 보여 줍니다. 이 차트에서 클라이언트는 VM (가상 컴퓨터) 이며 계정은 표준 성능 계층을 사용 합니다. 

> [!div class="mx-imgBorder"]
> ![표준 성능](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

다음 차트는 프리미엄 성능 계층을 사용 하는 계정에 적용 될 때 이와 동일한 효과를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![표준 성능](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>작은 규모의 응용 프로그램에 프리미엄 성능 계층 사용

클라이언트를 더 추가 하 여 모든 응용 프로그램을 확장할 수 있는 것은 아닙니다. 이러한 응용 프로그램의 경우 [Azure premium block blob storage 계정은](storage-blob-create-account-block-blob.md) 일관 된 낮은 대기 시간 및 높은 트랜잭션 속도를 제공 합니다. 프리미엄 블록 blob 저장소 계정은 스레드 및 클라이언트 수를 줄여 최대 대역폭에 도달할 수 있습니다. 예를 들어 단일 클라이언트를 사용 하는 경우 프리미엄 블록 blob storage 계정은 표준 performance 범용 v2 저장소 계정에서 사용 되는 것과 동일한 설정에 비해 **2.3 x** 대역폭을 달성할 수 있습니다. 

다음 차트의 각 막대는 프리미엄 및 표준 성능 저장소 계정 간에 달성 된 대역폭의 차이를 보여 줍니다. 클라이언트 수가 늘어나면 이러한 차이가 줄어듭니다.  

> [!div class="mx-imgBorder"]
> ![상대적 성능](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-date"></a>자주 덮어쓰는 일을 방지 합니다.

새 쓰기 작업 보다는 덮어쓰기 작업을 완료 하는 데 시간이 더 오래 걸립니다. NFS 덮어쓰기 작업, 특히 부분 내부 파일 편집은 몇 가지 기본 blob 작업 (읽기, 수정 및 쓰기 작업)의 조합 이기 때문입니다. 그러므로 자주 사용 하는 편집이 필요한 응용 프로그램은 NFS 사용 blob storage 계정에 적합 하지 않습니다. 

## <a name="other-best-practice-recommendations"></a>기타 모범 사례 권장 사항 

- 네트워크 대역폭이 충분 한 Vm을 사용 합니다.

- 워크 로드에서 허용 하는 경우 여러 탑재 위치를 사용 합니다.

- 최대한 많은 스레드를 사용 합니다.

- 긴 블록 크기를 사용 합니다.

- 저장소 계정과 동일한 지역에 있는 클라이언트에서 저장소 요청을 만듭니다. 이렇게 하면 네트워크 대기 시간을 향상 시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Blob Storage에서 NFS 3.0 지원에 대 한 자세한 내용은 [Azure Blob Storage에서 nfs (네트워크 파일 시스템) 3.0 프로토콜 지원 (미리 보기)](network-file-system-protocol-support.md)을 참조 하세요.

- 시작 하려면 [NFS (네트워크 파일 시스템) 3.0 프로토콜을 사용 하 여 Blob 저장소 탑재 (미리 보기)](network-file-system-protocol-support-how-to.md)를 참조 하세요.
