---
title: Azure Kinect DK Windows 비교
description: Azure Kinect DK와 Kinect for Windows v2 간의 하드웨어 및 소프트웨어 차이점
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, 비교, SDK, 차이점, 하드웨어, 소프트웨어
ms.openlocfilehash: 1791767e842043bbb0eda058cc3ea92be90a00fb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078301"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect와 Kinect Windows v2 비교

Azure Kinect DK 하드웨어 및 소프트웨어 개발 키트는 Kinect for Windows v2와 차이가 있습니다. 기존 Kinect for Windows v2 애플리케이션은 Azure Kinect DK에서 직접 작동하지 않으며 새 SDK로 이식해야 합니다.  

## <a name="hardware"></a>하드웨어

다음 표에는 Azure Kinect 개발 키트와 Kinect for Windows v2 간의 상위 수준 차이가 나와 있습니다.

| 기능 | Type | Azure Kinect DK | Kinect for Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **오디오** | 세부 정보  | 7 마이크 원형 배열 | 4 마이크 선형 단계별 배열 |
| **동작 센서** | 세부 정보 | 3축 가속도계 3축 gyro | 3축 가속도계 |
| **RGB 카메라**    | 세부 정보 | 3840 x 2160픽셀 @30 fps | 1920 x 1080픽셀 @30 fps |
| **깊이 카메라**  | 방법   | 비행 시간 | 비행 시간 |
|                   | 해결 방법 | 640 x 576픽셀 @30 fps | 512 x 424픽셀 @ 30 fps |
|                   |            | 512 x 512픽셀 @30 fps |                       |
|                   |            | 1024x1024픽셀 @15 fps |                       |
| **연결** | 데이터 | USB-C 형식의 USB3.1 Gen 1  | USB 3.1 1세대|
|  | 고급 | 외부 PSU 또는 USB-C | 외부 PSU |
|  | 동기화 | RGB 및 깊이 내부, 외부 디바이스 간| RGB 및 깊이 내부 전용 |
| **기계식** | 차원 | 103 x 39 x 126mm | 249 x 66 x 67mm |
|  | Mass | 440g | 970g |
| | 탑재 | ¼-20 UNC 1개. 4개의 내부 스크류 지점 | ¼-20 UNC 1개 |

[Azure Kinect DK 하드웨어](hardware-specification.md) 문서에서 추가 세부 정보를 찾습니다.

## <a name="sensor-access"></a>센서 액세스

다음 표에서는 하위 수준의 디바이스 센서 액세스 기능 비교를 제공합니다.

| **기능**| **Azure Kinect** | **Kinect for Windows** | **참고** |
|---------|---------|------------|---------|
| **깊이** | ✔️ | ✔️ |    | 
| **IR** | ✔️ | ✔️ |  |
| **색상** | ✔️ | ✔️ | 색 형식은 차이점을 지원합니다. Azure Kinect DK는 이러한 카메라 컨트롤(노출, 화이트 밸런스, 밝기, 대비, 채도, 선명도 및 게인 제어)을 지원합니다. |
| **오디오** | ✔️ | ✔️ | Azure Kinect DK 마이크는 Speech SDK 또는 Windows 네이티브 API를 통해 액세스됩니다. |
| **IMU** | ✔️ |  | Azure Kinect DK는 전체 6축 IMU가 있으며 Kinect for Windows는 1축만 제공합니다. |
| **보정 데이터** | ✔️ | ✔️ | OpenCV 호환 카메라 모델 보정 |
| **깊이 RGB 내부 동기화** | ✔️ | ✔️ |  |
| **외부 동기화**| ✔️|  | Azure Kinect DK는 외부 동기화에 대해 프로그래밍 가능한 지연을 허용합니다. |
| **여러 클라이언트와 액세스 공유** | | ✔️ | Azure Kinect 센서 SDK는 WinUSB/libUSB를 사용하여 디바이스에 액세스하고 여러 프로세스와 디바이스 액세스를 공유할 수 있도록 구현된 서비스가 없습니다. |
| **스트림 기록 / 재생 도구** | ✔️ | ✔️ | Azure Kinect DK는 오픈 소스 Matroska 컨테이너 기반 구현을 사용합니다. |

## <a name="features"></a>기능

Azure Kinect SDK 기능 집합은 아래에 설명된 대로 Kinect for Windows v2와 다릅니다.

| **Kinect v2 기능** | **Kinect v2 데이터 형식** | **Azure Kinect SDK/Service** |
|--------|--------|------|
| 센서 데이터 액세스 |DepthFrame| [센서 SDK - 이미지 검색](retrieve-images.md) 
| |InfraredFrame | [센서 SDK - 이미지 검색](retrieve-images.md) 
| | ColorFrame | [센서 SDK - 이미지 검색](retrieve-images.md) | 
| | AudioBeamFrame |현재 지원되지 않음 
| 신체 추적 | BodyFrame | 신체 추적 SDK |
| | BodyIndexFrame | 신체 추적 SDK  |
| 좌표 매핑|CoordinateMapper| [센서 SDK - 이미지 변환](use-image-transformation.md) |
|Face 추적 | FaceFrame | [Cognitive Services: Face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    음성 인식    |    해당 없음                      |    [Cognitive Services: 음성](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>다음 단계

[Kinect for Windows 개발자 페이지](https://developer.microsoft.com/windows/kinect)
