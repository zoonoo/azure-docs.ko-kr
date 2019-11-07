---
title: Azure HPC 캐시 저장소 대상 업데이트
description: Azure HPC 캐시 저장소 대상을 편집 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 757fbc166687110b9061ab95e96b0182e0ad3774
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622769"
---
# <a name="edit-storage-targets"></a>저장소 대상 편집

저장소 대상을 편집 하 여 일부 속성을 수정할 수 있습니다. 다른 유형의 저장소에 대해 서로 다른 속성을 편집할 수 있습니다.

* Blob 저장소 대상의 경우 네임 스페이스 경로를 변경할 수 있습니다.

* NFS 저장소 대상의 경우 다음 속성을 변경할 수 있습니다.

  * 네임 스페이스 경로
  * 사용 모델
  * 내보내기
  * 하위 디렉터리 내보내기

저장소 대상의 이름, 유형 또는 백 엔드 저장소 시스템 (Blob 컨테이너 또는 NFS 호스트 이름/i p 주소)은 편집할 수 없습니다. 이러한 속성을 변경 해야 하는 경우 저장소 대상을 삭제 하 고 새 값으로 대체를 만듭니다.

저장소 대상을 수정 하려면 저장소 대상 이름을 클릭 하 여 세부 정보 페이지를 엽니다. 페이지의 일부 필드는 편집할 수 있습니다.

![NFS 저장소 대상에 대 한 편집 페이지의 스크린샷](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS 저장소 대상 업데이트

NFS 저장소 대상의 경우 여러 속성을 업데이트할 수 있습니다. 예제 편집 페이지는 위의 스크린샷을 참조 하세요.

* **사용 모델** -사용 모델은 캐시가 데이터를 보존 하는 방법에 영향을 줍니다. 자세히 알아보려면 [사용 모델 선택](hpc-cache-add-storage.md#choose-a-usage-model) 을 참조 하세요.
* **가상 네임 스페이스 경로** -클라이언트가이 저장소 대상을 탑재 하는 데 사용 하는 경로입니다. 자세한 내용은 [집계 된 네임 스페이스 계획을](hpc-cache-namespace.md) 참조 하세요.
* **NFS 내보내기 경로** -이 네임 스페이스 경로에 사용할 저장소 시스템 내보내기
* **하위 디렉터리 경로** -이 네임 스페이스 경로와 연결할 하위 디렉터리 (내보내기 아래)입니다. 하위 디렉터리를 지정 하지 않아도 되는 경우이 필드를 비워 둡니다.

각 네임 스페이스 경로에는 내보내기와 하위 디렉터리의 고유한 조합이 필요 합니다. 즉, 백 엔드 저장소 시스템에서 정확히 동일한 디렉터리에 두 개의 다른 클라이언트 연결 경로를 만들 수 없습니다.

변경 후에는 **확인** 을 클릭 하 여 저장소 대상을 업데이트 하거나 **취소** 를 클릭 하 여 변경 내용을 취소 합니다.

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob 저장소 대상 업데이트

Blob 저장소 대상의 세부 정보 페이지를 사용 하 여 가상 네임 스페이스 경로를 수정할 수 있습니다. 

![blob 저장소 대상에 대 한 편집 페이지의 스크린샷](media/hpc-cache-edit-storage-blob.png)

완료 되 면 **확인** 을 클릭 하 여 저장소 대상을 업데이트 하거나 **취소** 를 클릭 하 여 변경 내용을 취소 합니다.

## <a name="next-steps"></a>다음 단계

* 이러한 옵션에 대 한 자세한 내용은 [저장소 대상 추가](hpc-cache-add-storage.md) 를 참조 하세요.
* 가상 경로 사용에 대 한 자세한 팁 [은 집계 된 네임 스페이스 계획](hpc-cache-namespace.md) 을 참조 하세요.
