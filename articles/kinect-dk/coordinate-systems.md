---
title: Azure Kinect 진한 좌표계
description: Azure Kinect 진한 좌표계 설명 Azure 진한 센서와 연결
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이 카메라, tof, 원칙, 성능, 무효화
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276892"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect 진한 좌표계

이 문서에서는 2D 및 3D 좌표계에 사용 되는 규칙을 설명 합니다.  각 센서의 장치와 연결 된 별도의 좌표계가 있으며 이러한 좌표계 사이에 요소를 변환 하는 데 사용할 수 있는 [보정 함수가](use-calibration-functions.md) 있습니다. [변환 함수](use-image-transformation.md) 는 좌표계 사이에서 전체 이미지를 변환 합니다.  

## <a name="2d-coordinate-systems"></a>2D 좌표계

 깊이 및 색 카메라는 모두 독립 된 2D 좌표계와 연결 됩니다. [X, y] 좌표는 픽셀 단위로 표현 됩니다. 여기서 *x* 는 0에서 1을 1로, *y* 범위는 0에서 높이-1 사이입니다. 너비와 높이는 깊이 및 색 카메라가 작동 하는 선택한 모드에 따라 달라 집니다. 픽셀 좌표는 `[0,0]` 이미지의 왼쪽 위 픽셀에 해당 합니다. 픽셀 좌표는 하위 픽셀 좌표를 나타내는 소수 자릿수가 될 수 있습니다.

2D 좌표계는 0에서 가운데 맞춤 됩니다. 즉, 하위 픽셀 좌표는 `[0.0, 0.0]` `[0.5,0.5]` 아래와 같이 픽셀의 중심 및 오른쪽 아래 모퉁이를 나타냅니다.

   ![2D 좌표계](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D 좌표계

각 카메라,가 속도계 및 자이로스코프가는 독립적인 3D 좌표 공간 시스템과 연결 됩니다.

3D 좌표계의 점은 미터 단위의 메트릭 [X, Y, Z] 좌표 triplets 표현 됩니다.

### <a name="depth-and-color-camera"></a>깊이 및 컬러 카메라

원점은 `[0,0,0]` 카메라의 초점에 있습니다. 좌표계는 양의 X 축이 오른쪽을 가리키고 양의 Y 축은 아래로, 양의 Z 축은 앞으로 이동 하는 방향입니다.

깊이 카메라는 아래에 표시 된 것 처럼 컬러 카메라의 아래쪽으로 기울어져 있습니다. 

깊이 카메라에 사용 되는 두 개의 illuminators 있습니다. 좁은 NFOV (illuminator) 모드에서 사용 되는는 깊이 카메라 케이스와 맞춰져 있으므로 illuminator가 기울어져 있지 않습니다. 넓은 WFOV (illuminator) 모드에서 사용 되는는 깊이 카메라를 기준으로 하 여 추가 1.3도를 하향 표시 합니다.

![3D 좌표 규칙](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>자이로스코프가 및가 속도계

자이로스코프가의 원점은 `[0,0,0]` 깊이 카메라의 원점과 동일 합니다. 가 속도계의 원점은 실제 위치와 일치 합니다. 가 속도계 및 자이로스코프가 좌표계는 모두 오른쪽으로 전달 됩니다. 좌표계의 양의 X 축은 뒤로, 양의 Y 축 지점은 왼쪽, 양의 Z 축은 아래와 같이 아래로 가리킵니다.

![IMU 좌표계](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>다음 단계

[Azure Kinect 센서 SDK에 대해 알아보기](about-sensor-sdk.md)