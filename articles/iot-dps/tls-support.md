---
title: Azure IoT (장치 프로 비전 서비스) TLS 지원
description: IoT 장치 프로 비전 서비스 (DPS)와 통신 하는 장치 및 서비스에 대 한 보안 TLS 연결 사용의 모범 사례
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: wesmc
ms.openlocfilehash: 9a90180fa606b14b06c94d3211fdf492add0350d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564971"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT Hub 장치 프로 비전 서비스 (DPS)의 TLS 지원

DPS는 [TLS (전송 계층 보안](http://wikipedia.org/wiki/Transport_Layer_Security) )를 사용 하 여 IoT 장치에서의 연결을 보호 합니다. 

DPS에서 지 원하는 현재 TLS 프로토콜 버전은 다음과 같습니다. 
* TLS 1.2

TLS 1.0 및 1.1은 레거시로 간주되며 사용 중단될 예정입니다. 자세한 내용은 [IoT Hub용 TLS 1.0 및 1.1 사용 중단](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)을 참조하세요. 

## <a name="restrict-connections-to-tls-12"></a>TLS 1.2에 대 한 연결 제한

보안을 강화 하려면 TLS 버전 1.2을 사용 하는 장치 클라이언트 *연결만 허용 하* 고 권장 되는 [암호화](#recommended-ciphers)사용을 적용 하도록 DPS 인스턴스를 구성 하는 것이 좋습니다.

이렇게 하려면 `minTlsVersion` `1.2` AZURE RESOURCE MANAGER 템플릿의 DPS 리소스 사양에서 속성을로 설정 하 여 새 DPS 리소스를 프로 비전 합니다. 다음 예제 템플릿 JSON은 `minTlsVersion` 새 DPS 인스턴스에 대 한 속성을 지정 합니다.

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

다음 Azure CLI 명령을 사용 하 여 템플릿을 배포할 수 있습니다. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

리소스 관리자 템플릿으로 DPS 리소스를 만드는 방법에 대 한 자세한 내용은 [Azure Resource Manager 템플릿을 사용 하 여 Dps 설정](quick-setup-auto-provision-rm.md)을 참조 하십시오.

이 구성을 사용 하 여 만든 DPS 리소스는 TLS 버전 1.0 및 1.1를 사용 하 여 연결을 시도 하는 장치를 거부 합니다. 마찬가지로 장치 클라이언트의 HELLO 메시지에 [권장 되는 암호화](#recommended-ciphers)가 나열 되지 않은 경우에는 TLS 핸드셰이크가 거부 됩니다.

> [!NOTE]
> `minTlsVersion`속성은 읽기 전용 이며 DPS 리소스를 만든 후에는 변경할 수 없습니다. 따라서 *모든* IOT 장치가 TLS 1.2 및 [권장 되는 암호화](#recommended-ciphers) 와 호환 되는지 제대로 테스트 하 고 유효성을 검사 하는 것이 중요 합니다.


> [!NOTE]
> 장애 조치 ( `minTlsVersion` failover) 시 DPS의 속성은 지리적으로 쌍을 이루는 지역에서 계속 적용 됩니다.

## <a name="recommended-ciphers"></a>권장되는 암호

TLS 1.2만 허용 하도록 구성 된 DPS 인스턴스는 다음 암호 그룹을 사용 하는 것도 적용 합니다.

### <a name="tls-12-cipher-suites"></a>TLS 1.2 암호 그룹

| 최소 막대 |
| :--- |
| `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`<br>`TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384` |

| 뛰어난 기회 |
| :--- |
| `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |

### <a name="cipher-suite-ordering-prior-to-windows-10"></a>Windows 10 이전의 암호 그룹 순서 지정

| 최소 막대 |
| :--- |
| `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256`<br>`TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P384`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384` |

| 뛰어난 기회 |
| :--- |
| `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384`<br>`TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256` |

### <a name="legacy-cipher-suites"></a>레거시 암호 그룹 

| 옵션 #1 (보안 강화) |
| :--- |
| `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P384 (uses SHA-1)`<br>`TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256 (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| 옵션 #2 (성능 향상) |
| :--- |
| `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256 (uses SHA-1)`<br>`TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P384 (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>IoT Sdk에서 TLS 1.2 사용

아래 링크를 사용 하 여 Azure IoT 클라이언트 Sdk에서 TLS 1.2 및 허용 되는 암호화를 구성 합니다.

| 언어 | TLS 1.2를 지원하는 버전 | 문서화 |
|----------|------------------------------------|---------------|
| C        | 태그 2019-12-11 이상            | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 버전 2.0.0 이상             | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 버전 1.21.4 이상            | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 버전 1.19.0 이상            | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 버전 1.12.2 이상            | [링크](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>IoT Hub에서 TLS 1.2 사용

장치와 통신할 때 TLS 1.2을 사용 하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub용 TLS 1.0 및 1.1 사용 중단](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)을 참조하세요.

## <a name="use-tls-12-with-iot-edge"></a>IoT Edge에서 TLS 1.2 사용

IoT Edge 장치는 IoT Hub 및 DPS와 통신할 때 TLS 1.2를 사용 하도록 구성할 수 있습니다. 자세한 내용은 [IoT Edge 설명서 페이지](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)를 참조 하세요.