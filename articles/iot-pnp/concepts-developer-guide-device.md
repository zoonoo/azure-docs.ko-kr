---
title: 장치 개발자 가이드 (C)-IoT 플러그 앤 플레이 | Microsoft Docs
description: C 장치 개발자를 위한 IoT 플러그 앤 플레이 설명
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8d8da39f038f465030a2dced092ab1b008e30e5e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511451"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT 플러그 앤 플레이 장치 개발자 가이드

IoT 플러그 앤 플레이를 사용 하 여 Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다.

스마트 장치는 직접 구현 하거나 [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 사용 하거나 [IoT Edge 모듈](../iot-edge/about-iot-edge.md)을 사용할 수 있습니다.

이 가이드에서는 [IoT 플러그 앤 플레이 규칙](../iot-pnp/concepts-convention.md)을 따르는 장치, 모듈 또는 IoT Edge 모듈을 만드는 데 필요한 기본 단계에 대해 설명 합니다.

IoT 플러그 앤 플레이 장치, 모듈 또는 IoT Edge 모듈을 빌드하려면 다음 단계를 수행 합니다.

1. 장치가 MQTT 또는 Websocket을 통한 MQTT 프로토콜을 사용 하 여 Azure IoT Hub에 연결 하는지 확인 합니다.
1. 장치를 설명 하는 [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 모델을 만듭니다. 자세히 알아보려면 [IoT 플러그 앤 플레이 모델의 구성 요소 이해](concepts-components.md)를 참조 하세요.
1. 장치 연결의 일부로를 알리기 위해 장치 또는 모듈을 업데이트 `model-id` 합니다.
1. [IoT 플러그 앤 플레이 규칙](concepts-convention.md) 을 사용 하 여 원격 분석, 속성 및 명령 구현

장치 또는 모듈 구현이 준비 되 면 [Azure iot 탐색기](howto-use-iot-explorer.md) 를 사용 하 여 장치가 IoT 플러그 앤 플레이 규칙을 따르는지 확인 합니다.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 장치 개발에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [모델 구성 요소](concepts-components.md)
- [DTDL authoring tools 설치 및 사용](howto-use-dtdl-authoring-tools.md)
- [IoT 플러그 앤 플레이 서비스 개발자 가이드](concepts-developer-guide-service.md)
