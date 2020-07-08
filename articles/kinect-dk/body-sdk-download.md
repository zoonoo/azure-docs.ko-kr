---
title: Azure Kinect 본문 추적 SDK 다운로드
description: Windows 또는 Linux에서 각 버전의 Azure Kinect 센서 SDK를 다운로드 하는 방법을 이해 합니다.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, sdk, 다운로드 업데이트, 최신, 사용 가능, 설치, 본문, 추적
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277068"
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
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linux 설치 지침

현재 유일하게 지원되는 배포는 Ubuntu 18.04입니다. 다른 배포에 대한 지원을 요청하려면 [이 페이지](https://aka.ms/azurekinectfeedback)를 참조하세요.

먼저 [여기](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software) 지침에 따라 [Microsoft의 패키지 리포지토리](https://packages.microsoft.com/)를 구성해야 합니다.

`libk4abt<major>.<minor>-dev` 패키지에는 `libk4abt`에 대해 빌드할 헤더와 CMake 파일이 포함되어 있습니다.
패키지에는 `libk4abt<major>.<minor>` 에 종속 된 실행 파일 `libk4abt` 및 예제 뷰어를 실행 하는 데 필요한 공유 개체가 포함 되어 있습니다.

기본 자습서에는 `libk4abt<major>.<minor>-dev` 패키지가 필요합니다. 설치하려면 다음을 실행합니다.

`sudo apt install libk4abt1.0-dev`

명령이 성공하면 SDK를 사용할 준비가 된 것입니다.

> [!NOTE]
> SDK를 설치할 때 설치 경로를 기억하세요. 예를 들어 "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0"입니다. 이 경로에 있는 문서에서 참조 되는 샘플을 찾을 수 있습니다.
> 본문 추적 샘플은 Kinect 리포지토리의 [본문 추적-샘플](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) 폴더에 있습니다. 문서에서 참조 되는 샘플은 여기에서 찾을 수 있습니다.

## <a name="change-log"></a>로그 변경

### <a name="v101"></a>v 1.0.1
* [버그 수정] Windows 빌드 19025 이상에서 경로에서 onnxruntime.dll 로드 하는 경우 SDK가 충돌 하는 문제를 해결 합니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* 기능과 Msi 설치 관리자에 c # 래퍼를 추가 합니다.
* [버그 수정] 헤드 회전을 제대로 검색할 수 없는 문제를 해결 합니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [버그 수정] Linux 컴퓨터에서 CPU 사용량이 최대 100%까지 발생 하는 문제를 해결 합니다. [링크](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* 표본의 샘플 리포지토리에 두 개의 샘플을 추가 합니다. 샘플 1에서는 깊이 공간에서 색 공간 [링크로](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)본문 추적 결과를 변환 하는 방법을 보여 줍니다. 샘플 2에서는 바닥 평면 [링크](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) 를 검색 하는 방법을 보여 줍니다.

### <a name="v095"></a>v 0.9.5
* 기능과 C # 지원 C # 래퍼가 nuget 패키지에 압축 됩니다.
* 기능과 다중 추적기 지원. 여러 추적기를 만들 수 있습니다. 이제 사용자는 여러 개의 추적기를 만들어 여러 Azure Kinect 장치에서 본문을 추적할 수 있습니다.
* 기능과 CPU 모드에 대 한 다중 스레드 처리 지원. CPU 모드에서 실행 하는 경우 모든 코어를 사용 하 여 속도를 최대화 합니다.
* 기능과 `gpu_device_id`구조체에를 추가 `k4abt_tracker_configuration_t` 합니다. 사용자가 기본이 아닌 GPU 장치를 지정 하 여 본문 추적 알고리즘을 실행할 수 있습니다.
* [버그 수정/주요 변경] 조인트 이름에서 오타를 수정 합니다. 에서로의 조인트 이름을 변경 `K4ABT_JOINT_SPINE_NAVAL` `K4ABT_JOINT_SPINE_NAVEL` 합니다.

### <a name="v094"></a>v 0.9.4
* 기능과 손으로 이루어진 지지대를 추가 합니다. SDK는 손 모양, 수동 팁, 엄지 세 가지 추가 조인트를 위한 정보를 제공 합니다.
* 기능과 검색 된 각 조인트에 대해 예측 신뢰 수준을 추가 합니다.
* 기능과 CPU 모드 지원을 추가 합니다. 이제에서 값을 변경 하 여 `cpu_only_mode` `k4abt_tracker_configuration_t` SDK를 CPU 모드에서 실행할 수 있습니다 .이 경우 사용자에 게 강력한 그래픽 카드가 필요 하지 않습니다.

### <a name="v093"></a>v 0.9.3
* 기능과 DNN 새 dnn_model_2_0 모델을 게시 합니다. onnx는 주로 본문 추적의 견고성을 향상 시킵니다.
* 기능과 기본적으로 임시 다듬기를 사용 하지 않도록 설정 합니다. 추적 된 조인트의 응답성이 향상 됩니다.
* 기능과 본문 인덱스 맵의 정확도를 높입니다.
* [버그 수정] 센서 방향 설정이 적용 되지 않는 버그를 수정 합니다.
* [버그 수정] Body_index_map 형식을 K4A_IMAGE_FORMAT_CUSTOM에서 K4A_IMAGE_FORMAT_CUSTOM8으로 변경 합니다.
* [알려진 문제] 두 개의 닫는 본문은 단일 인스턴스 세그먼트로 병합 될 수 있습니다.

### <a name="v092"></a>v 0.9.2
* [주요 변경 내용] 최신 Azure Kinect 센서 SDK 1.2.0에 따라 업데이트 합니다.
* [API 변경] `k4abt_tracker_create` 함수에서 입력을 시작 `k4abt_tracker_configuration_t` 합니다. 
* [API 변경] `k4abt_frame_get_timestamp_usec`API를에 `k4abt_frame_get_device_timestamp_usec` 더 구체적이 고 센서 SDK 1.2.0와 일치 하도록 변경 합니다.
* 기능과 다른 각도로 탑재할 때 더 정확한 본문 추적 결과를 얻기 위해 추적기를 만들 때 센서 장착 방향을 지정할 수 있습니다.
* 기능과 새 API `k4abt_tracker_set_temporal_smoothing` 를 제공 하 여 사용자가 적용 하려는 임시 다듬기의 양을 변경 합니다.
* 기능과 C + + 래퍼 k4abt를 추가 합니다.
* 기능과 버전 정의 헤더 k4abtversion를 추가 합니다.
* [버그 수정] 매우 높은 CPU 사용량을 일으킨 버그를 수정 합니다.
* [버그 수정] 로 거 충돌 버그를 수정 합니다.

### <a name="v091"></a>v 0.9.1
* [버그 수정] 추적기를 삭제 하는 경우 메모리 누수 해결
* [버그 수정] 누락 된 종속성에 대 한 더 나은 오류 메시지
* [버그 수정] 두 번째 tracker 인스턴스를 만들 때 충돌 없이 실패 함
* [버그 수정] 로 거 환경 변수가 이제 제대로 작동 합니다.
* Linux 지원

### <a name="v090"></a>v 0.9.0

* [주요 변경 내용] SDK 종속성 10.0을 이상으로 다운 그레이드 합니다 (CDA 10.1에서). ONNX runtime은 공식적으로 최대의 DA 10.0을 지원 합니다.
* [주요 변경 내용] Tensorflow 런타임 대신 ONNX 런타임으로 전환 되었습니다. 첫 번째 프레임 시작 시간 및 메모리 사용을 줄입니다. 또한 SDK 이진 크기를 줄입니다.
* [API 변경] 이름 `k4abt_tracker_queue_capture()` 변경 됨`k4abt_tracker_enqueue_capture()`
* [API 변경] `k4abt_frame_get_body()`및의 두 가지 별도 함수로 `k4abt_frame_get_body_skeleton()` 중단 `k4abt_frame_get_body_id()` 됩니다. 이제 전체 해골 구조를 항상 복사 하지 않고 본문 ID를 쿼리할 수 있습니다.
* [API 변경] `k4abt_frame_get_timestamp_usec()`사용자가 본문 프레임 타임 스탬프를 쿼리 하는 단계를 간소화 하는 함수를 추가 했습니다.
* 본문 추적 알고리즘 정확도 및 추적 안정성 향상

## <a name="next-steps"></a>다음 단계

- [Azure Kinect 진한 개요](about-azure-kinect-dk.md)

- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)

- [Azure Kinect 본문 추적 설정](body-sdk-setup.md)
