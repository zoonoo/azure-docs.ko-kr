---
title: Azure IoT SDK 이해 | Microsoft Docs
description: 개발자 가이드 - 장치 및 백 엔드 앱을 빌드하는데 사용할 수 있는 다양한 Azure IoT 장치 및 서비스 SDK에 대한 링크 정보입니다.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aec2126369f45a89050dbd8b2d3cae7e00ccb8ed
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Azure IoT SDK 이해 및 사용

IoT Hub를 사용하기 위한 SDK(소프트웨어 개발 키트)의 범주에는 다음 세 가지가 있습니다.

* **장치 SDK**를 사용하면 IoT 장치에서 실행되는 앱을 빌드할 수 있습니다. 이러한 앱은 IoT Hub로 원격 분석을 보내고, 필요에 따라 IoT Hub에서 메시지를 받습니다.

* **서비스 SDK**를 사용하면 IoT Hub를 관리하고, 필요에 따라 IoT 장치로 메시지를 보낼 수 있습니다.

* **Azure IoT Edge**를 사용하여 지원되는 프로토콜 중 하나를 사용하지 않는 장치에 대한 게이트웨이 빌드할 수 있습니다. 게이트웨이는 Edge의 메시지도 처리할 수 있습니다.

SDK는 여러 프로그래밍 언어를 지원하기 위해 제공됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT 장치 SDK

Microsoft Azure IoT 장치 SDK에는 Azure IoT Hub 서비스에 연결되고 Azure IoT Hub 서비스에서 관리하는 장치와 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 Azure IoT 장치 SDK는 GitHub에서 다운로드할 수 있습니다.

* [.NET용 Azure IoT 장치 SDK][lnk-dotnet-device-sdk]
* [Java용 Azure IoT 장치 SDK][lnk-java-device-sdk]
* [Node.js용 Azure IoT 장치 SDK][lnk-node-device-sdk]
* [Python용 Azure IoT 장치 SDK][lnk-python-device-sdk]
* [C용 Azure IoT 장치 SDK][lnk-c-device-sdk] - 이식성과 광범위한 플랫폼 호환성을 위해 ANSI C(C99)로 작성되었습니다. C용 장치 클라이언트 라이브러리에는 하위 수준 **iothub_client** 및 **serializer**의 두 가지가 있습니다.

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
* [Java용 Azure IoT 서비스 SDK][lnk-java-service-sdk]
* [Node.js용 Azure IoT 서비스 SDK][lnk-node-service-sdk]
* [Python용 Azure IoT 서비스 SDK][lnk-python-service-sdk]
* [C용 Azure IoT 서비스 SDK][lnk-c-service-sdk]

> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge에는 IoT 게이트웨이 솔루션을 만들기 위한 인프라 및 모듈이 포함되어 있습니다. IoT Edge를 확장하여 종단 간 시나리오에 맞는 게이트웨이를 만들 수 있습니다.

GitHub에서 [Azure IoT Edge][lnk-iot-edge]를 다운로드할 수 있습니다.

## <a name="online-api-reference-documentation"></a>온라인 API 참조 설명서

다음 목록은 Azure IoT 장치, 서비스 및 게이트웨이 라이브러리용 온라인 API 참조 설명서에 대한 링크를 포함합니다.

* [IoT(사물 인터넷) .NET][lnk-dotnet-ref]
* [Java용 Azure IoT 장치 SDK][lnk-java-ref]
* [Java용 Azure IoT 서비스 SDK][lnk-java-service-ref]
* [Node.js용 Azure IoT 장치 SDK][lnk-node-ref]
* [Node.js용 Azure IoT 서비스 SDK][lnk-node-service-ref]
* [C용 Azure IoT 장치 SDK][lnk-c-ref]
* [IoT Hub REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>다음 단계

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-devguide-endpoints]
* [장치 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어][lnk-devguide-query]
* [할당량 및 제한][lnk-devguide-quotas]
* [IoT Hub MQTT 지원][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
