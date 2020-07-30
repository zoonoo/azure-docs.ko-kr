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
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407798"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>IoT 용 Microsoft Sdk 플러그 앤 플레이

IoT 플러그 앤 플레이 라이브러리 및 Sdk를 통해 개발자는 여러 플랫폼에서 다양 한 프로그래밍 언어를 사용 하 여 IoT 솔루션을 빌드할 수 있습니다. 다음 표에서는 시작 하는 데 도움이 되는 샘플 및 빠른 시작에 대 한 링크를 제공 합니다.

## <a name="device-sdks-ga"></a>장치 Sdk (GA)

| 언어 | 패키지 | 코드 리포지토리 | 샘플 | 빠른 시작 | 참조 |
|---|---|---|---|---|---|
| C-장치 | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [샘플](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [IoT Hub에 연결](quickstart-connect-device-c.md) | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-장치 | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [IoT Hub에 연결](quickstart-connect-device-csharp.md) | [참조](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java-장치 | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [IoT Hub에 연결](quickstart-connect-device-java.md) | [참조](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-장치 | [pip 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [IoT Hub에 연결](quickstart-connect-device-python.md) | [참조](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| 노드-장치 | [npm 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [IoT Hub에 연결](quickstart-connect-device-node.md) | [참조](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>장치 Sdk (미리 보기)

| 언어 | 코드 리포지토리/샘플 |
|---|---|
|Embedded 용 Azure SDK| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTOS IoT 미들웨어| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Azure RTOS 시작 가이드 | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>서비스 Sdk (미리 보기)

| 언어 | 패키지 | 코드 리포지토리 | 샘플 | 빠른 시작 | 참조 |
|---|---|---|---|---|---|
| .NET-IoT Hub service 미리 보기 | [NuGet 1.27.1-미리 보기-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | N/A | 해당 없음 |
| Java-IoT Hub service 미리 보기 | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | N/A | 해당 없음 |
| 노드-IoT Hub service 미리 보기 | [npm 1.12.4.4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | N/A | 해당 없음 |
| Python-IoT Hub/Digital Twins 서비스 미리 보기 | [pip 2.2.1 rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [IoT Hub Digital Twins API와 상호 작용](quickstart-service-python.md) | 해당 없음 |
| 노드-디지털 Twins 서비스 미리 보기 | [npm 1.0.0-pnp-refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [IoT Hub Digital Twins API와 상호 작용](quickstart-service-node.md) | 해당 없음 |

## <a name="next-steps"></a>다음 단계

Sdk 및 라이브러리를 사용해 보려면 [개발자 가이드](concepts-developer-guide.md) 및 [장치](quickstart-connect-device-c.md) 빠른 시작 및 [서비스 퀵 스타트](quickstart-service-node.md)를 참조 하세요.
