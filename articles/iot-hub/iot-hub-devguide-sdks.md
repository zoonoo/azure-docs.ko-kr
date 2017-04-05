---
title: "Azure IoT SDK 이해 | Microsoft Docs"
description: "개발자 가이드 - 장치 및 백 엔드 앱을 빌드하는데 사용할 수 있는 다양한 Azure IoT 장치 및 서비스 SDK에 대한 링크 정보입니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: ff08ea2b6231b2344244b14e44bcfd9acd065508
ms.lasthandoff: 03/27/2017


---
# <a name="understand-and-use-azure-iot-sdks"></a>Azure IoT SDK 이해 및 사용

IoT Hub를 사용하기 위한 SDK에는 다음 세 가지 범주가 있습니다.

* **장치 SDK**를 사용하면 IoT 장치에서 실행되는 앱을 빌드할 수 있습니다. 이러한 앱은 IoT Hub로 원격 분석을 보내고, 필요에 따라 IoT Hub에서 메시지를 받습니다.

* **서비스 SDK**를 사용하면 IoT Hub를 관리하고, 필요에 따라 IoT 장치로 메시지를 보낼 수 있습니다.

* **게이트웨이 SDK**를 사용하면 지원되는 프로토콜 중 하나를 사용하지 않거나 에지 노드에서 메시지를 처리해야 할 때 장치를 활성화하도록 게이트웨이를 빌드할 수 있습니다.

SDK는 여러 프로그래밍 언어를 지원하기 위해 제공됩니다.

## <a name="azure-iot-device-sdks"></a>Azure IoT 장치 SDK

Microsoft Azure IoT 장치 SDK에는 Azure IoT Hub 서비스에 연결되고 Azure IoT Hub 서비스에서 관리하는 장치와 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 Azure IoT 장치 SDK는 GitHub에서 다운로드할 수 있습니다.

* [C용 Azure IoT 장치 SDK][lnk-c-device-sdk] - 이식성과 광범위한 플랫폼 호환성을 위해 ANSI C(C99)로 작성되었습니다.
* [.NET용 Azure IoT 장치 SDK][lnk-dotnet-device-sdk]
* [Java용 Azure IoT 장치 SDK][lnk-java-device-sdk]
* [Node.js용 Azure IoT 장치 SDK][lnk-node-device-sdk]
* [Python용 Azure IoT 장치 SDK][lnk-python-device-sdk]

> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS 플랫폼 및 하드웨어 호환성

특정 하드웨어 장치와의 SDK 호환성에 대한 자세한 내용은 [IoT용 Azure Certified 장치 카탈로그][lnk-certified]를 참조하세요.

## <a name="azure-iot-service-sdks"></a>Azure IoT 서비스 SDK

Azure IoT 서비스 SDK에는 장치와 보안을 관리하기 위해 IoT Hub 서비스를 직접 조작하는 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 Azure IoT 서비스 SDK는 GitHub에서 다운로드할 수 있습니다.

* [.NET용 Azure IoT 서비스 SDK][lnk-dotnet-service-sdk]
* [Node.js용 Azure IoT 서비스 SDK][lnk-node-service-sdk]
* [Java용 Azure IoT 서비스 SDK][lnk-java-service-sdk]
* [Python용 Azure IoT 서비스 SDK][lnk-python-service-sdk]


> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.

## <a name="azure-iot-gateway-sdks"></a>Azure IoT Gateway SDK

이 Azure IoT Gateway SDK는 IoT 게이트웨이 솔루션을 만드는 인프라 및 모듈을 포함합니다. SDK를 확장하여 모든 종단 간 시나리오에 맞는 게이트웨이를 만들 수 있습니다.

GitHub에서 [Azure IoT Gateway SDK][lnk-gateway-sdk]를 다운로드할 수 있습니다.

## <a name="online-api-reference-documentation"></a>온라인 API 참조 설명서

다음 목록은 Azure IoT 장치, 서비스 및 게이트웨이 라이브러리용 온라인 API 참조 설명서에 대한 링크를 포함합니다.

* [IoT(사물 인터넷) .NET][lnk-dotnet-ref]
* [IoT Hub REST][lnk-rest-ref]
* [C용 Azure IoT 장치 SDK][lnk-c-ref]
* [Java용 Azure IoT 장치 SDK][lnk-java-ref]
* [Java용 Azure IoT 서비스 SDK][lnk-java-service-ref]
* [Node.js용 Azure IoT 장치 SDK][lnk-node-ref]
* [Node.js용 Azure IoT 서비스 SDK][lnk-node-service-ref]
* [Azure IoT Gateway SDK][lnk-gateway-ref]

## <a name="next-steps"></a>다음 단계

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-devguide-endpoints]
* [장치 쌍 및 작업에 대한 IoT Hub 쿼리 언어][lnk-devguide-query]
* [할당량 및 제한][lnk-devguide-quotas]
* [IoT Hub MQTT 지원][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/azure-iot-device/1.1.8/index.html
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/azure-iothub/1.1.8/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

