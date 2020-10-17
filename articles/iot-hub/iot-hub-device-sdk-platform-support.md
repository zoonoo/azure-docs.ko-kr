---
title: Azure IoT 디바이스 SDK 플랫폼 지원 | Microsoft Docs
description: 'C, .NET (c #), Java, Node.js 및 Python의 GitHub에서 오픈 소스 장치 Sdk를 사용 하 여 장치를 Azure IoT Hub 및 DPS (장치 프로 비전 서비스)에 연결할 수 있습니다.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 5df36cdb3c02fd4c813625c4869c21581f009b02
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152225"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 장치 Sdk 플랫폼 지원

Microsoft는 Azure IoT Hub 지원 장치의 universe를 지속적으로 확장 하기 위해 노력 하 고 있습니다. Microsoft는 GitHub에 오픈 소스 장치 Sdk를 게시 하 여 장치를 Azure IoT Hub 및 장치 프로 비전 서비스에 연결할 수 있도록 지원 합니다. 장치 Sdk는 C, .NET (c #), Java, Node.js 및 Python에 사용할 수 있습니다. Microsoft는 각 SDK를 테스트 하 여 [Microsoft sdk 및 장치 플랫폼 지원](#microsoft-sdks-and-device-platform-support) 섹션에서 설명 하는 지원 되는 구성에서 실행 되는지 확인 합니다.

장치 Sdk 외에도 Microsoft는 고객과 개발자가 자신의 장치를 Azure IoT에 연결할 수 있는 몇 가지 다른 수단을 제공 합니다.

* Microsoft는 다양 한 파트너 회사와 협업 하 여 해당 하드웨어 플랫폼용 Azure IoT C SDK에 따라 개발 키트를 게시할 수 있도록 지원 합니다.

* Microsoft는 Microsoft 신뢰할 수 있는 파트너와 협력 하 여 Azure IoT에 대해 테스트 되 고 인증 된 장치 집합을 지속적으로 제공 합니다. 이러한 장치의 최신 목록은 [Azure IoT 장치 카탈로그에 대 한 인증](https://catalog.azureiotsolutions.com/)을 참조 하세요.

* Microsoft는 개발자가 SDK를 플랫폼으로 쉽게 이식할 수 있도록 하는 Azure IoT Hub 장치 C SDK에서 PAL (플랫폼 추상화 계층)을 제공 합니다. 자세히 알아보려면 [C SDK 포팅 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)을 참조 하세요.

이 항목에서는 Microsoft Sdk 및 Microsoft Sdk에서 지 원하는 플랫폼 구성과 위에 나열 된 다른 옵션에 대 한 정보를 제공 합니다.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft Sdk 및 장치 플랫폼 지원

Microsoft는 C, .NET (c #), Node.js, Java 및 Python 언어로 GitHub에 오픈 소스 Sdk를 게시 합니다. 이 섹션에는 Sdk 및 해당 종속성이 나열 되어 있습니다. Sdk는 이러한 종속성을 충족 하는 모든 장치 플랫폼에서 지원 됩니다.

나열 된 각 Sdk에 대해 Microsoft:

* 는 널리 사용 되는 여러 플랫폼에서 GitHub에 있는 관련 SDK의 마스터 분기에 대 한 종단 간 테스트를 지속적으로 빌드하고 실행 합니다.  여러 컴파일러 버전에서 테스트 검사를 제공 하기 위해 일반적으로 최신 LTS 버전 및 가장 인기 있는 버전을 테스트 합니다.

* 해당 하는 경우 설치 지침 또는 설치 패키지를 제공 합니다.

* 는 오픈 소스 코드, 고객 기여 경로 및 GitHub 문제를 포함 한 제품 팀 참여를 포함 하는 GitHub에서 Sdk를 완벽 하 게 지원 합니다.

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C 장치 SDK](https://github.com/Azure/azure-iot-sdk-c) 는로 테스트 되 고 다음과 같은 구성을 지원 합니다.

| OS                  | TLS 라이브러리                  | 추가 요구 사항                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL 또는 BearSSL | Berkeley 소켓</br></br>POSIX (이식 가능한 운영 체제 인터페이스)                       |
| iOS 12.2            | OpenSSL                      | OSX 10.13.4에서 에뮬레이트된 XCode                                                               |
| Windows 10 제품군   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [MXChip IoT dev kit](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 또는 ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT Hub Python 장치 SDK](https://github.com/Azure/azure-iot-sdk-python) 는를 사용 하 여 테스트 하 고 다음 구성을 지원 합니다.

| OS                  | 컴파일러                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7. *, 3.5 이상 |
| macOS High Sierra   | Python 2.7. *, 3.5 이상 |
| Windows 10 제품군   | Python 2.7. *, 3.5 이상 |

Python 버전 3.5.3 이상 에서만 비동기 Api를 지원 하므로 버전 3.7 이상을 사용 하는 것이 좋습니다.

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .net (c #) 장치 SDK](https://github.com/Azure/azure-iot-sdk-csharp) 는로 테스트 되 고 다음과 같은 구성을 지원 합니다.

| OS                                   | 표준                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop 및 Server Sku   | .NET Core 2.1, .NET Framework 4.5.1 또는 .NET Framework 4.7 |

.NET SDK는 [RPC를 사용 하 여 UWP 응용 프로그램과 통신할 수 있는](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/) [Azure 장치 에이전트](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) 또는 사용자 지정 Ntservice에서 Windows IoT Core와 함께 사용할 수도 있습니다.

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT Hub Node.js 장치 SDK](https://github.com/Azure/azure-iot-sdk-node) 는를 사용 하 여 테스트 하 고 다음 구성을 지원 합니다.

| OS                  | 노드 버전    |
|---------------------|-----------------|
| Linux               | LTS 및 Current |
| Windows 10 제품군   | LTS 및 Current |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java 장치 SDK](https://github.com/Azure/azure-iot-sdk-java) 는를 사용 하 여 테스트 하 고 다음 구성을 지원 합니다.

| OS                     | Java 버전 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 제품군 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>파트너 지원 개발 키트

Microsoft는 다양 한 파트너와 협력 하 여 여러 마이크로프로세서 아키텍처에 대 한 개발 키트를 제공 합니다. 이러한 파트너는 Azure IoT C SDK를 플랫폼으로 이식 했습니다. 파트너는 SDK의 PAL (플랫폼 추상화 계층)을 만들고 유지 관리 합니다. Microsoft는 이러한 파트너와 협력 하 여 확장 된 지원을 제공 합니다.

| 파트너             | 디바이스                            | 링크                     | 지원 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT 모뎀     | [IoT SDK용 Qualcomm LTE](https://developer.qualcomm.com/software/lte-iot-sdk) | [포럼](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 시리즈 <br/> STM32F4 시리즈 <br/>  STM32F7 시리즈 <br/>  IoT 노드용 STM32L4 Discovery Kit    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [지원](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF 실행 패드 </br> CC3220S 실행 패드 </br> CC3235SF 실행 패드 </br> CC3235S 실행 패드 </br> MSP432E4 실행 패드 | [SimpleLink용 Azure IoT 플러그 인](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 포럼](https://e2e.ti.com) <br/> [CC3220에 대한 TI E2E 포럼](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4에 대한 TI E2E 포럼](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Microsoft Azure IoT C SDK 포팅

장치 플랫폼에 이전 섹션 중 하나가 적용 되지 않는 경우 Azure IoT C SDK 포팅을 고려할 수 있습니다. C SDK를 이식할 때 주로 SDK의 PAL (platform 추상화 계층)을 구현 해야 합니다. PAL은 장치 간의 붙이기를 제공 하 고 SDK의 상위 수준 기능을 제공 하는 기본 형식을 정의 합니다. 자세한 내용은 [포팅 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)을 참조 하세요.

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft 파트너 및 인증 된 Azure IoT 장치

Microsoft는 Azure IoT 테스트와 인증 된 장치로 Azure IoT universe를 지속적으로 확장 하기 위해 많은 파트너와 협력 합니다.

* Azure IoT 인증 장치를 찾아보려면 [IoT용 Microsoft Azure Certified 장치 카탈로그](https://catalog.azureiotsolutions.com/)를 참조 하세요.

* IoT용 Azure Certified 에코 시스템에 대해 자세히 알아보려면 [IoT용 Certified 에코 시스템에 참여](https://catalog.azureiotsolutions.com/register)를 참조 하세요.

## <a name="connecting-to-iot-hub-without-an-sdk"></a>SDK 없이 IoT Hub에 연결

IoT Hub 장치 Sdk 중 하나를 사용할 수 없는 경우 지원 되는 통신 프로토콜을 사용 하 여 IoT Hub에 직접 연결할 수 있습니다. 자세히 알아보려면 [Azure IOT SDK를 사용 하지 않고 개발](iot-hub-devguide-no-sdk.md)을 참조 하세요.

## <a name="support-and-other-resources"></a>지원 및 다른 리소스

Azure IoT 장치 Sdk를 사용 하는 동안 문제가 발생 하는 경우 아래 요약 된 지원을 검색 하는 몇 가지 방법이 있습니다. 모든 지원 옵션에 대 한 자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../iot-fundamentals/iot-support-help.md)을 참조 하세요. 

**버그 보고** – 장치 sdk의 버그는 관련 GitHub 프로젝트의 문제 페이지에서 보고할 수 있습니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

* [Azure IoT Hub C SDK 문제](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (c #) SDK 문제](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Java SDK 문제 Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js SDK 문제](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Python SDK 문제 Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-python/issues)

**기술 질문** – [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) 와 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) 태그를 사용 하 여 기술 관련 질문을 할 *수 있습니다.*

**Microsoft 고객 지원 팀** - [지원 계획이](https://azure.microsoft.com/support/plans/) 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 직접 새 지원 요청을 만들어 Microsoft 고객 지원 팀에 참여할 수 있습니다.

**기능 요청** – Azure IoT 기능 요청은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/321918-azure-iot)를 통해 추적 됩니다.

## <a name="next-steps"></a>다음 단계

* [디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)
* [이식 지침](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)