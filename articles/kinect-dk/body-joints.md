---
title: Azure Kinect 본문 추적 조인트
description: Azure Kinect에서 본문 프레임, 조인트, 조인트 좌표 및 공동 계층 구조를 이해 합니다.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 포팅, 본문, 추적, 조인트, 계층, 뼈, 연결
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276996"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect 본문 추적 조인트

Azure Kinect 본문 추적은 동시에 여러 사람의 본문을 추적할 수 있습니다. 각 본문에는 프레임과 운동학 적 해골 간의 임시 상관 관계에 대 한 ID가 포함 됩니다. 각 프레임에서 검색 된 본문 수는를 사용 하 여 가져올 수 있습니다 `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>조인트

공동 위치와 방향은 참조의 글로벌 깊이 센서 프레임을 기준으로 하는 예상 률입니다. 위치는 밀리미터 단위로 지정 됩니다. 방향은 정규화 된 4 원수로 표시 됩니다.

## <a name="joint-coordinates"></a>조인트 좌표

각 조인트의 위치와 방향은 고유한 접합 좌표계를 형성 합니다. 모든 공동 좌표계는 깊이 카메라 3D 좌표계를 기준으로 하는 절대 좌표계입니다.

> [!NOTE]
> 조인트 좌표는 축 방향입니다. 축 방향은 상업적 아바타, 게임 엔진 및 렌더링 소프트웨어에서 널리 사용 됩니다. 축 방향을 사용 하면 대칭 이동이 간단해 집니다. 예를 들어 두 개의 팔걸이를 20도 올립니다.

![조인트 좌표](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>공동 계층

기본 구조에는 본문의 중앙에서 extremities 흐르는 조인트 계층 구조와 함께 32이 포함 됩니다. 각 연결 (뼈)은 부모 조인트를 자식 조인트와 연결 합니다. 다음 그림에서는 인간 본문에 상대적인 공동 위치 및 연결을 보여 줍니다.

![공동 계층](./media/concepts/joint-hierarchy.png)

다음 표에서는 표준 연결 연결을 열거 합니다.

|Index |조인트 이름     | 부모 조인트   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |목           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | 목           |
| 27   |코           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>다음 단계

[신체 추적 인덱스 맵](body-index-map.md)
