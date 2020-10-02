---
title: IoT 플러그 앤 플레이 라이브러리 및 Sdk
description: IoT 플러그 앤 플레이 사용 솔루션을 개발 하는 데 사용할 수 있는 장치 및 서비스 라이브러리에 대 한 정보입니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1b534c6ccb3bfca6c7dddaec82d8794deae72ff2
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651312"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>IoT 용 Microsoft Sdk 플러그 앤 플레이

IoT 플러그 앤 플레이 라이브러리 및 Sdk를 통해 개발자는 여러 플랫폼에서 다양 한 프로그래밍 언어를 사용 하 여 IoT 솔루션을 빌드할 수 있습니다. 다음 표에서는 시작 하는 데 도움이 되는 샘플 및 빠른 시작에 대 한 링크를 제공 합니다.

## <a name="device-sdks"></a>디바이스 SDK

| 언어 | 패키지 | 코드 리포지토리 | 샘플 | 빠른 시작 | 참조 |
|---|---|---|---|---|---|
| C-장치 | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [IoT Hub에 연결](quickstart-connect-device-c.md) | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-장치 | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [IoT Hub에 연결](quickstart-connect-device-csharp.md) | [참조](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java-장치 | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [IoT Hub에 연결](quickstart-connect-device-java.md) | [참조](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python-장치 | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [IoT Hub에 연결](quickstart-connect-device-python.md) | [참조](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| 노드-장치 | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [IoT Hub에 연결](quickstart-connect-device-node.md) | [참조](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| 임베디드 C 장치 | 해당 없음 | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [샘플](howto-use-embedded-c.md#samples) | [임베디드 C를 사용 하는 방법](howto-use-embedded-c.md) | N/A

## <a name="service-sdks"></a>서비스 SDK

| 플랫폼  | 패키지 | 코드 리포지토리 | 샘플 | 빠른 시작 | 참조 |
|---|---|---|---|---|---|
| .NET-IoT Hub 서비스 | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/A | [참조](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java-IoT Hub 서비스 | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/A | [참조](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| 노드 IoT Hub 서비스 | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/A | [참조](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python-디지털 Twins 서비스 | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [IoT Hub Digital Twins API와 상호 작용](quickstart-service-python.md) | N/A |
| 노드-디지털 Twins 서비스 | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [IoT Hub Digital Twins API와 상호 작용](quickstart-service-node.md) | 해당 없음 |

## <a name="next-steps"></a>다음 단계

Sdk 및 라이브러리를 사용해 보려면  [개발자 가이드](concepts-developer-guide-device-csharp.md) 및 [장치](quickstart-connect-device-c.md) 빠른 시작 및 [서비스 퀵 스타트](quickstart-service-node.md)를 참조 하세요.
