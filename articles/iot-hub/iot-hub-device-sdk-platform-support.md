---
title: Azure IoT 디바이스 SDK 플랫폼 지원 | Microsoft Docs
description: 오픈 소스 장치 SDK는 GitHub에서 C, .NET(C#), Java, Node.js 및 Python에서 장치를 Azure IoT 허브 및 장치 프로비저닝 서비스(DPS)에 연결할 수 있습니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045127"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 장치 SDK 플랫폼 지원

Microsoft는 Azure IoT Hub 지원 장치의 유니버스를 지속적으로 확장하기 위해 노력하고 있습니다. Microsoft는 Azure IoT Hub 및 장치 프로비저닝 서비스에 장치를 연결하는 데 도움이 되는 오픈 소스 장치 SDK를 GitHub에 게시합니다. 장치 SDK는 C, .NET(C#), Java, Node.js 및 파이썬에서 사용할 수 있습니다. Microsoft는 각 SDK를 테스트하여 [Microsoft SDK 및 장치 플랫폼 지원](#microsoft-sdks-and-device-platform-support) 섹션에서 해당 구성에 대해 자세히 설명한 지원되는 구성에서 실행되도록 합니다.

Microsoft는 장치 SDK 외에도 고객과 개발자가 Azure IoT에 장치를 연결할 수 있는 몇 가지 다른 방법을 제공합니다.

* Microsoft는 여러 파트너 회사와 협력하여 하드웨어 플랫폼에 대한 Azure IoT C SDK를 기반으로 개발 키트를 게시할 수 있도록 지원합니다.

* Microsoft는 Microsoft신뢰할 수 있는 파트너와 협력하여 Azure IoT에 대해 테스트 및 인증된 장치 집합을 계속 확장합니다. 이러한 장치의 현재 목록은 [IoT 장치 카탈로그에 대해 인증된 Azure를](https://catalog.azureiotsolutions.com/)참조하십시오.

* Microsoft는 개발자가 SDK를 플랫폼에 쉽게 포위한 다는 것을 돕는 Azure IoT 허브 장치 C SDK에 PAL(플랫폼 추상화 계층)을 제공합니다. 자세한 내용은 C [SDK 포팅 지침을](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)참조하십시오.

이 항목에서는 Microsoft SDK 및 지원하는 플랫폼 구성과 위에 나열된 각 다른 옵션에 대한 정보를 제공합니다.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK 및 장치 플랫폼 지원

Microsoft는 C, .NET(C#), Node.js, Java 및 Python의 다음 언어에 대해 GitHub에 오픈 소스 SDK를 게시합니다. SDK 및 해당 종속성은 이 섹션에 나열됩니다. SDK는 이러한 종속성을 조정하는 모든 장치 플랫폼에서 지원됩니다.

나열된 각 SDK에 대해 Microsoft는 다음을 수행합니다.

* 여러 인기 있는 플랫폼에서 GitHub의 관련 SDK의 마스터 분기에 대해 종단 간 테스트를 지속적으로 빌드하고 실행합니다.  다른 컴파일러 버전에 걸쳐 테스트 범위를 제공하기 위해 일반적으로 최신 LTS 버전과 가장 인기 있는 버전에 대해 테스트합니다.

* 해당하는 경우 설치 지침 또는 설치 패키지를 제공합니다.

* 오픈 소스 코드, 고객 기여 경로 및 GitHub 문제에 대한 제품 팀 참여를 통해 GitHub의 SDK를 완벽하게 지원합니다.

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C 장치 SDK는](https://github.com/Azure/azure-iot-sdk-c) 다음 구성으로 테스트되고 지원됩니다.

| OS                  | TLS 라이브러리                  | 추가 요구 사항                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, 늑대SSL 또는 베어SSL | 버클리 소켓</br></br>휴대용 운영 체제 인터페이스(POSIX)                       |
| 아이폰 OS 12.2            | OpenSSL                      | OSX 10.13.4에서 에뮬레이트된 XCode                                                               |
| 윈도우 10 가족   | SChannel                     |                                                                                             |
| 음파 OS 5.4         | 음의 TLS 2                   | [MXChip IoT 개발 키트](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure 구 OS     | 울프SSL                      | [Azure 구 MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | 베어스셀                      | [ESP32 또는 ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT Hub 파이썬 장치 SDK는](https://github.com/Azure/azure-iot-sdk-python) 다음 구성으로 테스트되고 지원됩니다.

| OS                  | 컴파일러                          |
|---------------------|-----------------------------------|
| Linux               | 파이썬 2.7.*, 3.5 이상 |
| MacOS High Sierra   | 파이썬 2.7.*, 3.5 이상 |
| 윈도우 10 가족   | 파이썬 2.7.*, 3.5 이상 |

파이썬 버전 3.5.3 이상만 비동기 API를 지원하므로 버전 3.7 이상에서 사용하는 것이 좋습니다.

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .NET(C#) 장치 SDK는](https://github.com/Azure/azure-iot-sdk-csharp) 다음 구성으로 테스트되고 지원됩니다.

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| 윈도우 10 데스크톱 및 서버 SCO   | .NET 코어 2.1, .NET 프레임워크 4.5.1 또는 .NET 프레임워크 4.7 |

.NET SDK는 [Azure 장치 에이전트또는](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) [RPC를 사용하여 UWP 응용 프로그램과 통신할 수 있는 사용자 지정 NTService와](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)함께 Windows IoT 코어와 함께 사용할 수도 있습니다.

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT Hub Node.js 장치 SDK는](https://github.com/Azure/azure-iot-sdk-node) 다음 구성으로 테스트되고 지원됩니다.

| OS                  | 노드 버전    |
|---------------------|-----------------|
| Linux               | LTS 및 전류 |
| 윈도우 10 가족   | LTS 및 전류 |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java 장치 SDK는](https://github.com/Azure/azure-iot-sdk-java) 다음 구성으로 테스트되고 지원됩니다.

| OS                     | Java 버전 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| 리눅스 x64             | Java 8       |
| 윈도우 10 가족 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>파트너 지원 개발 키트

Microsoft는 다양한 파트너와 협력하여 여러 마이크로프로세서 아키텍처에 대한 개발 키트를 제공합니다. 이러한 파트너는 Azure IoT C SDK를 플랫폼에 포위한 것입니다. 파트너는 SDK의 플랫폼 추상화 계층(PAL)을 만들고 유지 관리합니다. Microsoft는 이러한 파트너와 협력하여 확장 지원을 제공합니다.

| 파트너             | 디바이스                            | 링크                     | 고객 지원팀 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT 모뎀     | [IoT SDK용 Qualcomm LTE](https://developer.qualcomm.com/software/lte-iot-sdk) | [포럼](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 시리즈 <br/> STM32F4 시리즈 <br/>  STM32F7 시리즈 <br/>  IoT 노드용 STM32L4 Discovery Kit    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [지원](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF 런치패드 </br> CC3220S 런치패드 </br> CC3235SF 런치패드 </br> CC3235S 런치패드 </br> MSP432E4 런치패드 | [SimpleLink용 Azure IoT 플러그 인](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 포럼](https://e2e.ti.com) <br/> [CC3220에 대한 TI E2E 포럼](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4에 대한 TI E2E 포럼](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>마이크로소프트 Azure IoT C SDK 포팅

장치 플랫폼이 이전 섹션 중 하나에 포함되지 않는 경우 Azure IoT C SDK를 이식하는 것을 고려할 수 있습니다. C SDK 포팅은 주로 SDK의 플랫폼 추상화 계층(PAL)을 구현하는 것입니다. PAL은 장치와 SDK의 상위 수준 기능 사이에 접착제를 제공하는 프리미티브를 정의합니다. 자세한 내용은 [포팅 지침을](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)참조하십시오.

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>마이크로소프트 파트너 및 인증된 Azure IoT 장치

Microsoft는 여러 파트너와 협력하여 Azure IoT 테스트 및 인증 된 장치를 사용하여 Azure IoT 유니버스를 지속적으로 확장합니다.

* Azure IoT 인증 장치를 찾아보려면 [IoT 장치 카탈로그에 대해 Microsoft Azure 인증을 참조하십시오.](https://catalog.azureiotsolutions.com/)

* IoT 에코시스템에 대한 Azure 인증에 대한 자세한 내용은 [IoT 에코시스템에 대한 인증 된 가입을](https://catalog.azureiotsolutions.com/register)참조하십시오.

## <a name="connecting-to-iot-hub-without-an-sdk"></a>SDK 없이 IoT 허브에 연결

IoT Hub 장치 SDK 중 하나를 사용할 수 없는 경우 HTTPS 요청 및 응답을 송수신할 수 있는 모든 응용 프로그램에서 [IoT Hub REST API를](https://docs.microsoft.com/rest/api/iothub/) 사용하여 IoT Hub에 직접 연결할 수 있습니다.

## <a name="support-and-other-resources"></a>지원 및 기타 리소스

Azure IoT 장치 SDK를 사용하는 동안 문제가 발생하는 경우 지원을 요청하는 몇 가지 방법이 있습니다. 다음 채널 중 하나를 사용해 볼 수 있습니다.

**버그 보고** – 장치 SDK의 버그는 관련 GitHub 프로젝트의 문제 페이지에서 보고할 수 있습니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

* [Azure IoT 허브 C SDK 문제](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET(C#) SDK 문제](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT 허브 자바 SDK 문제](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT 허브 Node.js SDK 문제](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT 허브 파이썬 SDK 문제](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft 고객 지원 팀** - [지원 계획이](https://azure.microsoft.com/support/plans/) 있는 사용자는 [Azure 포털에서](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)직접 새 지원 요청을 만들어 Microsoft 고객 지원 팀에 참여할 수 있습니다.

**기능 요청** - Azure IoT 기능 요청은 제품의 [사용자 음성 페이지를](https://feedback.azure.com/forums/321918-azure-iot)통해 추적됩니다.

## <a name="next-steps"></a>다음 단계

* [디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)
* [이식 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
