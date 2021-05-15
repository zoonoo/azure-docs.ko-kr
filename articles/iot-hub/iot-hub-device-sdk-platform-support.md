---
title: Azure IoT 디바이스 SDK 플랫폼 지원 | Microsoft Docs
description: C, .NET(C#), Java, Node.js, Python의 GitHub에서 디바이스를 Azure IoT Hub 및 DPS(디바이스 프로비저닝 서비스)에 연결하는 데 오픈 소스 디바이스 SDK를 사용할 수 있습니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: ab450c6ec6ae32cafcf07e88940736f209973d8d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168045"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 디바이스 SDK 플랫폼 지원

Microsoft는 Azure IoT Hub 지원 디바이스의 유니버스를 지속적으로 확장하기 위해 노력하고 있습니다. Microsoft는 GitHub에 오픈 소스 디바이스 SDK를 게시하여 디바이스를 Azure IoT Hub 및 디바이스 프로비저닝 서비스에 연결할 수 있도록 지원합니다. 디바이스 SDK는 C, .NET(C#), Java, Node.js, Python에서 사용할 수 있습니다. Microsoft는 각 SDK를 테스트하여 [Microsoft SDK 및 디바이스 플랫폼 지원](#microsoft-sdks-and-device-platform-support) 섹션에서 설명하는 지원되는 구성에서 실행되는지 확인합니다.

디바이스 SDK 이외에도 Microsoft는 고객과 개발자가 자신의 디바이스를 Azure IoT에 연결할 수 있는 몇 가지 다른 수단을 제공합니다.

* Microsoft는 여러 파트너 회사와 협업하여 하드웨어 플랫폼을 위해 Azure IoT C SDK에 따른 개발 키트를 게시할 수 있도록 지원합니다.

* Microsoft는 Microsoft 신뢰할 수 있는 파트너와 협력하여 Azure IoT에 대해 테스트되고 인증된 디바이스 세트를 지속적으로 제공합니다. 관련 디바이스의 최신 목록은 [IoT용 Azure Certified 디바이스 카탈로그](https://devicecatalog.azure.com/)를 참조하세요.

* Microsoft는 개발자가 SDK를 자신의 플랫폼으로 쉽게 포팅할 수 있도록 하는 Azure IoT Hub 디바이스 C SDK에 PAL(플랫폼 추상화 계층)을 제공합니다. 자세한 정보는 [C SDK 포팅 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)을 참조하세요.

이 항목에서는 Microsoft SDK 및 Microsoft SDK에서 지원하는 플랫폼 구성과 위에 나열된 다른 옵션에 대한 정보를 제공합니다.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK 및 디바이스 플랫폼 지원

Microsoft는 C, .NET(C#), Node.js, Java, Python 언어로 GitHub에 오픈 소스 SDK를 게시합니다. SDK 및 해당 종속성은 이 섹션에 나열되어 있습니다. SDK는 이러한 종속성을 충족하는 모든 디바이스 플랫폼에서 지원됩니다.

나열된 각 SDK에 대해 Microsoft는 다음을 수행합니다.

* 많이 사용되는 여러 플랫폼에서 GitHub에 있는 관련 SDK의 기본 분기에 대한 엔드투엔드 테스트를 지속적으로 빌드하고 실행합니다.  여러 컴파일러 버전에서 테스트를 제공하기 위해 일반적으로 최신 LTS 버전 및 가장 인기 있는 버전을 테스트합니다.

* 해당하는 경우 설치 지침 또는 설치 패키지를 제공합니다.

* 오픈 소스 코드, 고객 기여 경로 및 GitHub 문제에 대한 제품 팀 참여를 통해 GitHub의 SDK를 완전히 지원합니다.

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C 디바이스 SDK](https://github.com/Azure/azure-iot-sdk-c)는 다음 구성을 사용하여 테스트됩니다.

| OS                  | TLS 라이브러리                  | 추가 요구 사항                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL 또는 BearSSL | Berkeley 소켓</br></br>POSIX(포팅 가능한 운영 체제 인터페이스)                       |
| iOS 12.2            | OpenSSL                      | OSX 10.13.4에서 에뮬레이트된 XCode                                                               |
| Windows 10 제품군   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [MXChip IoT 개발 키트](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 또는 ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT Hub Python 디바이스 SDK](https://github.com/Azure/azure-iot-sdk-python)는 다음 구성을 사용하여 테스트됩니다.

| OS                  | 컴파일러                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.6 이상 |
| macOS High Sierra   | Python 2.7.*, 3.6 이상 |
| Windows 10 제품군   | Python 2.7.*, 3.6 이상 |

Python 버전 3.5.3 이상에서만 비동기 API를 지원하므로 버전 3.7 이상을 사용하는 것이 좋습니다.

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .NET(C#) 디바이스 SDK](https://github.com/Azure/azure-iot-sdk-csharp)는 다음 구성을 사용하여 테스트됩니다.

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop 및 Server SKU   | .NET Core 2.1, .NET Framework 4.5.1 또는 .NET Framework 4.7 |

.NET SDK는 [Azure 디바이스 에이전트](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) 또는 [RPC를 사용하여 UWP 애플리케이션과 통신할 수 있는 사용자 지정 NTService](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)와 함께 Windows IoT Core와도 사용할 수 있습니다.

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT Hub Node.js 디바이스 SDK](https://github.com/Azure/azure-iot-sdk-node)는 다음 구성을 사용하여 테스트됩니다.

| OS                  | 노드 버전    |
|---------------------|-----------------|
| Linux               | LTS 및 Current |
| Windows 10 제품군   | LTS 및 Current |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java 디바이스 SDK](https://github.com/Azure/azure-iot-sdk-java)는 다음 구성을 사용하여 테스트됩니다.

| OS                     | Java 버전 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 제품군 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>파트너 지원 개발 키트

Microsoft는 다양한 파트너와 협력하여 여러 마이크로프로세서 아키텍처에 대한 개발 키트를 제공합니다. 이러한 파트너는 Azure IoT C SDK를 플랫폼으로 포팅했습니다. 파트너는 SDK의 PAL(플랫폼 추상화 계층)을 만들고 유지 관리합니다. Microsoft는 파트너와 협력하여 확장된 지원을 제공합니다.

| 파트너             | 디바이스                            | 링크                     | 지원 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT 모뎀     | [IoT SDK용 Qualcomm LTE](https://developer.qualcomm.com/software/lte-iot-sdk) | [포럼](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 시리즈 <br/> STM32F4 시리즈 <br/>  STM32F7 시리즈 <br/>  IoT 노드용 STM32L4 Discovery Kit    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [지원](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [SimpleLink용 Azure IoT 플러그 인](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 포럼](https://e2e.ti.com) <br/> [CC3220에 대한 TI E2E 포럼](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4에 대한 TI E2E 포럼](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Microsoft Azure IoT C SDK 포팅

이전 섹션 중 하나에서 디바이스 플랫폼을 다루지 않은 경우 Azure IoT C SDK 포팅을 고려할 수 있습니다. C SDK 포팅은 주로 SDK의 PAL(플랫폼 추상화 계층)을 구현하는 것과 관련이 있습니다. PAL은 SDK에서 디바이스와 SDK의 상위 수준 기능 간 결합을 제공하는 기본 형식을 정의합니다. 자세한 내용은 [포팅 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)을 참조하세요.

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft 파트너 및 인증 Azure IoT 디바이스

Microsoft는 Azure IoT 테스트와 인증 디바이스로 Azure IoT 유니버스를 지속적으로 확장하기 위해 여러 파트너와 협력합니다.

* Azure IoT 인증 디바이스를 찾아보려면 [IoT용 Microsoft Azure Certified 디바이스 카탈로그](https://devicecatalog.azure.com/)를 참조하세요.

* IoT용 Azure Certified 에코시스템에 대해 자세한 정보는 [IoT용 Certified 에코시스템에 참여](../certification/overview.md)를 참조하세요.

## <a name="connecting-to-iot-hub-without-an-sdk"></a>SDK 없이 IoT Hub에 연결

IoT Hub 디바이스 SDK 중 하나를 사용할 수 없는 경우 지원되는 통신 프로토콜을 사용하여 IoT Hub에 직접 연결할 수 있습니다. 자세한 정보는 [Azure IoT SDK 없이 개발](iot-hub-devguide-no-sdk.md)을 참조하세요.

## <a name="support-and-other-resources"></a>지원 및 다른 리소스

Azure IoT 디바이스 서비스를 사용하는 동안 문제가 발생하는 경우 지원을 검색하는 몇 가지 방법이 아래에 요약되어 있습니다. 모든 지원 옵션에 대한 자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../iot-fundamentals/iot-support-help.md)을 참조하세요. 

**버그 보고** – 디바이스 SDK의 버그는 관련 GitHub 프로젝트의 문제 페이지에서 보고할 수 있습니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

* [Azure IoT Hub C SDK 문제](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET(C#) SDK 문제](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT Hub Java SDK 문제](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js SDK 문제](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT Hub Python SDK 문제](https://github.com/Azure/azure-iot-sdk-python/issues)

**기술 질문** – *azure-iot-sdk* 태그를 사용하여 [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) 및 [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-iot-sdk) 모두에 대한 기술 질문을 할 수 있습니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 직접 새로운 지원 요청을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT 기능은 제품의 [사용자 음성 페이지](https://feedback.azure.com/forums/321918-azure-iot)를 통해 추적할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)
* [이식 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
