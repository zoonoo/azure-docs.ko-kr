---
title: IoT 플러그 앤 플레이 라이브러리 및 Sdk
description: IoT 플러그 앤 플레이 사용 솔루션을 개발 하는 데 사용할 수 있는 장치 및 서비스 라이브러리에 대 한 정보입니다.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b328d7fe4cf3a3487614ed93dcf130aa7a233efd
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830572"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 플러그 앤 플레이 라이브러리 및 Sdk

IoT 플러그 앤 플레이 라이브러리 및 Sdk를 통해 개발자는 여러 플랫폼에서 다양 한 프로그래밍 언어를 사용 하 여 IoT 솔루션을 빌드할 수 있습니다. 다음 표에서는 시작 하는 데 도움이 되는 샘플 및 빠른 시작에 대 한 링크를 제공 합니다.

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft 지원 라이브러리 및 Sdk

| 플랫폼 | 라이브러리/패키지 | 소스 코드 | 샘플 | 빠른 시작 | 참조 |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Apt의 장치 SDK-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [디지털 쌍 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [IoT Hub에 연결](./quickstart-connect-pnp-device-c-linux.md) | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Vcpkg의 장치 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [디지털 쌍 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [IoT Hub에 연결](./quickstart-connect-pnp-device-c-windows.md) | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [EMBED의 장치 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [디지털 쌍 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Arduino IDE의 장치 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [디지털 쌍 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [CocoaPod의 장치 SDK](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [디지털 쌍 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [참조](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [디지털 쌍 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [IoT Hub에 연결](./quickstart-connect-pnp-device-csharp.md) | [참조](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [디지털 쌍 샘플](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [IoT Hub에 연결](./quickstart-connect-pnp-device-java.md) | [참조](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [디지털 쌍 샘플](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [IoT Hub에 연결](./quickstart-connect-pnp-device-node.md) | [참조](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |

## <a name="iot-hub-support"></a>IoT Hub 지원

IoT 플러그 앤 플레이 장치 기능은 [무료 및 표준 계층 IoT hubs](../iot-hub/iot-hub-scaling.md)에서만 지원 됩니다.

## <a name="next-steps"></a>다음 단계

장치 Sdk 및 라이브러리 외에도 REST Api를 사용 하 여 모델 리포지토리와 상호 작용할 수 있습니다.