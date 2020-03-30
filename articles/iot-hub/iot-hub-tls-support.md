---
title: Azure IoT 허브 TLS 지원
description: IoT Hub와 통신하는 장치 및 서비스에 대한 보안 TLS 연결을 사용하는 모범 사례
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409502"
---
# <a name="tls-support-in-iot-hub"></a>IoT 허브에서 TLS 지원

IoT Hub는 TLS(전송 계층 보안)를 사용하여 IoT 장치 및 서비스에서 연결을 보호합니다. TLS 프로토콜의 세 가지 버전은 현재 지원됩니다, 즉 버전 1.0, 1.1, 1.2.

TLS 1.0 및 1.1은 레거시로 간주되며 사용 중단을 위해 계획됩니다. 자세한 내용은 [IoT Hub에 대한 TLS 1.0 및 1.1 을](iot-hub-tls-deprecating-1-0-and-1-1.md)참조하세요. IoT Hub에 연결할 때 TLS 1.2를 기본 TLS 버전으로 사용하는 것이 좋습니다.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>IoT Hub 리소스에서 TLS 1.2에 대한 연결 제한

보안을 강화하기 위해 TLS 버전 1.2를 사용하는 클라이언트 *연결만* 허용하고 권장 암호 사용을 적용하도록 IoT [Hub를](#recommended-ciphers)구성하는 것이 좋습니다.

이를 위해 [지원되는 모든 지역에](#supported-regions) 새 IoT Hub를 `minTlsVersion` 프로비전하고 Azure Resource Manager 템플릿의 IoT 허브 리소스 사양에 `1.2` 속성을 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

이 구성을 사용하여 생성된 IoT Hub 리소스는 TLS 버전 1.0 및 1.1을 사용하여 연결을 시도하는 장치 및 서비스 클라이언트를 거부합니다. 마찬가지로 클라이언트 HELLO 메시지에 권장되는 암호를 나열하지 않으면 TLS 핸드셰이크가 [거부됩니다.](#recommended-ciphers)

> [!NOTE]
> 속성은 `minTlsVersion` 읽기 전용이며 IoT Hub 리소스가 만들어지면 변경할 수 없습니다. 따라서 *모든* IoT 장치 및 서비스가 TLS 1.2 및 권장 암호와 호환되는지 사전에 적절하게 테스트하고 [검증해야 합니다.](#recommended-ciphers)

### <a name="supported-regions"></a>지원되는 지역

TLS 1.2를 사용해야 하는 IoT 허브는 다음 리전에서 만들 수 있습니다.

* 미국 동부
* 미국 중남부
* 미국 서부 2
* US Gov 애리조나
* US Gov 버지니아

> [!NOTE]
> 장애 조치(failover)가 완료되면 IoT Hub의 `minTlsVersion` 속성은 장애 조치 후 지리적으로 페어링된 지역에서 유효하게 유지됩니다.

### <a name="recommended-ciphers"></a>권장 암호

TLS 1.2만 허용하도록 구성된 IoT 허브는 다음과 같은 권장 암호의 사용도 적용합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>IoT 허브 SDK에서 TLS 1.2 사용

아래 링크를 사용하여 IoT Hub 클라이언트 SDK에서 TLS 1.2 및 허용 된 암호를 구성합니다.

| 언어 | TLS 1.2를 지원하는 버전 | 설명서 |
|----------|------------------------------------|---------------|
| C        | 태그 2019-12-11 또는 최신            | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 버전 2.0.0 이상             | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 버전 1.21.4 이상            | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 버전 1.19.0 이상            | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 버전 1.12.2 이상            | [링크](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>IoT 에지 설정에서 TLS 1.2 사용

IoT Edge 장치는 IoT Hub와 통신할 때 TLS 1.2를 사용하도록 구성할 수 있습니다. 이를 위해 [IoT Edge 문서 페이지를](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)사용합니다.