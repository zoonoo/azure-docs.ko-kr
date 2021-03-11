---
title: Windows의 Linux에 대 한 Azure IoT Edge 정의 | Microsoft Docs
description: Windows 10 장치에서 Linux IoT Edge 모듈을 실행할 수 있는 개요
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 63538661cceb7e1bdd4151a3592ccbd4de0033b0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612017"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Windows에서 Linux용 Azure IoT Edge란?(미리 보기)

Windows에서 Linux에 대 한 Azure IoT Edge를 사용 하면 windows IoT 배포에서 Windows 응용 프로그램과 함께 컨테이너 화 된 Linux 워크 로드를 실행할 수 있습니다. Windows IoT를 사용 하 여에 지 장치를 구동 하는 기업은 이제 Linux에서 빌드된 클라우드 네이티브 분석 솔루션을 활용할 수 있습니다.

Windows에서 Linux에 대 한 IoT Edge은 Windows 장치에서 Linux 가상 머신을 실행 하는 방식으로 작동 합니다. Linux 가상 머신은 IoT Edge 런타임과 함께 미리 설치 되어 제공 됩니다. 장치에 배포 된 모든 IoT Edge 모듈은 가상 머신 내에서 실행 됩니다. 한편 Windows 호스트 장치에서 실행 되는 Windows 응용 프로그램은 Linux 가상 머신에서 실행 되는 모듈과 통신할 수 있습니다.

지금 미리 보기를 [시작](how-to-install-iot-edge-on-windows.md) 하세요.

>[!NOTE]
>IoT Edge 배경 및 목표에 따라 Windows에서 Linux에 대 한 Azure IoT Edge 개선 하는 데 도움이 되는 [제품 설문 조사](https://aka.ms/AzEFLOW-Registration) 를 수행 하는 것을 고려 하세요. 이 설문 조사를 사용 하 여 Windows 공지에서 Linux에 대 한 향후 Azure IoT Edge에 등록할 수도 있습니다.

## <a name="components"></a>구성 요소

Windows에서 Linux에 대 한 IoT Edge 다음 구성 요소를 사용 하 여 Linux 및 Windows 작업을 서로 함께 실행 하 고 원활 하 게 통신할 수 있습니다.

* **Azure IoT Edge를 실행 하는 linux 가상 컴퓨터**: Microsoft 자사 [Mariner](https://github.com/microsoft/CBL-Mariner) 운영 체제를 기반으로 하는 linux 가상 머신은 IoT Edge 런타임으로 빌드되고 IoT Edge 워크 로드에 대해 계층 1 지원 환경으로 유효성이 검사 됩니다.

* **Windows 관리 센터**: Windows 관리 센터에 대 한 IoT Edge 확장은 Linux 가상 머신에서 IoT Edge의 설치, 구성 및 진단을 용이 하 게 합니다. Windows 관리 센터는 Windows에서 Linux에 대 한 IoT Edge를 로컬 장치에 배포 하거나 대상 장치에 연결 하 여 원격으로 관리할 수 있습니다.

* **Microsoft 업데이트**: Microsoft 업데이트와 통합 하면 Windows 런타임 구성 요소, MARINER Linux VM 및 IoT Edge 최신 상태로 유지 됩니다.

![Windows 및 Linux VM은 병렬로 실행 되지만 Windows 관리 센터는 두 구성 요소를 모두 제어 합니다.](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows 프로세스와 Linux 가상 머신 간의 양방향 통신은 Windows 프로세스가 Linux 컨테이너에서 실행 되는 워크 로드에 대 한 사용자 인터페이스 또는 하드웨어 프록시를 제공할 수 있음을 의미 합니다.

## <a name="samples"></a>샘플

Windows에서 Linux에 대 한 IoT Edge Linux 및 Windows 구성 요소 간의 상호 운용성을 강조 합니다.

Windows 응용 프로그램 및 IoT Edge 모듈 간의 통신을 보여 주는 샘플은 [windows 10 IoT 샘플](https://github.com/microsoft/Windows-IoT-Samples)을 참조 하세요.

## <a name="public-preview"></a>퍼블릭 미리 보기

Windows에서 Linux에 대 한 IoT Edge는 현재 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공 됩니다. 설치 및 관리 프로세스는 일반적으로 사용할 수 있는 기능의 경우와 다를 수 있습니다.

## <a name="support"></a>지원

IoT Edge 지원 및 피드백 채널을 사용 하 여 Windows에서 Linux 용 IoT Edge에 대 한 지원을 받을 수 있습니다.

**버그 보고** -IoT Edge 오픈 소스 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues) 에서 버그를 보고할 수 있습니다. Windows에서 Linux에 대 한 Azure IoT Edge 관련 된 버그는 [iotedge-eflow 문제 페이지](https://github.com/azure/iotedge-eflow/issues)에서 보고할 수 있습니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="next-steps"></a>다음 단계

[Windows 10 IoT Enterprise에서 Linux에 대 한 IoT Edge를](https://aka.ms/EFLOWPPC9) 시청 하 고 작동 하는 샘플을 확인 하세요.

Windows에서 Linux 용 [Azure IoT Edge 설치 및 프로 비전](how-to-install-iot-edge-on-windows.md) 의 단계에 따라 Windows에서 linux 용 IoT Edge를 사용 하 여 장치를 설정 합니다.
