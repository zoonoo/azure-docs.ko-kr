---
title: Azure IoT 임베디드 디바이스 개발 빠른 시작
description: Azure RTOS 및 Azure IoT를 사용하여 임베디드 디바이스 개발을 수행하는 방법을 보여주는 빠른 시작 가이드입니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 536b9092a7bddd353b58874e7aa97291839bafcc
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589536"
---
# <a name="getting-started-with-azure-iot-embedded-device-development"></a>Azure IoT 임베디드 디바이스 개발 시작

**적용 대상**: [임베디드 디바이스 개발](about-iot-develop.md#embedded-device-development)

이 시작 가이드에는 임베디드 디바이스 및 Azure IoT로 작업을 시작하는 방법을 보여주는 빠른 시작 세트가 포함되어 있습니다. 

각 빠른 시작에서 다음과 같은 기본 작업을 완료합니다.
* C에서 특정 디바이스를 프로그래밍하기 위한 임베디드 개발 도구 세트 설치
* Azure RTOS 구성 요소 및 샘플이 포함된 이미지를 빌드한 다음, 디바이스를 깜박입니다.
* 디바이스를 Azure IoT에 안전하게 연결
* 디바이스 원격 분석 보기, 속성 보기 및 클라우드-디바이스 메서드 호출

## <a name="quickstarts"></a>빠른 시작
시작 가이드에는 다음 자습서가 포함되어 있습니다.

|빠른 시작|디바이스|
|---------------|-----|
|[ST Microelectronics B-L475E-IOT01 Discovery 키트 시작](https://go.microsoft.com/fwlink/p/?linkid=2129536) |[ST Microelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)|
|[ST Microelectronics B-L4S5I-IOT01 Discovery 키트 시작](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+) |[ST Microelectronics B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)|
|[NXP MIMXRT1060-EVK Evaluation 키트 시작](https://go.microsoft.com/fwlink/p/?linkid=2129821) |[NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)|
|[NXP MIMXRT1050-EVKB Evaluation 키트 시작](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB) |[NXP MIMXRT1050-EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK)|
|[Microchip ATSAME54-XPRO Evaluation 키트 시작](https://go.microsoft.com/fwlink/p/?linkid=2129537) |[Microchip ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)|
|[MXChip AZ3166 IoT DevKit 시작](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166) |[MXChip AZ3166 IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)|
|[RX65N-2MB용 Renesas 스타터 키트 + 시작](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB) |[RX65N-2MB용 Renesas 스타터 키트+](https://www.renesas.com/us/en/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)|

## <a name="next-steps"></a>다음 단계
이 가이드의 디바이스별 빠른 시작을 완료한 후 Azure RTOS 시작 리포지토리에서 다른 디바이스별 문서와 샘플을 탐색합니다.
* [Azure RTOS 및 Azure IoT 시작](https://github.com/azure-rtos/getting-started#getting-started-with-azure-rtos-and-azure-iot)