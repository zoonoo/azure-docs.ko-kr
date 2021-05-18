---
title: Azure IoT DPS(Device Provisioning Service) TLS 지원
description: IoT DPS(Device Provisioning Service)와 통신하는 디바이스 및 서비스에 보안 TLS 연결을 사용하는 모범 사례입니다.
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761297"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT Hub DPS(Device Provisioning Service)의 TLS 지원

DPS는 [TLS(전송 계층 보안)](http://wikipedia.org/wiki/Transport_Layer_Security)를 사용하여 IoT 디바이스의 연결을 보호합니다. 

DPS에서 지원하는 현재 TLS 프로토콜 버전은 다음과 같습니다. 
* TLS 1.2

TLS 1.0 및 1.1은 레거시로 간주되며 사용 중단될 예정입니다. 자세한 내용은 [IoT Hub용 TLS 1.0 및 1.1 사용 중단](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)을 참조하세요. 

## <a name="restrict-connections-to-tls-12"></a>연결을 TLS 1.2로 제한

보안을 강화하려면 TLS 버전 1.2를 사용하는 디바이스 클라이언트 ‘연결만’ 허용하고 [권장 암호화](#recommended-ciphers) 사용을 적용하도록 DPS 인스턴스를 구성하는 것이 좋습니다.

이렇게 하려면 Azure Resource Manager 템플릿의 DPS 리소스 사양에서 `minTlsVersion` 속성을 `1.2`로 설정하여 새 DPS 리소스 설정을 프로비저닝합니다. 다음 예제 템플릿 JSON은 새 DPS 인스턴스의 `minTlsVersion` 속성을 지정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

다음 Azure CLI 명령을 사용하여 템플릿을 배포할 수 있습니다. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Resource Manager 템플릿을 사용하여 DPS 리소스를 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 DPS 설정](quick-setup-auto-provision-rm.md)을 참조하세요.

이 구성을 사용하여 만든 DPS 리소스는 TLS 버전 1.0 및 1.1을 사용하여 연결을 시도하는 디바이스를 거부합니다. 마찬가지로, 디바이스 클라이언트의 HELLO 메시지에 [권장 암호화](#recommended-ciphers)가 나열되지 않는 경우 TLS 핸드셰이크가 거부됩니다.

> [!NOTE]
> `minTlsVersion` 속성은 읽기 전용이며 DPS 리소스를 만든 후에는 변경할 수 없습니다. 따라서 미리 적절하게 테스트하여 ‘모든’ IoT 디바이스가 TLS 1.2 및 [권장 암호화](#recommended-ciphers)와 호환되는지 확인하는 것이 중요합니다.


> [!NOTE]
> 장애 조치(failover) 시 DPS의 `minTlsVersion` 속성은 지리적으로 쌍을 이루는 지역에서 장애 조치 후에도 계속 적용됩니다.

## <a name="recommended-ciphers"></a>권장되는 암호

TLS 1.2만 허용하도록 구성된 DPS 인스턴스는 다음과 같은 암호화 그룹 사용도 적용합니다.


| 권장되는 TLS 1.2 암호화 그룹 |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>레거시 암호화 그룹 

이 암호화 그룹은 현재 DPS에서 계속 지원되지만 사용이 중단될 예정입니다. 가능하면 위의 권장 암호화 그룹을 사용합니다.

| 옵션 #1(보안 강화) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| 옵션 #2(성능 향상) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>IoT SDK에서 TLS 1.2 사용

아래 링크를 사용하여 IoT Hub 클라이언트 SDK에서 TLS 1.2 및 허용되는 암호화를 구성할 수 있습니다.

| 언어 | TLS 1.2를 지원하는 버전 | 문서화 |
|----------|------------------------------------|---------------|
| C        | 태그 2019-12-11 이상            | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 버전 2.0.0 이상             | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 버전 1.21.4 이상            | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 버전 1.19.0 이상            | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 버전 1.12.2 이상            | [링크](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>IoT Hub에서 TLS 1.2 사용

디바이스와 통신할 때 TLS 1.2를 사용하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub용 TLS 1.0 및 1.1 사용 중단](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)을 참조하세요.

## <a name="use-tls-12-with-iot-edge"></a>IoT Edge에서 TLS 1.2 사용

IoT Hub 및 DPS와 통신할 때 TLS 1.2를 사용하도록 IoT Edge 디바이스를 구성할 수 있습니다. 자세한 내용은 [IoT Edge 설명서 페이지](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)를 참조하세요.
