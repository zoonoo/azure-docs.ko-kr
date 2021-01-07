---
title: Azure Kinect 센서 SDK 이미지 변환 사용
description: Azure Kinect 센서 SDK 이미지 변환 함수를 사용 하는 방법에 대해 알아봅니다.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: kinect, azure, 센서, sdk, 좌표계, 보정, 프로젝트, unproject, 변환, rgb-d, point cloud
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276904"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Azure Kinect 센서 SDK 이미지 변환 사용

특정 기능을 수행 하 여 Azure Kinect에서 조정 된 카메라 시스템 간에 이미지를 사용 하 고 변환 합니다.

## <a name="k4a_transformation-functions"></a>k4a_transformation 함수

 *K4a_transformation* 접두사가 있는 모든 함수는 전체 이미지에 대해 작동 합니다. [K4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) 를 통해 가져온 변환 핸들 [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) 필요 하며 [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44)를 통해 할당 해제 됩니다. 이 항목의 세 가지 함수를 사용 하는 방법을 보여 주는 SDK [변환 예제](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) 를 참조할 수도 있습니다.

지원되는 함수는 다음과 같습니다.

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>개요

 함수 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 는 깊이 카메라의 시점에서 색 카메라의 시점으로 깊이 맵을 변환 합니다. 이 함수는 RGB D 이미지를 생성 하도록 디자인 되었습니다. 여기서 D는 깊이 값을 기록 하는 추가 이미지 채널을 나타냅니다. 아래 그림에 표시 된 것 처럼 색 이미지와 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 의 출력은 동일한 관점에서 가져온 것 처럼 보입니다. 즉, 색 카메라의 시점입니다.

   ![이미지 변환](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>구현

 이 변환 함수는 단순히 모든 픽셀에 대해 [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 를 호출 하는 것 보다 더 복잡 합니다. 깊이 카메라의 기 하 도형에서 색 카메라의 기 하 도형에 대 한 삼각형 메시를 개 이동 합니다. 삼각형 메시는 변환 된 깊이 이미지에서 구멍이 생성 되지 않도록 하는 데 사용 됩니다. Z 버퍼는 occlusions이 올바르게 처리 되도록 합니다. 이 함수에 대 한 GPU 가속은 기본적으로 사용 하도록 설정 되어 있습니다.

#### <a name="parameters"></a>매개 변수

 입력 매개 변수는 변환 핸들 및 깊이 이미지입니다. 깊이 이미지 해상도는 ```depth_mode``` 변환 핸들을 만들 때 지정 된와 일치 해야 합니다. 예를 들어 1024x1024 모드를 사용 하 여 변환 핸들을 만든 경우 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` 깊이 이미지의 해상도는 1024x1024 픽셀 이어야 합니다. 출력은 [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)를 호출 하 여 사용자가 할당 해야 하는 변환 된 깊이 이미지입니다. 변환 된 깊이 이미지의 해상도는 ```color_resolution``` 변환 핸들을 만들 때 지정 된와 일치 해야 합니다. 예를 들어 색 해상도가로 설정 된 경우 `K4A_COLOR_RESOLUTION_1080P` 출력 이미지 해상도는 1920 x 1080 픽셀 이어야 합니다. 이미지가 16 비트 깊이 값을 저장 하기 때문에 출력 이미지 stride는로 설정 됩니다 `width * sizeof(uint16_t)` .

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>개요

 함수 [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) 는 깊이 카메라의 시점에서 색 카메라의 시점으로 깊이 맵과 사용자 지정 이미지를 변환 합니다. [K4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)의 확장으로,이 함수는 각 픽셀에서 변형 된 깊이 이미지에 대 한 컬러 카메라의 해당 픽셀 좌표와 일치 하는 해당 사용자 지정 이미지를 생성 하도록 디자인 되었습니다.

#### <a name="implementation"></a>구현

 이 변환 함수는 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)와 동일한 방식으로 변환 된 깊이 이미지를 생성 합니다. 사용자 지정 이미지를 변환 하기 위해이 함수는 선형 보간 또는 가장 인접 한 항목 보간을 사용 하는 옵션을 제공 합니다. 선형 보간을 사용 하면 변환 된 사용자 지정 이미지에 새 값을 만들 수 있습니다. 가장 인접 한 항목 보간을 사용 하면 원본 이미지의 값이 출력 이미지에 표시 되지 않지만 부드러운 이미지를 초래 하지 않습니다. 사용자 지정 이미지는 단일 채널 8 비트 또는 16 비트 여야 합니다. 이 함수에 대 한 GPU 가속은 기본적으로 사용 하도록 설정 되어 있습니다.

#### <a name="parameters"></a>매개 변수

 입력 매개 변수는 변환 핸들, 깊이 이미지, 사용자 지정 이미지 및 보간 유형입니다. 깊이 이미지와 사용자 지정 이미지 해상도는 `depth_mode` 변환 핸들을 만들 때 지정 된와 일치 해야 합니다. 예를 들어 1024x1024 모드를 사용 하 여 변환 핸들을 만든 경우 `K4A_DEPTH_MODE_WFOV_UNBINNED` 깊이 이미지와 사용자 지정 이미지의 해상도는 1024x1024 픽셀 이어야 합니다. 는 `interpolation_type` 또는 중 하나 여야 합니다 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . 출력은 [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)를 호출 하 여 사용자가 할당 해야 하는 변환 된 깊이 이미지 및 변환 된 사용자 지정 이미지입니다. 변환 된 깊이 이미지 및 변환 된 사용자 지정 이미지의 해상도는 `color_resolution` 변환 핸들을 만들 때 지정 된와 일치 해야 합니다. 예를 들어 색 해상도가로 설정 된 경우 `K4A_COLOR_RESOLUTION_1080P` 출력 이미지 해상도는 1920 x 1080 픽셀 이어야 합니다. 이미지가 16 비트 깊이 값을 저장 하기 때문에 출력 깊이 이미지 stride는로 설정 됩니다 `width * sizeof(uint16_t)` . 입력 사용자 지정 이미지 및 변환 된 사용자 지정 이미지는 형식 `K4A_IMAGE_FORMAT_CUSTOM8` 이거나 `K4A_IMAGE_FORMAT_CUSTOM16` 그에 따라 해당 이미지 stride를 설정 해야 합니다. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>개요

 함수 [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) 는 컬러 카메라의 관점에서 색 이미지를 깊이 카메라의 시점으로 변환 합니다 (위 그림 참조). RGB 이미지를 생성 하는 데 사용할 수 있습니다.

#### <a name="implementation"></a>구현

 깊이 맵의 모든 픽셀에 대해이 함수는 픽셀의 깊이 값을 사용 하 여 색 이미지의 해당 하위 픽셀 좌표를 계산 합니다. 그런 다음 색 이미지의이 좌표에서 색 값을 조회 합니다. 색 이미지에서 이중 선형 보간을 수행 하 여 하위 픽셀 정밀도에서 색 값을 가져옵니다. 연결 된 깊이 읽기가 없는 픽셀은 출력 이미지에서 BGRA 값에 할당 됩니다 `[0,0,0,0]` . 이 함수에 대 한 GPU 가속은 기본적으로 사용 하도록 설정 되어 있습니다. 이 메서드는 변환 된 색 이미지에 구멍을 생성 하 고 occlusions를 처리 하지 않으므로 대신 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 함수를 사용 하는 것이 좋습니다.

#### <a name="parameters"></a>매개 변수

입력 매개 변수는 변환 핸들, 깊이 이미지 및 색 이미지입니다. 깊이 및 색 이미지의 해상도는 변환 핸들을 만들 때 지정 된 depth_mode 및 color_resolution 일치 해야 합니다. 출력은 [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)를 호출 하 여 사용자가 할당 해야 하는 변형 된 색 이미지입니다. 변환 된 색 이미지의 해상도는 변환 핸들을 만들 때 지정 된 depth_resolution 일치 해야 합니다. 출력 이미지에는 모든 픽셀에 대해 BGRA를 나타내는 4 8 비트 값이 저장 됩니다. 따라서 이미지의 stride는 ```width * 4 * sizeof(uint8_t)``` 입니다. 데이터 순서는 픽셀 인터리브, 즉 파랑 값-픽셀 0, 녹색 값-픽셀 0, 빨강 값-픽셀 0, 알파 값-픽셀 0, 파랑 값-픽셀 1 등입니다.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>개요

함수 [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) 는 카메라에서 사용한 2d 깊이 맵을 동일한 카메라의 좌표계에서 3d 포인트 클라우드로 변환 합니다. 카메라는 깊이 또는 컬러 카메라 일 수 있습니다.

#### <a name="implementation"></a>구현

 함수는 모든 픽셀에 대해 [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 를 실행 하는 것과 동일한 결과를 제공 하지만 계산이 더 효율적입니다. [K4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)를 호출 하는 경우 모든 이미지 픽셀에 대 한 x 및 y 배율 요소를 저장 하는 xy 조회 테이블을 미리 계산 합니다. [K4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)를 호출 하는 경우 픽셀의 x 배율 인수를 픽셀의 Z 좌표와 곱하여 픽셀의 3d x 좌표를 가져옵니다. 와 유사 3D Y 좌표는 y 배율 계수를 사용 하 여 계산 됩니다. SDK의 [fast point 클라우드 예제](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) 에서는 xy 테이블을 계산 하는 방법을 보여 줍니다. 사용자는 예제 코드를 따라이 함수의 고유한 버전을 구현 하 여 GPU 파이프라인의 속도를 높일 수 있습니다.

#### <a name="parameters"></a>매개 변수

 입력 매개 변수는 변환 핸들, 카메라 지정자 및 깊이 이미지입니다. 카메라 지정자를 depth로 설정 하면 깊이 이미지의 해상도가 변환 핸들을 만들 때 지정 된 depth_mode 일치 해야 합니다. 그렇지 않고, 지정 자가 색 카메라로 설정 된 경우 해상도는 선택한 color_resolution의 해상도와 일치 해야 합니다. Output 매개 변수는 [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)를 호출 하 여 사용자가 할당 해야 하는 XYZ 이미지입니다. XYZ 이미지 해상도는 입력 깊이 맵의 해상도와 일치 해야 합니다. 3 개의 부호 있는 16 비트 좌표 값을 모든 픽셀에 대 한 밀리미터 단위로 저장 합니다. 따라서 XYZ 이미지 stride는로 설정 됩니다 `width * 3 * sizeof(int16_t)` . 데이터 순서는 픽셀 인터리브, 즉 X 좌표 – 픽셀 0, Y 좌표 – 픽셀 0, Z 좌표 – 픽셀 0, X 좌표 – 픽셀 1 등입니다. 픽셀을 3D로 변환할 수 없는 경우이 함수는 픽셀에 값을 할당 합니다 `[0,0,0]` .

## <a name="samples"></a>샘플

[변환 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>다음 단계

이제 Azure Kinect 센서 SDK 이미지 변환 함수를 사용 하는 방법을 배웠으므로 다음에 대해 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[Azure Kinect 센서 SDK 보정 함수](use-calibration-functions.md)

검토할 수도 있습니다.

[좌표계](coordinate-systems.md)
