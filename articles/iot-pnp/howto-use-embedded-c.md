---
title: 제한 된 장치에서 IoT 플러그 앤 플레이 사용 | Microsoft Docs
description: 임베디드 C 또는 Azure RTOS 용 SDK를 사용 하 여 제한 된 장치에서 IoT 플러그 앤 플레이를 구현 하는 방법에 대해 알아봅니다.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: edcfcda8de1fade605c409537155231d90d5849e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91610078"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>제한 된 장치에서 IoT 플러그 앤 플레이 구현

*제약이 있는 장치*에 대해 개발 하는 경우 [임베디드 C iot 클라이언트 라이브러리](https://aka.ms/embeddedcsdk) 또는 [azure Rtos](https://docs.microsoft.com/azure/rtos/overview-rtos)를 사용 하 여 azure SDK와 iot 플러그 앤 플레이를 사용할 수 있습니다. 이 문서에는 이러한 제한 된 시나리오에 대 한 링크와 리소스가 포함 되어 있습니다.

## <a name="use-the-sdk-for-embedded-c"></a>임베디드 C에 SDK 사용

Embedded C 용 SDK는 [iot 플러그 앤 플레이 규칙](concepts-convention.md)을 사용 하 여 제한 된 장치를 Azure iot 서비스에 연결 하는 간단한 솔루션을 제공 합니다. 다음 링크에는 실제 장치에 대 한 샘플과 교육 및 디버깅이 포함 됩니다.

### <a name="use-a-real-device"></a>실제 디바이스 사용

실제 장치에서 임베디드 C, 장치 프로 비전 서비스 및 IoT 플러그 앤 플레이에 대 한 SDK를 사용 하는 전체 종단 간 자습서는 [IoT Hub Device Provisioning Service를 통해 Azure에 연결 하기 위한 PIC-IoT Wx Development Board](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)사용을 참조 하세요.

### <a name="introductory-samples"></a>소개 샘플

임베디드 C 리포지토리의 SDK에는 IoT 플러그 앤 플레이를 사용 하는 방법을 보여 주는 [몇 가지 샘플이](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) 포함 되어 있습니다.

> [!NOTE]
> 이러한 샘플은 교육 및 디버깅을 위해 Windows 및 Linux에서 실행 되는 것으로 표시 됩니다. 프로덕션 시나리오에서 샘플은 제한 된 장치에만 사용 됩니다.

- [임베디드 C 용 SDK를 사용 하는 자동 온도 조절기 예제](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [임베디드 C 용 SDK를 사용 하는 온도 컨트롤러 예제](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Azure RTOS 사용

Azure RTOS에는 Azure IoT 클라우드 서비스에 대 한 기본 연결을 추가 하는 경량 계층이 포함 되어 있습니다. 이 계층은 Azure RTOS의 고급 기능을 사용 하는 동안 제한 된 장치를 Azure IoT에 연결 하는 간단한 메커니즘을 제공 합니다. 자세한 내용은 [Microsoft Azure RTOS 정의](https://docs.microsoft.com/azure/rtos/overview-rtos)를 참조 하세요.

### <a name="toolchains"></a>도구 체인

Azure RTOS 샘플은 다음과 같은 다양 한 종류의 IDE 및 도구 체인 조합을 제공 합니다.

- IAR: IAR의 [Embedded 워크 벤치](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: 오픈 소스 [cmake](https://cmake.org/) 시스템 및 [GNU Arm Embedded 도구 체인](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)위에 빌드합니다.
- MCUExpresso: NXP의 [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: St마이크로 Electronic의 [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: 마이크로칩으로 's [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>샘플

Azure RTOS 및 IoT 플러그 앤 플레이를 사용 하 여 다른 장치에서 시작 하는 방법을 보여 주는 샘플은 다음 표를 참조 하세요.

제조업체 | 디바이스 | 샘플 |
| --- | --- | --- |
| 마이크로칩으로 | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/cto•](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) [iar](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/cto•](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) [iar](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| St마이크로 전자 제품 | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [Iar](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| St마이크로 전자 제품 | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/cto•](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) [iar](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| St마이크로 전자 제품 | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/cto•](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) [iar](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>다음 단계

이제 제한 된 장치에서 IoT 플러그 앤 플레이를 구현 하기 위한 옵션에 대해 알아보았습니다. 다음 단계에서는 [iot 플러그 앤 플레이 규칙](concepts-convention.md)에 대해 알아봅니다.