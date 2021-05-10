---
title: 제한된 디바이스에서 IoT 플러그 앤 플레이 사용 | Microsoft Docs
description: Embedded C용 SDK 또는 Azure RTOS를 사용하여 제한된 디바이스에서 IoT 플러그 앤 플레이를 구현하는 방법에 대해 알아봅니다.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfa71e02688d5bf63d894ae42dabab7da1d4174c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057317"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>제한된 디바이스에 IoT 플러그 앤 플레이 구현

‘제한된 디바이스’용으로 개발 중인 경우 [Embedded C IoT 클라이언트 라이브러리용 Azure SDK](https://aka.ms/embeddedcsdk) 또는 [Azure RTOS](/azure/rtos/overview-rtos)를 통해 IoT 플러그 앤 플레이를 사용할 수 있습니다. 이 문서에는 이러한 제한된 시나리오에 대한 링크와 리소스가 포함되어 있습니다.

## <a name="use-the-sdk-for-embedded-c"></a>Embedded C용 SDK 사용

Embedded C용 SDK는 [IoT 플러그 앤 플레이 규칙](concepts-convention.md)을 사용하여 제한된 디바이스를 Azure IoT 서비스에 연결하는 간단한 솔루션을 제공합니다. 다음 링크에는 실제 디바이스, 교육, 디버깅을 목적으로 사용되는 샘플이 포함되어 있습니다.

### <a name="use-a-real-device"></a>실제 디바이스 사용

실제 디바이스에서 Embedded C용 SDK, 디바이스 프로비저닝 서비스, IoT 플러그 앤 플레이를 사용하는 전체 엔드투엔드 자습서는 [IoT Hub Device Provisioning Service를 통해 Azure에 연결할 PIC-IoT Wx 개발 보드 용도 변경](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)을 참조하세요.

### <a name="introductory-samples"></a>소개 샘플

Embedded C용 SDK 리포지토리에는 IoT 플러그 앤 플레이를 사용하는 방법을 보여 주는 [몇 가지 샘플](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample)이 포함되어 있습니다.

> [!NOTE]
> 이러한 샘플은 교육 및 디버깅을 목적으로 Windows 및 Linux에서 실행되는 것으로 표시됩니다. 프로덕션 시나리오에서 샘플은 제한된 디바이스에서만 사용됩니다.

- [Embedded C용 SDK를 사용하는 자동 온도 조절기 예제](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Embedded C용 SDK를 사용하는 온도 컨트롤러 예제](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Azure RTOS 사용

Azure RTOS에는 Azure IoT 클라우드 서비스에 기본 연결을 추가하는 경량 계층이 포함되어 있습니다. 이 계층은 Azure RTOS의 고급 기능을 사용하는 동안 제한된 디바이스를 Azure IoT에 연결하는 간단한 메커니즘을 제공합니다. 자세히 알아보려면 [Microsoft Azure RTOS란?](/azure/rtos/overview-rtos)을 참조하세요.

### <a name="toolchains"></a>도구 체인

Azure RTOS 샘플에는 다음과 같은 다른 종류의 IDE 및 도구 체인 조합이 제공됩니다.

- IAR: IAR의 [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: 오픈 소스 [CMake](https://cmake.org/) 빌드 시스템 및 [GNU Arm Embedded 도구 체인](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)의 위에 빌드합니다.
- MCUExpresso: NXP의 [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronic의 [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: Microchip의 [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>샘플

Azure RTOS 및 IoT 플러그 앤 플레이를 사용하여 다른 디바이스에서 시작하는 방법을 보여 주는 샘플은 다음 표를 참조하세요.

제조업체 | 디바이스 | 샘플 |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>다음 단계

이제 제한된 디바이스에서 IoT 플러그 앤 플레이를 구현하는 옵션에 대해 알아보았으니 다음 단계에서는 [IoT 플러그 앤 플레이 규칙](concepts-convention.md)에 대해 알아보겠습니다.