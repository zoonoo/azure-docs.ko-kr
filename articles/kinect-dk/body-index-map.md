---
title: Azure Kinect 신체 인덱스 맵
description: Azure Kinect DK에서 신체 추적 인덱스 맵을 쿼리하는 방법을 이해합니다.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 이동, 신체, 추적, 인덱스, 구분, 맵
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277028"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Azure Kinect 신체 추적 인덱스 맵

신체 인덱스 맵에는 깊이 카메라 캡처의 각 신체에 대한 인스턴스 구분 맵이 포함됩니다. 각 픽셀은 깊이 또는 IR 이미지에서 해당 픽셀에 매핑됩니다. 각 픽셀의 값은 해당 픽셀이 속하는 신체를 나타냅니다. 이는 배경(`K4ABT_BODY_INDEX_MAP_BACKGROUND` 값) 또는 검색된 `k4abt_body_t`의 인덱스일 수 있습니다.

![신체 인덱스 맵 예](./media/concepts/body-index-map.png)

>[!NOTE]
> 신체 인덱스는 신체 id와는 다릅니다. [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) API를 호출하여 지정된 신체 인덱스에서 신체 id를 쿼리할 수 있습니다.


## <a name="using-body-index-map"></a>신체 인덱스 맵 사용

신체 인덱스 맵은 `k4a_image_t`로 저장되며 깊이 또는 IR 이미지와 동일한 해상도를 가집니다. 각 픽셀은 8비트 값입니다. `k4abt_frame_get_body_index_map`을 호출하여 `k4abt_frame_t`에서 쿼리될 수 있습니다. `k4a_image_release()`를 호출하여 신체 인덱스 맵용 메모리를 해제하는 것은 개발자가 담당합니다.

## <a name="next-steps"></a>다음 단계

[첫 번째 신체 추적 앱 빌드](build-first-body-app.md)
