---
title: Azure Kinect 본문 인덱스 맵
description: Azure Kinect 진한에서 본문 추적 인덱스 맵을 쿼리 하는 방법에 대해 알아봅니다.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 포팅, 본문, 추적, 인덱스, 조각화, 매핑
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277028"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Azure Kinect 본문 추적 인덱스 맵

본문 인덱스 맵에는 깊이 카메라 캡처의 각 본문에 대 한 인스턴스 분할 맵이 포함 됩니다. 각 픽셀은 깊이 또는 IR 이미지의 해당 픽셀에 매핑됩니다. 각 픽셀의 값은 픽셀이 속하는 본문을 나타냅니다. 이 값은 배경 (값 `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) 이거나 검색 된의 인덱스 일 수 있습니다 `k4abt_body_t` .

![본문 인덱스 맵 예](./media/concepts/body-index-map.png)

>[!NOTE]
> 본문 인덱스가 본문 id와 다릅니다. API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)를 호출 하 여 지정 된 본문 인덱스에서 본문 id를 쿼리할 수 있습니다.


## <a name="using-body-index-map"></a>본문 인덱스 맵 사용

본문 인덱스 맵은로 저장 되며 `k4a_image_t` 깊이 또는 IR 이미지와 동일한 해상도를 가집니다. 각 픽셀은 8 비트 값입니다. 을 `k4abt_frame_t` 호출 하 여에서 쿼리할 수 있습니다 `k4abt_frame_get_body_index_map` . 개발자는를 호출 하 여 본문 인덱스 맵에 대 한 메모리를 해제 해야 합니다 `k4a_image_release()` .

## <a name="next-steps"></a>다음 단계

[첫 번째 신체 추적 앱 빌드](build-first-body-app.md)
