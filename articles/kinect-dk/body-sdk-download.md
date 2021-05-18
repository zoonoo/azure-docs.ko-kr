---
title: Azure Kinect 신체 추적 SDK 다운로드
description: Windows 또는 Linux에서 Azure Kinect 센서 SDK의 각 버전을 다운로드하는 방법을 이해합니다.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: azure, kinect, sdk, 다운로드 업데이트, 최신, 사용 가능, 설치, 신체, 추적
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654495"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Azure Kinect 신체 추적 SDK 다운로드

이 문서에서는 Azure Kinect 신체 추적 SDK의 각 버전을 설치하기 위한 링크를 제공합니다.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect 신체 추적 SDK 콘텐츠

- Azure Kinect DK를 사용하는 신체 추적 애플리케이션을 빌드하기 위한 헤더 및 라이브러리입니다.
- Azure Kinect DK를 사용하는 신체 추적 애플리케이션에 필요한 재배포 가능 DLL입니다.
- 샘플 신체 추적 애플리케이션입니다.

## <a name="windows-download-links"></a>Windows 다운로드 링크

Version       | 다운로드
--------------|----------
1.1.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linux 설치 지침

현재 유일하게 지원되는 배포는 Ubuntu 18.04 및 20.04입니다. 다른 배포에 대한 지원을 요청하려면 [이 페이지](https://aka.ms/azurekinectfeedback)를 참조하세요.

먼저 [여기](/windows-server/administration/linux-package-repository-for-microsoft-software) 지침에 따라 [Microsoft의 패키지 리포지토리](https://packages.microsoft.com/)를 구성해야 합니다.

`libk4abt<major>.<minor>-dev` 패키지에는 `libk4abt`에 대해 빌드할 헤더와 CMake 파일이 포함되어 있습니다.
`libk4abt<major>.<minor>` 패키지에는 뷰어 예뿐만 아니라 `libk4abt`에 종속된 실행 파일을 실행하는 데 필요한 공유 개체가 포함되어 있습니다.

기본 자습서에는 `libk4abt<major>.<minor>-dev` 패키지가 필요합니다. 설치하려면 다음을 실행합니다.

`sudo apt install libk4abt<major>.<minor>-dev`

명령이 성공하면 SDK를 사용할 준비가 된 것입니다.

> [!NOTE]
> SDK를 설치할 때 설치 경로를 기억하세요. 예를 들어 "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0"입니다. 문서에서 참조하는 샘플을 이 경로에서 찾을 수 있습니다.
> 신체 추적 샘플은 Azure-Kinect-Samples 리포지토리의 [body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) 폴더에 있습니다. 문서에서 참조하는 샘플을 여기에서 찾을 수 있습니다.

## <a name="change-log"></a>로그 변경

### <a name="v110"></a>v1.1.0
* [기능] DirectML(Windows에만 해당)에 대한 지원과 포즈 예측 모델의 TensorRT 실행이 추가되었습니다. 새 실행 환경에 대한 FAQ를 참조하세요.
* [기능] `model_path`가 `k4abt_tracker_configuration_t` 구조에 추가되었습니다. 사용자가 포즈 예측 모델에 대한 경로 이름을 지정할 수 있습니다. 기본값은 현재 디렉터리에 있는 표준 포즈 추정 모델 `dnn_model_2_0_op11.onnx`입니다.
* [기능] `dnn_model_2_0_lite_op11.onnx` 라이트 포즈 예측 모델이 포함되었습니다. 이 모델은 최대 5%까지의 정확도 감소로 최대 2배까지의 성능 향상을 제공합니다.
* [기능] 확인된 샘플은 Visual Studio 2019로 컴파일되고 이 릴리스를 사용하도록 샘플을 업데이트합니다.
* [주요 변경 내용] CPU, CUDA 11.1, DirectML(Windows에만 해당), TensorRT 7.2.1 실행 환경에 대한 지원이 포함된 ONNX Runtime 1.6으로 업데이트합니다. R455 이상의 NVIDIA 드라이버 업데이트가 필요합니다.
* [주요 변경 내용] NuGet 설치가 없습니다.
* [버그 수정] NVIDIA RTX 30xx 시리즈 GPU에 대한 지원이 추가되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [버그 수정] AMD 및 Intel 통합 GPU(Windows에만 해당)에 대한 지원이 추가되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [버그 수정] CUDA 11.1로 업데이트되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [버그 수정] 센서 SDK 1.4.1로 업데이트되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v1.0.1
* [버그 수정] Windows 빌드 19025 이상의 경로에서 onnxruntime.dll을 로드하는 경우 SDK가 충돌하는 문제가 수정되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [기능] msi 설치 프로그램에 C# 래퍼가 추가되었습니다.
* [버그 수정] 머리 회전을 제대로 감지할 수 없는 문제가 수정되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [버그 수정] Linux 컴퓨터에서 CPU 사용량이 최대 100%까지 올라가는 문제가 수정되었습니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [샘플] 샘플 리포지토리에 두 개의 샘플이 추가되었습니다. 샘플 1에서는 깊이 공간에서 색상 공간으로 신체 추적 결과를 변환하는 방법을 보여 줍니다. [링크](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample) 샘플 2에서는 바닥 평면을 감지하는 방법을 보여 줍니다. [링크](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

## <a name="next-steps"></a>다음 단계

- [Azure Kinect DK 개요](about-azure-kinect-dk.md)

- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)

- [Azure Kinect 신체 추적 설정](body-sdk-setup.md)