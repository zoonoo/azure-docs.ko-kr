---
title: Azure IoT Hub TLS 지원
description: IoT Hub와 통신 하는 장치 및 서비스에 대 한 보안 TLS 연결 사용의 모범 사례
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722890"
---
# <a name="tls-support-in-iot-hub"></a>IoT Hub의 TLS 지원

IoT Hub는 TLS (전송 계층 보안)를 사용 하 여 IoT 장치 및 서비스에서의 연결을 보호 합니다. 현재 세 가지 버전의 TLS 프로토콜 (버전 1.0, 1.1 및 1.2)이 지원 됩니다.

TLS 1.0 및 1.1은 레거시로 간주 되며 사용 중단에 대 한 계획입니다. 자세한 내용은 [사용 중단 TLS 1.0 및 1.1 for IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md)를 참조 하세요. IoT Hub에 연결할 때 TLS 1.2을 기본 TLS 버전으로 사용 하는 것이 좋습니다.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>IoT Hub 리소스에서 TLS 1.2에 대 한 연결 제한

보안을 강화 하려면 TLS 버전 1.2을 사용 하는 클라이언트 연결만 *허용 하도록 IoT hub를 구성* 하 고 권장 되는 [암호화](#recommended-ciphers)사용을 적용 하는 것이 좋습니다.

이러한 목적을 위해 [지원 되는 지역](#supported-regions) 중 하나에 새 IoT Hub를 프로 비전 하 고 Azure Resource Manager 템플릿의 IoT Hub 리소스 사양에서 `minTlsVersion` 속성을 `1.2`로 설정 합니다.

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

이 구성을 사용 하 여 만든 IoT Hub 리소스는 TLS 버전 1.0 및 1.1를 사용 하 여 연결을 시도 하는 장치 및 서비스 클라이언트를 거부 합니다. 마찬가지로, 클라이언트 HELLO 메시지에 [권장 되는 암호화](#recommended-ciphers)가 나열 되지 않으면 TLS 핸드셰이크가 거부 됩니다.

> [!NOTE]
> `minTlsVersion` 속성은 읽기 전용 이며 IoT Hub 리소스를 만든 후에는 변경할 수 없습니다. 따라서 *모든* IoT 장치 및 서비스가 TLS 1.2 및 권장 되는 [암호화](#recommended-ciphers) 를 미리 준수 하는지 적절히 테스트 하 고 유효성을 검사 해야 합니다.

### <a name="supported-regions"></a>지원되는 지역

TLS 1.2을 사용 해야 하는 IoT Hub는 다음 지역에서 만들 수 있습니다.

* 미국 동부
* 미국 중남부
* 미국 서부 2

> [!NOTE]
> 장애 조치 (failover) 시 IoT Hub의 `minTlsVersion` 속성은 지리적으로 쌍을 이루는 지역에서 장애 조치 후에 적용 됩니다.

### <a name="recommended-ciphers"></a>권장 암호화

TLS 1.2만 허용 하도록 구성 된 IoT Hub는 다음과 같은 권장 암호 사용도 적용 합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>IoT Hub Sdk에서 TLS 1.2 사용

IoT Hub 클라이언트 Sdk에서 TLS 1.2 및 허용 되는 암호화를 구성 하려면 아래 링크를 사용 하세요.

| 언어 | TLS 1.2 지원 됨 | 문서화 |
|----------|-------------------|---------------|
| C        | yes               | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | yes               | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | yes               | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | yes               | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | yes               | [링크](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>IoT Edge 설정에서 TLS 1.2 사용

IoT Edge 장치는 IoT Hub와 통신할 때 TLS 1.2를 사용 하도록 구성할 수 있습니다. 이러한 목적을 위해 [IoT Edge 설명서 페이지](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)를 사용 합니다.