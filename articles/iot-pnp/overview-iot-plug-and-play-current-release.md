---
title: IoT 플러그 앤 플레이 현재 릴리스 | Microsoft Docs
description: 현재 IoT 플러그 앤 플레이 릴리스에 포함된 내용에 대해 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c7ebcdac3174f7eb497ae3e976386ab92212e1ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715558"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>현재 IoT 플러그 앤 플레이 릴리스에 포함된 내용

이 문서에서는 현재 IoT 플러그 앤 플레이 릴리스를 지원하는 도구, SDK 및 API를 요약합니다. 표시되는 버전 번호는 IoT 플러그 앤 플레이가 일반 공급될 때의 버전 번호를 반영합니다. 버전 번호는 릴리스 후 증가할 수 있습니다.

## <a name="modeling-language"></a>모델링 언어

[DTDL(디지털 트윈 정의 언어) v2](https://github.com/Azure/opendigitaltwins-dtdl)

IoT 플러그 앤 플레이 디바이스가 DTDL에서 작동하는 방식에 대한 자세한 내용은 [IoT 플러그 앤 플레이 규칙](concepts-convention.md)을 참조하세요.

## <a name="tools-and-utilities"></a>도구 및 유틸리티

- Azure IoT 탐색기 0.12.0

    자세히 알아보려면 [Azure IoT 탐색기 설치 및 사용](howto-use-iot-explorer.md)을 참조하세요.

- VS Code 확장 1.0.0

    자세히 알아보려면 [DTDL 제작 도구 설치 및 사용](howto-use-dtdl-authoring-tools.md)을 참조하세요.

- Visual Studio 2019 확장 1.0.0

    자세히 알아보려면 [DTDL 제작 도구 설치 및 사용](howto-use-dtdl-authoring-tools.md)을 참조하세요.

- Azure CLI IoT 확장 0.10.0

    자세히 알아보려면 [Azure CLI용 Azure IoT 확장 설치 및 사용](howto-use-iot-pnp-cli.md)을 참조하세요.

    > [!TIP]
    > Azure IoT 확장에는 디바이스를 인증하는 데 도움이 되는 명령이 포함되어 있습니다. `az iot product -h`을 참조하세요.

## <a name="libraries-and-sdks"></a>라이브러리 및 SDK

라이브러리 및 SDK에 대한 자세한 내용은 [IoT 플러그 앤 플레이용 Microsoft SDK](libraries-sdks.md)를 참조하세요.

- C 디바이스 SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- 임베디드 C 디바이스 SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET 디바이스 SDK [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java 디바이스 SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python 디바이스 SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js 디바이스 SDK [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - IoT Hub 서비스 [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - IoT Hub 서비스 [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - IoT Hub 서비스 [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - IoT Hub/Digital Twins 서비스 [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL 모델 파서 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)

## <a name="rest-apis"></a>REST API

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub)

자세히 알아보려면 [IoT 플러그 앤 플레이 개발자 가이드](concepts-developer-guide-service.md)를 참조하세요.

## <a name="iot-hub"></a>IoT Hub

IoT 플러그 앤 플레이는 모든 지역에서 IoT Hub로 지원됩니다. IoT 플러그 앤 플레이는 표준 또는 무료 계층 IoT 허브에서만 지원됩니다.

## <a name="announcements"></a>공지 사항

현재 및 이전 IoT 플러그 앤 플레이 공지는 다음 블로그 게시물을 참조하세요.

- [공개 미리 보기 새로 고침(2020년 8월 29일에 게시됨)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [IoT 플러그 앤 플레이에 대한 디바이스 준비 및 인증(2020년 8월 26일에 게시됨)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [현재 미리 보기로 제공되는 IoT 플러그 앤 플레이(2019년 8월 22일에 게시됨)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Azure IoT Central 및 IoT 플러그 앤 플레이를 사용하여 빌드(2019년 5월 7일에 게시됨)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)
