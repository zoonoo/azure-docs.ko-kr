---
title: Azure Kinect 본문 추적 SDK 다운로드
description: Windows 또는 Linux에서 각 버전의 Azure Kinect 센서 SDK를 다운로드 하는 방법을 이해 합니다.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: azure, kinect, sdk, 다운로드 업데이트, 최신, 사용 가능, 설치, 본문, 추적
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654495"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Azure Kinect Body 추적 SDK 다운로드

이 문서에서는 Azure Kinect Body 추적 SDK의 각 버전을 설치 하기 위한 링크를 제공 합니다.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect 본문 추적 SDK 콘텐츠

- 헤더 및 라이브러리를 사용 하 여 Azure Kinect 진한 본문 추적 응용 프로그램을 빌드합니다.
- Azure Kinect 진한를 사용 하는 본문 추적 응용 프로그램에 필요한 재배포 가능 Dll.
- 샘플 본문 추적 응용 프로그램입니다.

## <a name="windows-download-links"></a>Windows 다운로드 링크

버전       | 다운로드
--------------|----------
1.1.0 | [i](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linux 설치 지침

현재 유일 하 게 지원 되는 배포는 Ubuntu 18.04 및 20.04입니다. 다른 배포에 대한 지원을 요청하려면 [이 페이지](https://aka.ms/azurekinectfeedback)를 참조하세요.

먼저 [여기](/windows-server/administration/linux-package-repository-for-microsoft-software) 지침에 따라 [Microsoft의 패키지 리포지토리](https://packages.microsoft.com/)를 구성해야 합니다.

`libk4abt<major>.<minor>-dev` 패키지에는 `libk4abt`에 대해 빌드할 헤더와 CMake 파일이 포함되어 있습니다.
패키지에는 `libk4abt<major>.<minor>` 에 종속 된 실행 파일 `libk4abt` 및 예제 뷰어를 실행 하는 데 필요한 공유 개체가 포함 되어 있습니다.

기본 자습서에는 `libk4abt<major>.<minor>-dev` 패키지가 필요합니다. 설치하려면 다음을 실행합니다.

`sudo apt install libk4abt<major>.<minor>-dev`

명령이 성공하면 SDK를 사용할 준비가 된 것입니다.

> [!NOTE]
> SDK를 설치할 때 설치 경로를 기억하세요. 예를 들어 "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0"입니다. 이 경로에 있는 문서에서 참조 되는 샘플을 찾을 수 있습니다.
> 본문 추적 샘플은 Kinect 리포지토리의 [본문 추적-샘플](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) 폴더에 있습니다. 문서에서 참조 되는 샘플은 여기에서 찾을 수 있습니다.

## <a name="change-log"></a>로그 변경

### <a name="v110"></a>v 1.1.0
* 기능과 DirectML (Windows에만 해당)에 대 한 지원과 포즈 예측 모델의 TensorRT 실행을 추가 합니다. 새 실행 환경에 대 한 FAQ를 참조 하세요.
* 기능과 `model_path` 구조체에를 추가 `k4abt_tracker_configuration_t` 합니다. 사용자가 포즈 예측 모델의 경로 이름을 지정할 수 있습니다. 기본값은 `dnn_model_2_0_op11.onnx` 현재 디렉터리에 있는 표준 포즈 추정 모델입니다.
* 기능과 `dnn_model_2_0_lite_op11.onnx` Lite 포즈 예측 모델을 포함 합니다. 이 모델은 ~ 5% 정확도 감소에 대해 최대 2 배의 성능을 향상 시킵니다.
* 기능과 확인 된 샘플 Visual Studio 2019 및 업데이트 샘플로 컴파일하여이 릴리스를 사용 합니다.
* [주요 변경 내용] CPU 11.1, Azure (Windows 전용) 및 TensorRT 7.2.1 실행 환경에 대 한 지원을 포함 하는 ONNX Runtime 1.6로 업데이트 합니다. R455 이상의 NVIDIA 드라이버 업데이트가 필요 합니다.
* [주요 변경 내용] NuGet이 설치 되어 있지 않습니다.
* [버그 수정] NVIDIA RTX 30xx 시리즈 Gpu [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) 에 대 한 지원 추가
* [버그 수정] AMD 및 Intel integrated Gpu에 대 한 지원 추가 (Windows에만 해당) [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [버그 수정] 이 [링크로](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125) 11.1 업데이트
* [버그 수정] 센서 SDK 1.4.1로 업데이트 [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v 1.0.1
* [버그 수정] Windows 빌드 19025 이상에서 경로에서 onnxruntime.dll 로드 하는 경우 SDK가 충돌 하는 문제를 해결 합니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* 기능과 Msi 설치 관리자에 c # 래퍼를 추가 합니다.
* [버그 수정] 헤드 회전을 제대로 검색할 수 없는 문제를 해결 합니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [버그 수정] Linux 컴퓨터에서 CPU 사용량이 최대 100%까지 발생 하는 문제를 해결 합니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* 표본의 샘플 리포지토리에 두 개의 샘플을 추가 합니다. 샘플 1에서는 깊이 공간에서 색 공간 [링크로](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)본문 추적 결과를 변환 하는 방법을 보여 줍니다. 샘플 2에서는 바닥 평면 [링크](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) 를 검색 하는 방법을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

- [Azure Kinect 진한 개요](about-azure-kinect-dk.md)

- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)

- [Azure Kinect 본문 추적 설정](body-sdk-setup.md)