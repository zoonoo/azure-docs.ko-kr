---
title: Azure Kinect 보정 함수 사용
description: Azure Kinect에서 보정 함수를 사용 하는 방법에 대해 알아봅니다.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 좌표계, 보정, 기능, 카메라, 내장, 외부, 프로젝트, unproject, 변환, rgb-d, point cloud
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276940"
---
# <a name="use-azure-kinect-calibration-functions"></a>Azure Kinect 보정 함수 사용

보정 함수를 사용 하면 Azure Kinect 장치에서 각 센서의 좌표계 간에 점이 변형 될 수 있습니다. 전체 이미지를 변환 해야 하는 응용 프로그램은 [변환 함수](use-image-transformation.md)에서 사용할 수 있는 가속화 된 작업을 활용할 수 있습니다.

## <a name="retrieve-calibration-data"></a>보정 데이터 검색

좌표계 변환을 수행 하려면 장치 보정을 검색 해야 합니다. 보정 데이터는 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 데이터 형식으로 저장 됩니다. [K4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)함수를 통해 장치에서 가져옵니다. 보정 데이터는 각 장치에만 국한 되는 것이 아니라 카메라의 운영 모드에도 적용 됩니다. 따라서 [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 에는 `depth_mode` 및 `color_resolution` 매개 변수가 입력으로 필요 합니다.

### <a name="opencv-compatibility"></a>OpenCV 호환성

보정 매개 변수는 [OpenCV](https://opencv.org/)와 호환 됩니다. 개별 카메라 보정 매개 변수에 대 한 자세한 내용은 [OpenCV 설명서](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)를 참조 하세요. 또한 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 형식과 해당 하는 opencv 데이터 구조 간의 변환을 보여 주는 SDK의 [opencv 호환성 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) 를 참조 하세요.

## <a name="coordinate-transformation-functions"></a>좌표 변환 함수

아래 그림에는 Azure Kinect의 다양 한 좌표계와 둘 사이를 변환 하는 함수가 나와 있습니다. 자이로스코프가 및가 속도계의 3D 좌표계를 생략 하 여 그림을 단순하게 유지 합니다.

   ![좌표 변환](./media/how-to-guides/coordinate-transformation.png)

렌즈 왜곡에 대 한 주석: 2D 좌표는 항상 SDK에서 왜곡 된 이미지를 참조 합니다. SDK의 왜곡 되지 않은 [예제](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) 는 이미지 왜곡을 보여 줍니다. 일반적으로 3D 요소는 렌즈 왜곡의 영향을 받지 않습니다.

### <a name="convert-between-3d-coordinate-systems"></a>3D 좌표계 간 변환

함수 [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) 는 카메라의 외부 보정을 사용 하 여 원본 좌표계의 3d 포인트를 대상 좌표계의 3d 점으로 변환 합니다. 원본 및 대상은 4 가지 3D 좌표계, 즉 color camera, depth camera, 자이로스코프가 또는가 속도계 중 하나로 설정할 수 있습니다. 원본 및 대상이 동일한 경우 수정 되지 않은 입력 3D 점이 출력으로 반환 됩니다.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>2D 및 3D 좌표계 간 변환

함수 [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 는 원본 좌표계의 3d 포인트를 대상 카메라의 2d 픽셀 좌표로 변환 합니다. 이 함수를 종종 프로젝트 함수 라고 합니다. 원본을 네 가지 3D 좌표계 중 하나로 설정할 수 있지만, 대상은 깊이 또는 컬러 카메라 여야 합니다. 원본 및 대상이 다르면 입력 된 3D 점이 [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)를 사용 하 여 대상 카메라의 3d 좌표계로 변환 됩니다. 3D 점이 대상 카메라 좌표계에서 표시 되 면 해당 2D 픽셀 좌표는 대상 카메라의 내장 보정을 사용 하 여 계산 됩니다. 3D 점이 대상 카메라의 가시 영역을 벗어나면 유효한 값은 0으로 설정 됩니다.

함수 [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) 는 원본 카메라의 2d 픽셀 좌표를 대상 카메라 좌표계의 3d 점으로 변환 합니다. 원본은 색 또는 깊이 카메라 여야 합니다. 이 대상은 네 가지 3D 좌표계 중 하나로 설정할 수 있습니다. 2D 픽셀 좌표 외에도 원본 카메라 이미지의 픽셀 깊이 값 (밀리미터 단위)은 함수에 대 한 입력으로 필요 합니다. 색 카메라 기 하 도형에서 깊이 값을 파생 하는 한 가지 방법은 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)함수를 사용 하는 것입니다. 함수는 원본 카메라의 내장 보정을 사용 하 여 원본 카메라의 초점에서 지정 된 픽셀 좌표까지 이어지는 3D 광선을 계산 합니다. 그런 다음 깊이 값을 사용 하 여이 광선에서 3D 지점의 정확한 위치를 찾습니다. 이 작업을 종종 unproject 함수 라고 합니다. 원본 및 대상 카메라가 다른 경우 함수는 [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)를 통해 3d 점을 대상의 좌표계로 변환 합니다. 2D 픽셀 좌표가 원본 카메라의 가시 영역을 벗어나면 유효한 값은 0으로 설정 됩니다.

### <a name="converting-between-2d-coordinate-systems"></a>2D 좌표계 간 변환

함수 [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 는 원본 카메라의 2d 픽셀 좌표를 대상 카메라의 2d 픽셀 좌표로 변환 합니다. 원본 및 대상은 색 또는 깊이 카메라로 설정 되어야 합니다. 함수는 원본 카메라 이미지에서 픽셀의 깊이 값 (밀리미터 단위)을 입력으로 사용 해야 합니다. 색 카메라 기 하 도형에서 깊이 값을 파생 하는 한 가지 방법은 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)함수를 사용 하는 것입니다. [K4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) 를 호출 하 여 원본 카메라 시스템의 3d 지점으로 변환 합니다. 그런 다음 [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 를 호출 하 여 대상 카메라 이미지의 2d 픽셀 좌표로 변환 합니다. [K4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) 또는 [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 에서 잘못 된 결과를 반환 하는 경우 유효한 값은 0으로 설정 됩니다.

## <a name="related-samples"></a>관련 샘플

- [OpenCV 호환성 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [왜곡 안 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [빠른 시점 클라우드 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>다음 단계

이제 카메라 calibrations에 대해 알고 있습니다.
>[!div class="nextstepaction"]
>[캡처 디바이스 동기화](capture-device-synchronization.md)

검토할 수도 있습니다.

[좌표계](coordinate-systems.md)
