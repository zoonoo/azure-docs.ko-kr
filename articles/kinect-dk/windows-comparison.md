---
title: Azure Kinect 진한 Windows 비교
description: Windows v2 용 Azure Kinect 진한 및 Kinect 간의 하드웨어 및 소프트웨어 차이점
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, 비교, SDK, 차이점, 하드웨어, 소프트웨어
ms.openlocfilehash: ec105cef0c52b02c763487fbe5b2d8c018315f4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67452515"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect and Kinect Windows v2 비교

Azure Kinect 진한 하드웨어 및 소프트웨어 개발 키트는 Windows v 2의 Kinect과 차이가 있습니다. 모든 기존 Kinect for Windows v2 응용 프로그램은 Azure Kinect 진한에서 직접 작동 하지 않으며 새 SDK로 이식 해야 합니다.  

## <a name="hardware"></a>하드웨어

다음 표에는 Azure Kinect 개발 키트와 Windows v 2 용 Kinect의 상위 수준 차이가 나와 있습니다.

|    |      | Azure Kinect DK |  Windows v2 용 Kinect |
|----------|---------------|--------| ------------|
| **비디오** | 설명  | 7-mic 순환 배열 | 4-mic 선형 단계별 배열 |
| **동작 센서** | 설명 | 3 축가 속도계 3 축 gyro | 3 축가 속도계 |
| **RGB 카메라**    | 설명 | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **깊이 카메라**  | 메서드   | 비행 시간 | 비행 시간 |
|                   | 해결 방법 | 640 x 576 px @30 fps | 512 x 424 px @ 30fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **연결** | 데이터 | Usb 3.1 Gen 1 유형 (USB-C)  | USB 3.1 gen 1|
|  | 고급 | 외부 PSU 또는 USB-C | 외부 PSU |
|  | 동기화 | RGB & 깊이 내부, 외부 장치-장치| RGB & 깊이 내부용 |
| **기계적** | 차원 | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | 편지 | 440 g | 970 g |
| | 장착 | 1 1/4-20 UNC. 4 개의 내부 스크류 점수 | 1 1/4-20 UNC |

[Azure KINECT 진한 하드웨어](hardware-specification.md) 문서에서 추가 세부 정보를 확인 합니다.

## <a name="sensor-access"></a>센서 액세스

다음 표에서는 하위 수준 장치 센서 액세스 기능 비교를 제공 합니다.

| **기능**| **Azure Kinect** | **Windows 용 Kinect** | **참고** |
|---------|---------|------------|---------|
| **Depth** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **색상** | ✔️ | ✔️ | 색 형식은 차이점을 지원 합니다. Azure Kinect는 이러한 카메라 컨트롤 (노출, 흰색 밸런스, 밝기, 대비, 채도, 선명도 및 게인 제어)을 지원 합니다. |
| **비디오** | ✔️ | ✔️ | Azure Kinect 진한 mics는 음성 SDK 또는 Windows 네이티브 API를 통해 액세스 됩니다. |
| **IMU** | ✔️ |  | Azure Kinect 진한은 전체 6 축 IMU 및 Windows 용 Kinect는 1 축만 제공 합니다. |
| **보정 데이터** | ✔️ | ✔️ | OpenCV 호환 카메라 모델 보정 |
| **깊이-RGB 내부 동기화** | ✔️ | ✔️ |  |
| **외부 동기화**| ✔️|  | Azure Kinect 진한 외부 동기화에 대 한 프로그래밍 가능한 지연 |
| **여러 클라이언트를 사용 하 여 액세스 공유** | | ✔️ | Azure Kinect 센서 SDK는 WinUSB/libUSB를 사용 하 여 장치에 액세스 하 고 여러 프로세스에서 장치 액세스를 공유할 수 있도록 하는 서비스를 구현 하지 않습니다. |
| **스트림 레코드/재생 도구** | ✔️ | ✔️ | Azure Kinect는 오픈 소스 Matroska 컨테이너 기반 구현을 사용 합니다. |

## <a name="features"></a>기능

Azure Kinect SDK 기능 집합은 아래에 설명 된 대로 Windows v2의 Kinect와 다릅니다.

| **Kinect v2 기능** | **Kinect v2 데이터 형식** | **Azure Kinect SDK/서비스** |
|--------|--------|------|
| 센서 데이터 액세스 |DepthFrame| [센서 SDK-이미지 검색](retrieve-images.md) 
| |InfraredFrame | [센서 SDK-이미지 검색](retrieve-images.md) 
| | ColorFrame | [센서 SDK-이미지 검색](retrieve-images.md) | 
| | AudioBeamFrame |현재 지원되지 않음 
| 본문 추적 | BodyFrame | 본문 추적 SDK |
| | BodyIndexFrame | 본문 추적 SDK  |
| 좌표 매핑|CoordinateMapper| [센서 SDK-이미지 변환](use-image-transformation.md) |
|얼굴 추적 | FaceFrame | [Cognitive Services: 얼굴](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    음성 인식    |    해당 없음                      |    [Cognitive Services: Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>다음 단계

[Kinect for Windows developer pages](https://developer.microsoft.com/windows/kinect)
