---
title: Azure Blob 백업에 대 한 지원 매트릭스
description: Azure Blob (미리 보기)를 백업할 때 지원 설정 및 제한 사항에 대 한 요약을 제공 합니다.
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561902"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Azure Blob 백업에 대 한 지원 매트릭스 (미리 보기)

이 문서에서는 지역 가용성, 지원 되는 시나리오 및 blob의 운영 백업에 대 한 제한 사항을 요약 합니다.

## <a name="supported-regions"></a>지원되는 지역

Blob에 대 한 운영 백업은 현재 오스트레일리아 중부, 오스트레일리아 동부, 브라질 남부, 캐나다 중부, 인도 중부, 미국 중부, 동아시아, 미국 동부, 미국 동부 2, 독일 중서부, 일본 동부, 일본 서 부, 대한민국 중부, 한국 남부, 북부, 미국 동부, 미국 동부, 남부 동아시아, 스위스 북부, 아랍에미리트 북부, 영국 남부, 영국 서부, 미국 서 부, 미국에서 사용할 수 있습니다. , 미국 서 부, 미국 서 부 2

## <a name="limitations"></a>제한 사항

Blob의 운영 백업은 blob 지정 시간 복원, blob 버전 관리, blob에 대 한 일시 삭제, blob에 대 한 변경 피드 및 로컬 백업 솔루션을 제공 하는 잠금 삭제를 사용 합니다. 따라서 이러한 기능에 적용 되는 제한 사항은 운영 백업에도 적용 됩니다.

**지원 되는 시나리오:** 운영 백업은 표준 용도의 v2 저장소 계정에서 블록 blob을 지원 합니다. 따라서 ADLS Gen2 계정은 지원 되지 않습니다. 또한 저장소 계정에 있는 모든 페이지 blob, 추가 blob 및 프리미엄 blob은 복원 되지 않으며 블록 blob만 복원 됩니다.

**기타 제한 사항:**

- 보존 기간 동안 컨테이너를 삭제 한 경우 해당 컨테이너는 지정 시간 복원 작업으로 복원 되지 않습니다. 삭제 된 컨테이너에 blob을 포함 하는 blob 범위를 복원 하려고 하면 지정 시간 복원 작업이 실패 합니다. 컨테이너를 삭제 하지 못하도록 보호 하는 방법에 대 한 자세한 내용은 [컨테이너의 일시 삭제 (미리 보기)](../storage/blobs/soft-delete-container-overview.md)를 참조 하세요.
- Blob이 현재 순간과 복원 지점 사이에서 핫 및 쿨 계층 사이를 이동 하면 blob이 이전 계층으로 복원 됩니다. 보관 계층에서 블록 blob을 복원 하는 것은 지원 되지 않습니다. 예를 들어, 핫 계층의 blob을 2 일 전에 보관 계층으로 이동 하 고 복원 작업이 3 일 전 지점으로 복원 되 면 blob이 핫 계층으로 복원 되지 않습니다. 보관 된 blob을 복원 하려면 먼저 보관 계층 외부로 이동 합니다. 자세한 내용은 [리하이드레이션 blob data from the archive 계층](../storage/blobs/storage-blob-rehydration.md)항목을 참조 하세요.
- [Put 블록 또는](/rest/api/storageservices/put-block) [URL에서](/rest/api/storageservices/put-block-from-url)put 블록을 통해 업로드 되었지만 [put 블록 목록을](/rest/api/storageservices/put-block-list)통해서는 커밋되지 않은 블록은 blob의 일부가 아니므로 복원 작업의 일부로 복원 되지 않습니다.
- 활성 임대가 있는 blob은 복원할 수 없습니다. 활성 임대가 있는 blob이 복원할 blob 범위에 포함 된 경우 복원 작업이 자동으로 실패 합니다. 복원 작업을 시작 하기 전에 활성 임대를 중단 합니다.
- 스냅숏은 복원 작업의 일부로 생성 되거나 삭제 되지 않습니다. 기본 blob만 이전 상태로 복원 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Blob에 대 한 운영 백업 개요 (미리 보기)](blob-backup-overview.md)