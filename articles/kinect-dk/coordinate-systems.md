---
title: Azure Kinect DK 좌표계
description: Azure DK 센서와 관련된 Azure Kinect DK 좌표계 설명
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이 카메라, tof, 원리, 성능, 무효화
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276892"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK 좌표계

이 문서에서는 2D 및 3D 좌표계에 사용되는 규칙을 설명합니다.  각 센서의 디바이스와 관련된 별도의 좌표계 및 이들 사이에서 점을 변환할 수 있는 [보정 함수](use-calibration-functions.md)가 있습니다. [변환 함수](use-image-transformation.md)는 좌표계 사이에서 전체 이미지를 변환합니다.  

## <a name="2d-coordinate-systems"></a>2D 좌표계

 깊이 및 색상 카메라 둘 다 독립적인 2D 좌표계와 연결되어 있습니다. [x, y] 좌표는 픽셀 단위로 표현되며 여기서 *x* 의 범위는 0부터 너비-1까지이고 *y* 의 범위는 0부터 높이-1까지입니다. 너비와 높이는 깊이 및 색상 카메라가 작동하는 선택 모드에 따라 달라집니다. 픽셀 좌표 `[0,0]`은 이미지의 왼쪽 맨 위 픽셀에 해당합니다. 픽셀 좌표는 서브 픽셀 좌표를 나타내는 소수 자릿수가 될 수 있습니다.

아래와 같이 2D 좌표계는 0에 중심이 맞춰져 있어서 서브 픽셀 좌표 `[0.0, 0.0]`은 중심을 나타내고 `[0.5,0.5]`는 오른쪽 아래 모서리를 나타냅니다.

   ![2D 좌표계](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D 좌표계

각 카메라, 가속도계, 자이로스코프는 독립적인 3D 좌표 공간 시스템과 연결되어 있습니다.

3D 좌표계의 점은 밀리미터 단위의 메트릭 [X,Y,Z] 3자리 좌표로 표현됩니다.

### <a name="depth-and-color-camera"></a>깊이 및 색상 카메라

원점 `[0,0,0]`은 카메라의 초점에 있습니다. 좌표계는 양의 X축이 오른쪽을 가리키고, 양의 Y축이 아래쪽을 가리키고, 양의 Z축이 앞쪽을 가리키도록 방향이 지정됩니다.

아래와 같이 깊이 카메라는 색상 카메라의 아래쪽으로 6도 기울어져 있습니다. 

깊이 카메라에 사용되는 일루미네이터는 두 개가 있습니다. NFOV(좁은 시야) 모드에 사용되는 일루미네이터는 깊이 카메라 케이스에 맞춰져 있어서 일루미네이터가 기울어져 있지 않습니다. WFOV(넓은 시야) 모드에 사용되는 일루미네이터는 깊이 카메라를 기준으로 1.3도 더 아래로 기울어져 있습니다.

![3D 좌표 규칙](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>자이로스코프 및 가속도계

자이로스코프의 원점 `[0,0,0]`은 깊이 카메라의 원점과 동일합니다. 가속도계의 원점은 실제 위치와 일치합니다. 가속도계 및 자이로스코프 좌표계는 모두 오른손 좌표계입니다. 아래와 같이 좌표계의 양의 X축은 뒤쪽을 가리키고, 양의 Y축은 왼쪽을 가리키고, 양의 Z축은 아래쪽을 가리킵니다.

![IMU 좌표계](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>다음 단계

[Azure Kinect 센서 SDK에 대해 알아보기](about-sensor-sdk.md)