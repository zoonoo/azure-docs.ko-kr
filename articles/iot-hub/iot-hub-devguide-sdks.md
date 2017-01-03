---
title: "개발자 가이드 - Azure IoT SDK | Microsoft Docs"
description: "Azure IoT Hub 개발자 가이드 - 다양한 Azure IoT 장치 및 서비스 SDK에 대한 링크와 정보입니다."
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58dc546ee2db9a0925ecf0fc074795b531323e03


---
# <a name="azure-iot-sdks"></a>Azure IoT SDK
## <a name="azure-iot-device-sdk"></a>Azure IoT 장치 SDK
Microsoft Azure IoT 장치 SDK에는 Azure IoT Hub 서비스에 연결되고 Azure IoT Hub 서비스에서 관리하는 장치와 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 Azure IoT 장치 SDK는 GitHub에서 다운로드할 수 있습니다.

* [C용 Azure IoT 장치 SDK][lnk-c-device-sdk] - 이식성과 광범위한 플랫폼 호환성을 위해 ANSI C(C99)로 작성되었습니다.
* [.NET용 Azure IoT 장치 SDK][lnk-dotnet-device-sdk]
* [Java용 Azure IoT 장치 SDK][lnk-java-device-sdk]
* [Node.js용 Azure IoT 장치 SDK][lnk-node-device-sdk]
* [Python 2.7용 Microsoft Azure IoT 장치 SDK][lnk-python-device-sdk]

> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.
> 
> 

## <a name="os-platforms-and-hardware-compatibility"></a>OS 플랫폼 및 하드웨어 호환성
특정 하드웨어 장치와의 SDK 호환성에 대한 자세한 내용은 [IoT용 Azure Certified 장치 카탈로그][lnk-certified]를 참조하세요.

## <a name="azure-iot-service-sdk"></a>Azure IoT 서비스 SDK
Azure IoT 서비스 SDK에는 장치와 보안을 관리하기 위해 IoT Hub와 직접적으로 상호 작용하는 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 Azure IoT 서비스 SDK는 GitHub에서 다운로드할 수 있습니다.

* [.NET용 Azure IoT 서비스 SDK][lnk-dotnet-service-sdk]
* [Node.js용 Azure IoT 서비스 SDK][lnk-node-service-sdk]
* [Java용 Azure IoT 서비스 SDK][lnk-java-service-sdk]

> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.
> 
> 

## <a name="azure-iot-gateway-sdk"></a>Azure IoT Gateway SDK
이 Azure IoT Gateway SDK는 IoT 게이트웨이 솔루션을 만드는 인프라 및 모듈을 포함합니다. SDK를 확장하여 모든 종단 간 시나리오에 맞는 게이트웨이를 만들 수 있습니다.

GitHub에서 [Azure IoT Gateway SDK][lnk-gateway-sdk]를 다운로드할 수 있습니다.

## <a name="online-api-reference-documentation"></a>온라인 API 참조 설명서
다음은 Azure IoT 장치, 서비스 및 게이트웨이 라이브러리용 온라인 API 참조 설명서에 대한 링크 목록입니다.

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

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


