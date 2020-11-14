---
title: Azure IoT Hub TLS 지원
description: IoT Hub와 통신하는 디바이스 및 서비스에 대해 보안 TLS 연결을 사용하는 모범 사례
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: jlian
ms.openlocfilehash: c9dd66fe9d71f0a857e4b0821190bceb5d6d4680
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628801"
---
# <a name="tls-support-in-iot-hub"></a>IoT Hub의 TLS 지원

IoT Hub는 TLS(전송 계층 보안)를 사용하여 IoT 디바이스 및 서비스의 연결을 보호합니다. 현재 세 가지 버전의 TLS 프로토콜(버전 1.0, 1.1 및 1.2)이 지원됩니다.

TLS 1.0 및 1.1은 레거시로 간주되며 사용 중단될 예정입니다. 자세한 내용은 [IoT Hub용 TLS 1.0 및 1.1 사용 중단](iot-hub-tls-deprecating-1-0-and-1-1.md)을 참조하세요. IoT Hub에 연결할 때 TLS 1.2를 기본 TLS 버전으로 사용하는 것이 좋습니다.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Select 지역에서 TLS 1.2 적용 사용 가능

보안을 강화 하려면 TLS 버전 1.2을 사용 하는 클라이언트 *연결만 허용 하도록 IoT hub를 구성* 하 고, [암호 그룹](#cipher-suites)사용을 적용 합니다. 이 기능은 다음 지역 에서만 지원 됩니다.

* 미국 동부
* 미국 중남부
* 미국 서부 2
* US Gov 애리조나
* US Gov 버지니아

이러한 목적을 위해 지원 지역에서 새 IoT Hub를 프로비저닝하고 Azure Resource Manager 템플릿의 IoT Hub 리소스 사양에서 `minTlsVersion` 속성을 `1.2`로 설정합니다.

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

이 구성을 사용하여 만든 IoT Hub 리소스는 TLS 버전 1.0 및 1.1을 사용하여 연결을 시도하는 디바이스 및 서비스 클라이언트를 거부합니다. 마찬가지로, `ClientHello` 메시지에 [권장 되는 암호화](#cipher-suites)가 나열 되지 않으면 TLS 핸드셰이크가 거부 됩니다.

> [!NOTE]
> `minTlsVersion` 속성은 읽기 전용이며 IoT Hub 리소스를 만든 후에는 변경할 수 없습니다. 따라서 *모든*  IoT 디바이스 및 서비스가 TLS 1.2 및 [권장되는 암호](#cipher-suites)와 호환되는지 적절히 테스트하고 유효성을 검사하는 것이 중요합니다.
> 
> 장애 조치(failover) 시 IoT Hub의 `minTlsVersion` 속성은 지리적으로 쌍을 이루는 지역에서 장애 조치 후에 적용됩니다.

## <a name="cipher-suites"></a>암호 그룹

TLS 1.2만 허용 하도록 구성 된 IoT Hub는 다음과 같은 권장 된 암호 그룹을 사용 하도록 적용 합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

TLS 1.2 적용에 대해 구성 되지 않은 IoT Hub의 경우 TLS 1.2은 여전히 다음 암호 그룹을 사용 합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

클라이언트는에서 사용 하는 더 높은 암호 그룹 목록을 제안할 수 있습니다 `ClientHello` . 그러나 일부는 IoT Hub에서 지원 되지 않을 수 있습니다 (예: `ECDHE-ECDSA-AES256-GCM-SHA384` ). 이 경우 IoT Hub은 클라이언트의 기본 설정을 따르도록 시도 하지만 결국는를 사용 하 여 암호 그룹을 협상 `ServerHello` 합니다.

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>IoT Hub SDK에서 TLS 1.2 사용

IoT Hub 클라이언트 SDK에서 TLS 1.2 및 허용되는 암호를 구성하려면 아래 링크를 사용하세요.

| 언어 | TLS 1.2를 지원하는 버전 | 문서화 |
|----------|------------------------------------|---------------|
| C        | 태그 2019-12-11 이상            | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 버전 2.0.0 이상             | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 버전 1.21.4 이상            | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 버전 1.19.0 이상            | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 버전 1.12.2 이상            | [링크](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>IoT Edge 설정에서 TLS 1.2 사용

IoT Edge 디바이스는 IoT Hub와 통신할 때 TLS 1.2를 사용하도록 구성할 수 있습니다. 이러한 목적을 위해 [IoT Edge 설명서 페이지](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)를 사용합니다.

## <a name="device-authentication"></a>디바이스 인증

TLS 핸드셰이크에 성공한 후 IoT Hub 대칭 키 또는 x.509 인증서를 사용 하 여 장치를 인증할 수 있습니다. 인증서 기반 인증의 경우 ECC를 포함 하 여 모든 x.509 인증서를 사용할 수 있습니다. IoT Hub는 사용자가 제공 하는 지문 또는 CA (인증 기관)에 대해 인증서의 유효성을 검사 합니다. 자세히 알아보려면 [지원 되는 x.509 인증서](iot-hub-devguide-security.md#supported-x509-certificates)를 참조 하세요.
