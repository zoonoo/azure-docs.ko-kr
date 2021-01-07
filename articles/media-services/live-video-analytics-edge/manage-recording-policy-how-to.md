---
title: 기록 정책 관리-Azure
description: 이 항목에서는 기록 정책을 관리 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011484"
---
# <a name="manage-recording-policy"></a>녹음/녹화 정책 관리

[연속 비디오 녹화](continuous-video-recording-concept.md)IoT Edge에서 라이브 비디오 분석을 사용 하 여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. Azure storage에 기본 제공 되는 [수명 주기 관리 도구](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) 를 사용 하 여 해당 클라우드 보관의 길이 (일)를 관리할 수 있습니다.  

미디어 서비스 계정은 Azure Storage 계정에 연결 되어 있으며, 클라우드에 비디오를 기록 하는 경우 콘텐츠가 미디어 서비스 [자산](../latest/assets-concept.md)에 기록 됩니다. 각 자산은 저장소 계정의 컨테이너에 매핑됩니다. 수명 주기 관리를 사용 하 여 저장소 계정에 대 한 [정책을](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) 정의할 수 있습니다 .이 경우 다음과 같은 [규칙](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) 을 지정할 수 있습니다.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

위의 규칙은 다음과 같습니다.

* 저장소 계정의 모든 블록 blob에 적용 됩니다.
* Blob이 30 일이 넘은 경우 [핫 액세스 계층에서 쿨로](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)이동 하도록 지정 합니다.
* Blob이 90 일을 초과 하면 삭제 됩니다.

Live Video Analytics는 지정 된 시간 단위로 비디오를 보관 하므로 자산에는 세그먼트 당 하나의 blob 인 일련의 blob이 포함 됩니다. 수명 주기 관리 정책이 실행 되 고 이전 blob를 삭제 하는 경우 Media Service Api를 통해 나머지 blob에 계속 액세스 하 고 재생할 수 있습니다. 자세한 내용은 [재생 기록](playback-recordings-how-to.md)을 참조 하세요. 

## <a name="limitations"></a>제한 사항

다음은 수명 주기 관리에 대 한 몇 가지 알려진 제한 사항입니다.

* 정책 내에서 최대 100 개의 규칙을 가질 수 있으며 각 규칙은 최대 10 개의 컨테이너를 지정할 수 있습니다. 따라서 다른 녹음 정책을 사용 해야 하는 경우 (예를 들어, 로드 도크에서 카메라에 대해 3 일 아카이브, 로드 도크에서 카메라의 경우 30 일, 체크 아웃 카운터 뒤의 카메라의 경우 180 일), 한 번의 미디어 서비스 계정으로 최대 1000 카메라에 대 한 규칙을 사용자 지정할 수 있습니다.
* 수명 주기 관리 정책 업데이트는 즉시 실행 되지 않습니다. 자세한 내용은 [이 FAQ 섹션](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) 을 참조 하세요.
* Blob이 쿨 계층으로 이동 하는 정책을 적용 하도록 선택 하는 경우 해당 보관 부분의 재생에 영향을 줄 수 있습니다. 추가 대기 시간이 나 간헐적 오류가 표시 될 수 있습니다. Media Services는 보관 계층의 콘텐츠 재생을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

[녹음/녹화 재생](playback-recordings-how-to.md)
