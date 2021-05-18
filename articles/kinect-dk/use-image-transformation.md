---
title: Azure Kinect 센서 SDK 이미지 변환 사용
description: Azure Kinect 센서 SDK 이미지 변환 함수를 사용하는 방법을 알아봅니다.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: kinect, azure, 센서, sdk, 좌표계, 보정, 프로젝트, 언프로젝트, 변환, rgb-d, 포인트 클라우드
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276904"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Azure Kinect 센서 SDK 이미지 변환 사용

Azure Kinect DK에서 조정된 카메라 시스템 간에 이미지를 사용하고 변환하려면 특정 함수를 따릅니다.

## <a name="k4a_transformation-functions"></a>k4a_transformation 함수

 *k4a_transformation* 으로 접두사가 지정된 함수는 모두 전체 이미지에 대해 작동합니다. [k4a_transformation_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)를 통해 가져온 변환 핸들 [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html)가 필요하며 [k4a_transformation_destroy()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44)를 통해 할당 해제됩니다. 또한 이 항목의 세 가지 함수를 사용하는 방법을 보여 주는 SDK [변환 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)를 참조할 수 있습니다.

지원되는 함수는 다음과 같습니다.

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>개요

 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 함수는 깊이 카메라의 뷰포인트에서 색상 카메라의 뷰포인트로 깊이 맵을 변환합니다. 이 함수는 소위 RGB-D 이미지를 생성하도록 디자인되었는데 여기에서 D는 깊이 값을 기록하는 추가적인 이미지 채널을 나타냅니다. 아래 그림에서 보이는 것처럼 색상 이미지와 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)의 출력은 마치 동일한 뷰포인트, 즉 색상 카메라의 뷰포인트에서 찍은 것처럼 보입니다.

   ![이미지 변환](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>구현

 이 변환 함수는 간단하게 모든 픽셀에 대해 [k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4)를 호출하는 것보다 더 복잡합니다. 깊이 카메라의 기하 도형에서 색상 카메라의 기하 도형으로 삼각형 메시를 왜곡합니다. 삼각형 메시는 변환된 깊이 이미지에서 빈 영역이 생성되는 것을 피하기 위해 사용됩니다. Z-버퍼는 폐색이 올바르게 처리되도록 합니다. 이 함수의 경우 기본적으로 GPU 가속이 사용하도록 설정되어 있습니다.

#### <a name="parameters"></a>매개 변수

 입력 매개 변수는 변환 핸들 및 깊이 이미지입니다. 깊이 이미지 해상도는 변환 핸들 생성에 지정된 ```depth_mode```와 일치해야 합니다. 예를 들어 1024x1024 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` 모드를 사용하여 변환 핸들이 만들어졌다면 깊이 이미지의 해상도는 1024x1024 픽셀이어야 합니다. 출력은 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 호출을 통해 사용자가 할당해야 하는 변환된 깊이 이미지입니다. 변환된 깊이 이미지 해상도는 변환 핸들 생성에 지정된 ```color_resolution```과 일치해야 합니다. 예를 들어 색상 해상도가 `K4A_COLOR_RESOLUTION_1080P`로 설정되었다면 출력 이미지 해상도는 1920x1080 픽셀이어야 합니다. 이미지가 16비트 깊이 값을 저장하므로 출력 이미지 스트라이드는 `width * sizeof(uint16_t)`로 설정됩니다.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>개요

 [k4a_transformation_depth_image_to_color_camera_custom()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) 함수는 깊이 카메라의 뷰포인트에서 색상 카메라의 뷰포인트로 깊이 맵과 사용자 지정 이미지를 변환합니다. [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)의 확장으로서 이 함수는 각 픽셀이 변환된 깊이 이미지에 추가로 색상 카메라의 해당 픽셀 좌표와 일치하는 해당 사용자 지정 이미지를 생성하도록 설계되었습니다.

#### <a name="implementation"></a>구현

 이 변환 함수는 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)와 동일한 방식으로 변환된 깊이 이미지를 생성합니다. 사용자 지정 이미지를 변환하기 위해 이 함수는 선형 보간 또는 가장 인접한 항목 보간을 사용하는 옵션을 제공합니다. 선형 보간을 사용하면 변환된 사용자 지정 이미지에 새 값을 만들 수 있습니다. 가장 인접한 항목 보간을 사용하면 원본 이미지에 없는 값이 출력 이미지에 나타나지 않도록 방지하지만 덜 부드러운 이미지가 됩니다. 사용자 지정 이미지는 단일 채널 8비트 또는 16비트여야 합니다. 이 함수의 경우 기본적으로 GPU 가속이 사용하도록 설정되어 있습니다.

#### <a name="parameters"></a>매개 변수

 입력 매개 변수는 변환 핸들, 깊이 이미지, 사용자 지정 이미지 및 보간 유형입니다. 깊이 이미지 및 사용자 지정 해상도는 변환 핸들 생성에 지정된 `depth_mode`와 일치해야 합니다. 예를 들어 1024x1024 `K4A_DEPTH_MODE_WFOV_UNBINNED` 모드를 사용하여 변환 핸들이 만들어졌다면 깊이 이미지 및 사용자 지정 이미지의 해상도는 1024x1024 픽셀이어야 합니다. `interpolation_type`은 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` 또는 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST`여야 합니다. 출력은 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 호출을 통해 사용자가 할당해야 하는 변환된 깊이 이미지 및 변환된 사용자 지정 이미지입니다. 변환된 깊이 이미지와 변환된 사용자 지정 이미지의 해상도는 변환 핸들 생성에 지정된 `color_resolution`과 일치해야 합니다. 예를 들어 색상 해상도가 `K4A_COLOR_RESOLUTION_1080P`로 설정되었다면 출력 이미지 해상도는 1920x1080 픽셀이어야 합니다. 이미지가 16비트 깊이 값을 저장하므로 출력 깊이 이미지 스트라이드는 `width * sizeof(uint16_t)`로 설정됩니다. 입력 사용자 지정 이미지 및 변환된 사용자 지정 이미지는 `K4A_IMAGE_FORMAT_CUSTOM8` 또는 `K4A_IMAGE_FORMAT_CUSTOM16` 형식이어야 하며 이에 따라 해당 이미지 스트라이드를 설정해야 합니다. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>개요

 [k4a_transformation_color_image_to_depth_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) 함수는 색상 카메라의 뷰포인트에서 깊이 카메라의 뷰포인트로 색상 이미지를 변환합니다(위의 그림 참조). RGB-D 이미지를 생성하는 데 사용할 수 있습니다.

#### <a name="implementation"></a>구현

 깊이 맵의 모든 픽셀에 대해서 이 함수는 픽셀의 깊이 값을 사용하여 색상 이미지의 해당 서브 픽셀 좌표를 계산합니다. 그런 다음 색상 이미지의 이 좌표에서 색상 값을 찾습니다. 색상 이미지에서 쌍선형 보간을 수행하여 서브 픽셀 정밀도의 색상 값을 얻습니다. 연결된 깊이 읽기가 없는 픽셀은 출력 이미지의 `[0,0,0,0]`의 BGRA 값에 할당됩니다. 이 함수의 경우 기본적으로 GPU 가속이 사용하도록 설정되어 있습니다. 이 메서드는 변환된 색상 이미지에 빈 영역을 생성하고 폐색을 처리하지 않으므로 대신 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 함수를 사용하는 것이 좋습니다.

#### <a name="parameters"></a>매개 변수

입력 매개 변수는 변환 핸들, 깊이 이미지 및 색상 이미지입니다. 깊이 및 색상 이미지의 해상도는 변환 핸들 생성에 지정된 depth_mode 및 color_resolution과 일치해야 합니다. 출력은 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 호출을 통해 사용자가 할당해야 하는 변환된 색상 이미지입니다. 변환된 색상 이미지의 해상도는 변환 핸들 생성에 지정된 depth_resolution과 일치해야 합니다. 출력 이미지에는 모든 픽셀에 대해 BGRA를 나타내는 네 개의 8비트 값이 저장됩니다. 따라서 해당 이미지의 스트라이드는 ```width * 4 * sizeof(uint8_t)```입니다. 데이터 순서는 픽셀 인터리브되어 있어서 파란색 값 - 픽셀 0, 녹색 값 - 픽셀 0, 빨간색 값 - 픽셀 0, 알파 값 - 픽셀 0, 파란색 값 - 픽셀 1 식으로 계속됩니다.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>개요

[k4a_transformation_depth_image_to_point_cloud()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) 함수는 카메라가 찍은 2D 깊이 맵을 동일한 카메라의 좌표계에서 3D 포인트 클라우드로 변환합니다. 그 때문에 카메라는 깊이 카메라나 색상 카메라가 가능합니다.

#### <a name="implementation"></a>구현

 이 함수는 모든 픽셀에 대해 [k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4)를 실행하는 것과 동등한 결과를 제공하지만 계산 측면에서 더 효율적입니다. [k4a_transformation_create](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)를 호출할 때 모든 이미지 픽셀에 대해 x 및 y배율을 저장하는 소위 xy-조회 테이블을 미리 계산합니다. [k4a_transformation_depth_image_to_point_cloud()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)를 호출할 때 픽셀의 x 배율과 픽셀의 Z 좌표를 곱하여 픽셀의 3D X 좌표를 얻습니다. 유사하게 3D Y 좌표는 y 배율과 곱하여 계산 됩니다. SDK의 [빠른 포인트 클라우드 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)에서는 xy-테이블을 계산하는 방법을 보여 줍니다. 사용자는 예제 코드를 따라 이 함수의 자체적인 버전을 구현하여 예를 들어 GPU 파이프라인의 속도를 높일 수 있습니다.

#### <a name="parameters"></a>매개 변수

 입력 매개 변수는 변환 핸들, 카메라 지정자 및 깊이 이미지입니다. 카메라 지정자를 깊이로 설정한 경우 깊이 이미지의 해상도는 변환 핸들 생성에 지정된 depth_mode와 일치해야 합니다. 그렇지 않고 지정자를 색상 카메라로 설정한 경우 해상도는 선택한 color_resolution의 해상도와 일치해야 합니다. 출력 매개 변수는 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 호출을 통해 사용자가 할당해야 하는 XYZ 이미지입니다. XYZ 이미지 해상도는 입력 깊이 맵의 해상도와 일치해야 합니다. 부호가 있는 세 개의 16비트 좌표 값을 모든 픽셀에 대해 밀리미터 단위로 저장합니다. 따라서 XYZ 이미지 스트라이드는 `width * 3 * sizeof(int16_t)`로 설정됩니다. 데이터 순서는 픽셀 인터리브되어 있어서 X 좌표 – 픽셀 0, Y 좌표 – 픽셀 0, Z 좌표 – 픽셀 0, X 좌표 – 픽셀 1 식으로 계속됩니다. 픽셀을 3D로 변환할 수 없는 경우 이 함수는 `[0,0,0]` 값을 픽셀에 할당합니다.

## <a name="samples"></a>샘플

[변환 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>다음 단계

이제 Azure Kinect 센서 SDK 이미지 변환 함수를 사용하는 방법을 배웠으므로 다음 내용에 대해 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[Azure Kinect 센서 SDK 보정 함수](use-calibration-functions.md)

또한 다음을 검토할 수 있습니다.

[좌표계](coordinate-systems.md)
