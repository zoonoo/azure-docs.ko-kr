---
title: Azure Kinect 보정 함수 사용
description: Azure Kinect DK의 보정 함수를 사용하는 방법을 알아봅니다.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 좌표계, 보정, 함수, 카메라, 내부, 외부, 프로젝트, 언프로젝트, 변환, rgb-d, 포인트 클라우드
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276940"
---
# <a name="use-azure-kinect-calibration-functions"></a>Azure Kinect 보정 함수 사용

보정 함수는 Azure Kinect 디바이스에서 각 센서의 좌표계 간에 점을 변환할 수 있게 합니다. 전체 이미지를 변환해야 하는 애플리케이션은 [변환 함수](use-image-transformation.md)에서 사용 가능한 가속화된 작업의 이점을 활용할 수 있습니다.

## <a name="retrieve-calibration-data"></a>보정 데이터 검색

좌표계 변환을 수행하려면 디바이스 보정을 검색해야 합니다. 보정 데이터는 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 데이터 형식으로 저장됩니다. [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 함수를 통해 디바이스에서 가져옵니다. 보정 데이터는 각각의 디바이스에 대해서뿐만 아니라 카메라의 운영 모드에 대해서도 지정되어 있습니다. 그러므로 [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)에는 `depth_mode` 및 `color_resolution` 매개 변수가 입력으로 필요합니다.

### <a name="opencv-compatibility"></a>OpenCV 호환성

보정 매개 변수는 [OpenCV](https://opencv.org/)와 호환됩니다. 개별 카메라 보정 매개 변수에 대한 자세한 내용은 [OpenCV 설명서](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)도 참조하세요. 또한 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 형식과 해당 OpenCV 데이터 구조 간의 변환을 보여 주는 SDK의 [OpenCV 호환성 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)를 참조하세요.

## <a name="coordinate-transformation-functions"></a>좌표 변환 함수

아래 그림에는 Azure Kinect의 여러 가지 좌표계와 둘 간에 변환하는 함수가 표시되어 있습니다. 자이로스코프 및 가속도계의 3D 좌표계를 생략하여 그림을 단순하게 유지합니다.

   ![좌표 변환](./media/how-to-guides/coordinate-transformation.png)

렌즈 왜곡에 대한 설명: 2D 좌표는 SDK에서 왜곡된 이미지를 항상 참조합니다. SDK의 [비왜곡 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)는 이미지의 비왜곡을 보여 줍니다. 일반적으로 3D 점은 렌즈 왜곡에 의한 영향을 전혀 받지 않습니다.

### <a name="convert-between-3d-coordinate-systems"></a>3D 좌표계 간 변환

[k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) 함수는 카메라의 외부 보정을 사용하여 원본 좌표계의 3D 포인트를 대상 좌표계의 3D 점으로 변환합니다. 원본과 대상은 네 가지의 3D 좌표계(색상 카메라, 깊이 카메라, 자이로스코프, 가속도계) 중 어느 것으로든 설정될 수 있습니다. 원본과 대상이 동일한 경우 수정되지 않은 입력 3D 점이 출력으로 반환됩니다.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>2D 및 3D 좌표계 간 변환

[k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 함수는 원본 좌표계의 3D 포인트를 대상 카메라의 2D 픽셀 좌표로 변환합니다. 이 함수는 흔히 프로젝트 함수라고 불립니다. 원본은 네 가지의 3D 좌표계 중 어느 것으로든 설정될 수 있는 반면, 대상은 깊이 카메라나 색상 카메라여야 합니다. 원본과 대상이 다른 경우 [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)를 사용하여 입력 3D 점이 대상 카메라의 3D 좌표계로 변환됩니다. 3D 점이 대상 카메라 좌표계에서 표시되고 나면 대상 카메라의 내부 보정을 사용하여 해당 2D 픽셀 좌표가 계산됩니다. 3D 점이 대상 카메라의 가시 영역을 벗어나면 유효한 값은 0으로 설정됩니다.

[k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) 함수는 원본 카메라의 2D 픽셀 좌표를 대상 카메라 좌표계의 3D 점으로 변환합니다. 원본은 색상 카메라나 깊이 카메라여야 합니다. 대상은 네 가지의 3D 좌표계 중 어느 것으로든 설정될 수 있습니다. 2D 픽셀 좌표 외에도 원본 카메라 이미지의 픽셀 깊이 값(밀리미터 단위)이 함수에 대한 입력으로서 필요한데, 색상 카메라의 기하 도형에서 깊이 값을 파생시키는 한 가지 방법은 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 함수를 사용하는 것입니다. 이 함수는 원본 카메라의 내부 보정을 사용하여 지정된 픽셀 좌표를 통해 원본 카메라의 초점에서 이어지는 3D 광선을 계산합니다. 그런 다음 깊이 값을 사용하여 이 광선에서 3D 점의 정확한 위치를 찾습니다. 이 작업은 흔히 언프로젝트 함수라고 불립니다. 원본 카메라와 대상 카메라가 다른 경우 이 함수는 [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)를 통해 3D 점을 대상의 좌표계로 변환합니다. 2D 픽셀 좌표가 원본 카메라의 가시 영역을 벗어나면 유효한 값은 0으로 설정됩니다.

### <a name="converting-between-2d-coordinate-systems"></a>2D 좌표계 간 변환

[k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 함수는 원본 카메라의 2D 픽셀 좌표를 대상 카메라의 2D 픽셀 좌표로 변환합니다. 원본과 대상은 색상 카메라나 깊이 카메라로 설정되어야 합니다. 이 함수에 원본 카메라 이미지의 픽셀 깊이 값(밀리미터 단위)이 입력으로 필요한데, 색상 카메라 기하 도형에서 깊이 값을 파생시키는 한 가지 방법은 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 함수를 사용하는 것입니다. [k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de)를 호출하여 원본 카메라 시스템의 3D 점으로 변환합니다. 그런 다음 [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9)를 호출하여 대상 카메라 이미지의 2D 픽셀 좌표로 변환합니다. [k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) 또는 [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9)가 유효하지 않은 결과를 반환하는 경우 유효한 값은 0으로 설정됩니다.

## <a name="related-samples"></a>관련 샘플

- [OpenCV 호환성 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [비왜곡 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [빠른 포인트 클라우드 예](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>다음 단계

이제 카메라 보정에 대해 배웠으므로 또한 다음 방법에 대해 알아볼 수도 있습니다.
>[!div class="nextstepaction"]
>[캡처 디바이스 동기화](capture-device-synchronization.md)

또한 다음을 검토할 수 있습니다.

[좌표계](coordinate-systems.md)
