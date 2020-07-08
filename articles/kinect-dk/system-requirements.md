---
title: Azure Kinect 센서 SDK 시스템 요구 사항
description: Windows 및 Linux에서 Azure Kinect 센서 SDK에 대 한 시스템 요구 사항을 이해 합니다.
author: tesych
ms.author: tesych
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/12/2020
ms.topic: article
keywords: azure, kinect, 시스템 요구 사항, CPU, GPU, USB, 설정, 설정, 최소, 요구 사항
ms.openlocfilehash: 5cf313114b62532ee3f2b3d7a5142f79218954c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277076"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect 센서 SDK 시스템 요구 사항

이 문서에서는 센서 SDK를 설치 하 고 Azure Kinect를 성공적으로 배포 하는 데 필요한 시스템 요구 사항에 대 한 세부 정보를 제공 합니다.

## <a name="supported-operating-systems-and-architectures"></a>지원되는 운영 체제 및 아키텍처

- Windows 10 4 월 2018 (버전 1803, OS 빌드 17134) 릴리스 (x64) 이상 버전
- Linux Ubuntu 18.04 (x64), OpenGLv 4.4 이상 버전을 사용 하는 GPU 드라이버 사용

센서 SDK는 네이티브 C/c + + Windows 응용 프로그램에 대 한 Windows API (Win32)에 사용할 수 있습니다. SDK는 현재 UWP 응용 프로그램에서 사용할 수 없습니다. S 모드의 Windows 10에는 Azure Kinect 진한이 지원 되지 않습니다.

## <a name="development-environment-requirements"></a>개발 환경 요구 사항

센서 SDK 개발에 참여 하려면 [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)를 참조 하세요.

## <a name="minimum-host-pc-hardware-requirements"></a>최소 호스트 PC 하드웨어 요구 사항

PC 호스트 하드웨어 요구 사항은 호스트 PC에서 실행 되는 응용 프로그램/알고리즘/센서 프레임 요금/해상도에 따라 달라 집니다. Windows에 권장 되는 최소 센서 SDK 구성은 다음과 같습니다.

- 일곱째 Gen Intel &reg; coretm I3 프로세서 (HD620 GPU 이상이 포함 된 듀얼 코어 2.4 g h z)
- 4gb 메모리
- 전용 USB3 포트
- OpenGL 4.4 또는 DirectX 11.0에 대 한 그래픽 드라이버 지원

사용 사례에 따라 낮은 종료 Cpu와 이전 Cpu도 작동할 수 있습니다.

Windows/Linux 운영 체제와 사용 중인 그래픽 드라이버 사이에도 성능이 다릅니다.

## <a name="body-tracking-host-pc-hardware-requirements"></a>본문 추적 호스트 PC 하드웨어 요구 사항

본문 추적 PC 호스트 요구 사항은 일반 PC 호스트 요구 사항 보다 더 엄격 합니다. Windows에 권장 되는 최소 본문 추적 SDK 구성은 다음과 같습니다.

- 일곱 번째 Gen Intel &reg; coretm I5 프로세서 (쿼드 코어 2.4 g h z 이상)
- 4gb 메모리
- NVIDIA GEFORCE GTX 1070 이상
- 전용 USB3 포트

권장 되는 최소 구성은 30fps 추적 5 명에서 K4A_DEPTH_MODE_NFOV_UNBINNED 깊이 모드를 가정 합니다. 사용 사례에 따라 낮은 종료 Cpu 및 NVIDIA Gpu도 작동할 수 있습니다.

## <a name="usb3"></a>USB3

USB 호스트 컨트롤러에는 알려진 호환성 문제가 있습니다. [문제 해결 페이지](troubleshooting.md#usb3-host-controller-compatibility) 에서 자세한 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Kinect 진한 개요](about-azure-kinect-dk.md)

- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)

- [Azure Kinect 본문 추적 설정](body-sdk-setup.md)
