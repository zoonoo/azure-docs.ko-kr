---
title: Azure IoT (장치 프로 비전 서비스) TLS 지원
description: IoT 장치 프로 비전 서비스 (DPS)와 통신 하는 장치 및 서비스에 대 한 보안 TLS 연결 사용의 모범 사례
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984851"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT Hub 장치 프로 비전 서비스 (DPS)의 TLS 지원

DPS는 TLS (전송 계층 보안)를 사용 하 여 IoT 장치에서의 연결을 보호 합니다. 현재 세 가지 버전의 TLS 프로토콜 (버전 1.0, 1.1 및 1.2)이 지원 됩니다.

TLS 1.0 및 1.1은 레거시로 간주 되며 사용 중단에 대 한 계획입니다. 자세한 내용은 [사용 중단 TLS 1.0 및 1.1 for IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)를 참조 하세요. DPS에 연결할 때 기본 TLS 버전으로 TLS 1.2를 사용 하는 것이 좋습니다.

## <a name="restrict-connections-to-tls-12"></a>TLS 1.2에 대 한 연결 제한

보안을 강화 하려면 TLS 버전 1.2을 사용 하는 장치 클라이언트 *연결만 허용 하* 고 권장 되는 [암호화](#recommended-ciphers)사용을 적용 하도록 DPS 인스턴스를 구성 하는 것이 좋습니다.

이렇게 하려면 [지원 되는 지역](#supported-regions) 중 하나에 새 DPS 리소스를 프로 비전 하 고 AZURE RESOURCE MANAGER `minTlsVersion` 템플릿의 DPS `1.2` 리소스 사양에서 속성을로 설정 합니다. 다음 예제 템플릿 JSON은 새 DPS `minTlsVersion` 인스턴스에 대 한 속성을 지정 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
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
> 속성 `minTlsVersion` 은 읽기 전용 이며 DPS 리소스를 만든 후에는 변경할 수 없습니다. 따라서 *모든* IOT 장치가 TLS 1.2 및 [권장 되는 암호화](#recommended-ciphers) 와 호환 되는지 제대로 테스트 하 고 유효성을 검사 하는 것이 중요 합니다.

## <a name="supported-regions"></a>지원되는 지역

TLS 1.2를 사용 해야 하는 IoT DPS 인스턴스는 다음 지역에서 만들 수 있습니다.

* US Gov 애리조나
* US Gov 버지니아

> [!NOTE]
> 장애 조치 (failover `minTlsVersion` ) 시 DPS의 속성은 지리적으로 쌍을 이루는 지역에서 계속 적용 됩니다.

## <a name="recommended-ciphers"></a>권장 암호화

TLS 1.2만 허용 하도록 구성 된 DPS 인스턴스는 다음과 같은 권장 암호화의 사용도 적용 합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>IoT Sdk에서 TLS 1.2 사용

아래 링크를 사용 하 여 Azure IoT 클라이언트 Sdk에서 TLS 1.2 및 허용 되는 암호화를 구성 합니다.

| 언어 | TLS 1.2을 지 원하는 버전 | 문서화 |
|----------|------------------------------------|---------------|
| C        | 태그 2019-12-11 이상            | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 버전 2.0.0 이상             | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 버전 1.21.4 이상            | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 버전 1.19.0 이상            | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 버전 1.12.2 이상            | [링크](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>IoT Edge에서 TLS 1.2 사용

IoT Edge 장치는 IoT Hub 및 DPS와 통신할 때 TLS 1.2를 사용 하도록 구성할 수 있습니다. 이러한 목적을 위해 [IoT Edge 설명서 페이지](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)를 사용 합니다.