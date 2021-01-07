---
title: 서비스 개발자 가이드-IoT 플러그 앤 플레이 | Microsoft Docs
description: 서비스 개발자를 위한 IoT 플러그 앤 플레이 설명
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: df913716ff34a61e5bde4c0771ea8b7599db3d30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521399"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 플러그 앤 플레이 서비스 개발자 가이드

IoT 플러그 앤 플레이를 사용 하 여 Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다.

IoT 플러그 앤 플레이를 사용 하면 IoT hub로 모델 ID를 발표 한 장치를 사용할 수 있습니다. 예를 들어 장치의 속성 및 명령에 직접 액세스할 수 있습니다.

Iot hub에 연결 된 iot 플러그 앤 플레이 장치를 사용 하려면 IoT 서비스 Sdk 중 하나를 사용 합니다.

## <a name="service-sdks"></a>서비스 SDK

솔루션에서 Azure IoT 서비스 Sdk를 사용 하 여 장치 및 모듈과 상호 작용 합니다. 예를 들어, 서비스 Sdk를 사용 하 여 쌍 속성을 읽고 업데이트 하 고 명령을 호출할 수 있습니다. 지원 되는 언어에는 c #, Java, Node.js 및 Python이 포함 됩니다.

서비스 Sdk를 사용 하면 데스크톱 또는 웹 응용 프로그램과 같은 솔루션에서 장치 정보에 액세스할 수 있습니다. 서비스 Sdk에는 모델 ID를 검색 하는 데 사용할 수 있는 두 가지 네임 스페이스와 개체 모델이 포함 되어 있습니다.

- Iot Hub 서비스 클라이언트입니다. 이 서비스는 모델 ID를 장치 쌍 속성으로 노출 합니다.

- Digital Twins 클라이언트. 새 Digital Twins API는 구성 요소, 속성 및 명령과 같은 [DTDL (디지털 Twins 정의 언어)](concepts-digital-twin.md) 모델 구조에서 작동 합니다. 디지털 쌍 Api를 사용 하면 솔루션 빌더가 IoT 플러그 앤 플레이 솔루션을 쉽게 만들 수 있습니다.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>다음 단계

이제 장치 모델링에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [모델 구성 요소](./concepts-components.md)
- [DTDL authoring tools 설치 및 사용](howto-use-dtdl-authoring-tools.md)