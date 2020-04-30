---
title: Azure IoT Edge용 모듈 개발 | Microsoft Docs
description: 런타임 및 IoT Hub과 통신할 수 있는 Azure IoT Edge용 사용자 지정 모듈을 개발합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79271383"
---
# <a name="develop-your-own-iot-edge-modules"></a>사용자 고유의 IoT Edge 모듈 개발

Azure IoT Edge 모듈은 다른 Azure 서비스와 연결하고 더 큰 클라우드 데이터 파이프라인에 속할 수 있습니다. 이 문서에서는 IoT Edge 런타임 및 IoT Hub, 나머지 Azure 클라우드와 통신하기 위한 모듈을 개발하는 방법을 설명합니다.

## <a name="iot-edge-runtime-environment"></a>IoT Edge 런타임 환경

IoT Edge 런타임은 여러 IoT Edge 모듈의 기능을 통합하고 이를 IoT Edge 디바이스에 배포하기 위한 인프라를 제공합니다. 모든 프로그램은 IoT Edge 모듈로 패키지할 수 있습니다. IoT Edge 통신 및 관리 기능을 완벽 하 게 활용 하기 위해 모듈에서 실행 되는 프로그램은 Azure IoT 장치 SDK를 사용 하 여 로컬 IoT Edge 허브에 연결할 수 있습니다.

## <a name="using-the-iot-edge-hub"></a>IoT Edge 허브 사용

IoT Edge 허브는 IoT Hub에 프록시 및 로컬 통신이라는 두 가지 주요 기능을 제공합니다.

### <a name="iot-hub-primitives"></a>IoT Hub 기본 형식

IoT Hub는 다음과 같은 점에서 디바이스와 비슷하게 모듈 인스턴스를 표시합니다.

* [장치](../iot-hub/iot-hub-devguide-device-twins.md) 쌍 및 해당 장치의 다른 모듈 쌍에서 분리 되 고 격리 된 모듈 쌍이 있습니다.
* [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messaging.md)을 보낼 수 있습니다.
* 특별히 해당 ID에서 대상으로 지정된 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)를 받을 수 있습니다.

현재 모듈은 클라우드-장치 메시지를 받거나 파일 업로드 기능을 사용할 수 없습니다.

모듈을 작성 하는 경우 [Azure IoT 장치 SDK](../iot-hub/iot-hub-devguide-sdks.md) 를 사용 하 여 IoT Edge 허브에 연결 하 고 장치 응용 프로그램에서 IoT Hub를 사용할 때와 같이 위의 기능을 사용할 수 있습니다. IoT Edge 모듈과 IoT 장치 응용 프로그램 간의 유일한 차이점은 장치 id 대신 모듈 id를 참조 해야 한다는 것입니다.

### <a name="device-to-cloud-messages"></a>디바이스-클라우드 메시지

IoT Edge 허브는 디바이스-클라우드 메시지의 복잡한 처리를 사용할 수 있게 하기 위해 모듈 간 메시지 및 모듈과 IoT Hub 간 메시지의 선언적 라우팅을 제공합니다. 선언적 라우팅을 사용하면 모듈은 다른 모듈에서 보낸 메시지를 가로채고, 처리하고, 복잡한 파이프라인에 전파할 수 있습니다. 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로 설정](module-composition.md)을 참조하세요.

일반 IoT Hub 장치 응용 프로그램과는 달리 IoT Edge 모듈은 로컬 IoT Edge 허브에서 프록시를 처리 하는 장치-클라우드 메시지를 받을 수 있습니다.

IoT Edge 허브는 [배포 매니페스트](module-composition.md)에서 설명하는 선언적 경로에 따라 메시지를 모듈에 전파합니다. IoT Edge 모듈을 개발할 경우 메시지 처리기를 설정하여 이러한 메시지를 받을 수 있습니다.

IoT Edge는 경로 만들기를 간소화하기 위해 모듈 *입력* 및 *출력* 엔드포인트의 개념을 추가합니다. 모듈은 입력을 지정하지 않고 라우팅되는 모든 디바이스-클라우드 메시지를 받을 수 있으며, 출력을 지정하지 않고 디바이스-클라우드 메시지를 보낼 수 있습니다. 그러나 명시적 입력 및 출력을 사용하면 라우팅 규칙을 더 쉽게 이해할 수 있습니다.

마지막으로 Edge 허브에서 처리하는 디바이스-클라우드 메시지는 다음 시스템 속성으로 스탬프 처리됩니다.

| 속성 | Description |
| -------- | ----------- |
| $connectionDeviceId | 메시지를 보낸 클라이언트의 디바이스 ID입니다. |
| $connectionModuleId | 메시지를 보낸 모듈의 모듈 ID입니다. |
| $inputName | 이 메시지를 받은 입력입니다. 비어 있을 수 있습니다. |
| $outputName | 메시지를 보내는 데 사용된 출력입니다. 비어 있을 수 있습니다. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>모듈에서 IoT Edge 허브에 연결

모듈에서 로컬 IoT Edge 허브로의 연결은 두 단계로 이루어집니다.

1. 애플리케이션에서 ModuleClient 인스턴스를 만듭니다.
2. 애플리케이션이 해당 디바이스의 IoT Edge 허브에서 제공한 인증서를 허용하는지 확인합니다.

DeviceClient 인스턴스가 IoT Hub에 IoT 디바이스를 연결하는 방법과 비슷하게 디바이스에서 실행 중인 IoT Edge 허브에 모듈을 연결하는 ModuleClient 인스턴스를 만듭니다. ModuleClient 클래스 및 해당 통신 방법에 대 한 자세한 내용은 [c #](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [c](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)또는 [node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)와 같은 기본 SDK 언어에 대 한 API 참조를 참조 하세요.

## <a name="language-and-architecture-support"></a>언어 및 아키텍처 지원

IoT Edge는 요구 사항과 일치 하는 시나리오를 구축할 수 있도록 여러 운영 체제, 장치 아키텍처 및 개발 언어를 지원 합니다. 이 섹션을 사용 하 여 사용자 지정 IoT Edge 모듈을 개발 하기 위한 옵션을 이해할 수 있습니다. [IoT Edge에 대 한 개발 및 테스트 환경 준비](development-environment.md)에서 각 언어에 대 한 도구 지원 및 요구 사항에 대해 자세히 알아볼 수 있습니다.

### <a name="linux"></a>Linux

다음 표의 모든 언어에 대해 IoT Edge는 AMD64 및 ARM32 Linux 장치에 대 한 개발을 지원 합니다.

| 개발 언어 | 개발 도구 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>ARM64 Linux 장치에 대 한 개발 및 디버깅은 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공 됩니다. 자세한 내용은 [Visual Studio Code(미리 보기)에서 ARM64 IoT Edge 모듈 개발 및 디버그](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)를 참조하세요.

### <a name="windows"></a>Windows

다음 표의 모든 언어에 대해 IoT Edge는 AMD64 Windows 장치 개발을 지원 합니다.

| 개발 언어 | 개발 도구 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (디버깅 기능 없음)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>다음 단계

[IoT Edge를 위한 개발 및 테스트 환경 준비](development-environment.md)

[Visual Studio를 사용 하 여 IoT Edge에 대 한 c # 모듈 개발](how-to-visual-studio-develop-module.md)

[Visual Studio Code를 사용 하 여 IoT Edge 모듈 개발](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDK 이해 및 사용](../iot-hub/iot-hub-devguide-sdks.md)
