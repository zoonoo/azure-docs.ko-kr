---
title: Azure IoT Hub 에이전트에 대 한 장치 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 에이전트에 대 한 장치 업데이트를 이해 합니다.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e932238849baf267983fb3ca1ebb082db169d9fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679600"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>IoT Hub 에이전트에 대한 디바이스 업데이트 개요

장치 업데이트 에이전트는 다음과 같은 두 가지 개념적 계층으로 구성 됩니다.

* 인터페이스 계층은 장치가 장치 업데이트 에이전트와 장치 업데이트 서비스 간에 전달 될 수 있도록 하는 [Azure IoT 플러그 앤 플레이 (PnP)](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play) 를 기반으로 빌드됩니다.
* 플랫폼 계층은 플랫폼 또는 장치별 일 수 있는 다운로드, 설치 및 적용의 상위 수준 업데이트 작업을 담당 합니다.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="에이전트 구현." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>인터페이스 계층

인터페이스 계층은 [ADU Core 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) 및 [장치 정보 인터페이스로](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)구성 됩니다.

이러한 인터페이스는 기본값에 대 한 구성 파일을 사용 합니다. 기본값에는 AzureDeviceUpdateCore 인터페이스 및 모델에 대 한 aduc_manufacturer 및 aduc_model와 DeviceInformation 인터페이스의 제조업체가 포함 됩니다. 구성 파일에 [대해 자세히 알아보세요](device-update-configuration-file.md) .

### <a name="adu-core-interface"></a>ADU Core 인터페이스

' ADU Core ' 인터페이스는 장치 업데이트 에이전트와 서비스 간의 기본 통신 채널입니다. 이 인터페이스에 [대해 자세히 알아보세요](device-update-plug-and-play.md#adu-core-interface) .

### <a name="device-information-interface"></a>장치 정보 인터페이스

장치 정보 인터페이스는 인터페이스를 구현 하는 데 사용 됩니다 `Azure IoT PnP DeviceInformation` . 이 인터페이스에 [대해 자세히 알아보세요](device-update-plug-and-play.md#device-information-interface) .

## <a name="the-platform-layer"></a>플랫폼 계층

플랫폼 계층에는 두 가지 구현이 있습니다. 시뮬레이터 플랫폼 계층은 업데이트 작업을 간단 하 게 구현 하며 IoT Hub 서비스 및 설치를 위한 장치 업데이트를 신속 하 게 테스트 하 고 평가 하는 데 사용 됩니다. 시뮬레이터 플랫폼 계층을 사용 하 여 장치 업데이트 에이전트를 빌드하는 경우이를 장치 업데이트 시뮬레이터 에이전트 또는 단지 시뮬레이터로 지칭 합니다. 시뮬레이터 에이전트를 사용 하는 방법에 [대해 자세히 알아보세요](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) . Linux 플랫폼 계층은 다운로드를 위해 [배달 최적화](https://github.com/microsoft/do-client) 프로그램과 통합 되며, Raspberry Pi 참조 이미지 및 linux 시스템에서 실행 되는 모든 클라이언트에 사용 됩니다.

### <a name="simulator-platform-layer"></a>시뮬레이터 플랫폼 계층

시뮬레이터 플랫폼 계층 구현은에서 찾을 수 있으며 `src/platform_layers/simulator_platform_layer` , IoT Hub에 대 한 장치 업데이트를 테스트 하 고 평가 하는 데 사용할 수 있습니다.  "시뮬레이터" 구현의 많은 작업은 IoT Hub에 대 한 장치 업데이트를 실험 하기 위한 물리적인 변화를 줄이기 위해 모의 됩니다.  이 플랫폼 계층을 사용 하 여 종단 간 "시뮬레이트된" 업데이트를 수행할 수 있습니다. 시뮬레이터 에이전트 실행에 [대해 자세히 알아보세요](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) .

### <a name="linux-platform-layer"></a>Linux 플랫폼 계층

Linux 플랫폼 계층 구현은에서 찾을 수 있으며, `src/platform_layers/linux_platform_layer` 다운로드를 위해 [배달 최적화 클라이언트](https://github.com/microsoft/do-client/releases) 와 통합 되며, Raspberry Pi 참조 이미지 및 Linux 시스템에서 실행 되는 모든 클라이언트에 사용 됩니다.

이 계층을 다른 업데이트 처리기와 통합 하 여 설치 관리자를 구현할 수 있습니다. 예를 들어 `SWUpdate` 업데이트 처리기는 `SWUpdate` 업데이트를 수행 하기 위해 실행 파일을 호출 하는 셸 스크립트를 호출 합니다.

## <a name="update-handlers"></a>업데이트 처리기

업데이트 처리기는 업데이트의 콘텐츠 또는 설치 관리자 관련 부분을 처리 하는 구성 요소입니다. 업데이트 처리기 구현은에 `src/content_handlers` 있습니다.

### <a name="simulator-update-handler"></a>시뮬레이터 업데이트 처리기

시뮬레이터 업데이트 처리기는 시뮬레이터 플랫폼 계층에서 사용 되며, 콘텐츠 처리기와의 상호 작용을 위해 Linux 플랫폼 계층과 함께 사용할 수 있습니다. 시뮬레이터 업데이트 처리기는 대부분의 ops를 사용 하 여 업데이트 처리기 Api를 구현 합니다. 시뮬레이터 업데이트 처리기의 구현은 아래에서 찾을 수 있습니다.
* [이미지 업데이트 시뮬레이터](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [패키지 업데이트 apt 시뮬레이터](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

참고: AzureDeviceUpdateCore PnP 인터페이스의 InstalledCriteria 필드는 콘텐츠의 sha256 해시 여야 합니다. 이것은 [가져오기 매니페스트 개체](import-update.md#create-device-update-import-manifest)에 있는 것과 동일한 해시입니다. 및 인터페이스에 [대해 자세히 알아보세요](device-update-plug-and-play.md) `installedCriteria` `AzureDeviceUpdateCore` .

### <a name="swupdate-update-handler"></a>`SWUpdate` 업데이트 처리기

`SWUpdate`"업데이트 처리기"는 `SWUpdate` 명령줄 실행 파일 및 기타 셸 명령과 통합 되어 Raspberry Pi 참조 이미지에 대해 특별히 A/B 업데이트를 구현 합니다. [여기](https://github.com/Azure/iot-hub-device-update/releases)에서 최신 Raspberry Pi 참조 이미지를 찾습니다. `SWUpdate`업데이트 처리기는 [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler)에서 구현 됩니다.

### <a name="apt-update-handler"></a>APT 업데이트 처리기

APT 업데이트 처리기는 APT 관련 업데이트 매니페스트를 처리 하 고 APT를 호출 하 여 지정 된 Debian 패키지를 설치 하거나 업데이트 합니다.

## <a name="self-update-device-update-agent"></a>장치 업데이트 에이전트 자동 업데이트

장치 업데이트 에이전트 및 해당 종속성은 IoT Hub 파이프라인에 대 한 장치 업데이트를 통해 업데이트할 수 있습니다. 이미지 기반 업데이트를 사용 하는 경우 새 이미지에 최신 장치 업데이트 에이전트를 포함 합니다. 패키지 기반 업데이트를 사용 하는 경우 다른 패키지와 마찬가지로 apt 매니페스트에 장치 업데이트 에이전트와 원하는 버전을 포함 합니다. Apt manifest에 [대해 자세히 알아보세요](device-update-apt-manifest.md) . [IoT 장치](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)쌍의 장치 속성 섹션에서 설치 된 버전의 장치 업데이트 에이전트와 배달 최적화 에이전트를 확인할 수 있습니다. [ADU Core 인터페이스에서 장치 속성에 대해 자세히 알아보세요](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>다음 단계
[장치 업데이트 에이전트 구성 파일 이해](device-update-configuration-file.md)

