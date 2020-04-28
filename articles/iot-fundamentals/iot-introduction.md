---
title: Azure IoT(사물 인터넷) 소개
description: IoT 사용법을 설명하는 예제를 포함하여 Azure IoT 및 IoT 서비스의 기본 사항을 소개합니다.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728996"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Azure IoT(사물 인터넷)란?

Azure IoT(사물 인터넷)는 수십억 개의 IoT 자산을 연결, 모니터링 및 제어하는 Microsoft 관리 클라우드 서비스의 모음입니다. 간단히 말해, IoT 솔루션은 클라우드에서 호스팅되는 하나 이상의 백 엔드 서비스와 통신하는 하나 이상의 IoT 디바이스로 구성됩니다. 

## <a name="iot-devices"></a>IoT 디바이스

IoT 디바이스는 일반적으로 인터넷에 연결되는 WiFi를 사용하는 센서가 부착된 회로 기판으로 구성되어 있습니다. 다음은 그 예입니다.

* 원격 오일 펌프의 압력 센서
* 에어컨 장치의 온도 및 습도 센서
* 엘리베이터의 가속도계
* 방의 감지 센서

다른 제조업체의 다양한 디바이스를 사용하여 솔루션을 빌드할 수 있습니다. Azure IoT Hub와 함께 작동하도록 인증된 디바이스 목록은 [IoT용 Azure Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/alldevices)를 참조하세요. 프로토타입 생성을 위해 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 또는 [Raspberry Pi](https://www.raspberrypi.org/) 등의 디바이스를 사용할 수 있습니다. Devkit에는 온도, 압력, 습도 및 자이로스코프가, 가속도계 및 지자기 센터에 대한 기본 제공 센서가 있습니다. Raspberry Pi를 사용하면 다양한 유형의 센서를 연결할 수 있습니다. 

Microsoft는 디바이스에서 실행되는 앱을 빌드하는 데 사용할 수 있는 오픈 소스 [디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 제공합니다. 이러한 [SDK는 IoT 솔루션 개발을 간소화하고 가속화](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)합니다.

## <a name="communication"></a>통신

일반적으로 IoT 디바이스는 센서의 원격 분석을 클라우드의 백 엔드 서비스로 보냅니다. 그러나 백엔드 서비스에서 디바이스에 명령을 보내는 것과 같은 다른 유형의 통신이 가능합니다. 다음은 디바이스-클라우드 및 클라우드-디바이스 통신의 몇 가지 예제입니다.

* 이동식 냉동 트럭은 5분마다 IoT Hub로 온도를 전송합니다. 

* 백 엔드 서비스는 디바이스에 명령을 전송하여 문제를 진단하는 데 도움이 되는 원격 분석을 전송하는 빈도를 변경합니다. 

* 디바이스는 해당 센서에서 읽은 값을 기반으로 하여 경고를 보냅니다. 예를 들어 화학 공장의 회분식 반응기를 모니터링하는 디바이스는 온도가 특정 값을 초과할 때 경고를 보냅니다.

* 디바이스가 대시보드로 정보를 보내 운영자가 확인할 수 있습니다. 예를 들어 정제소의 제어실에서는 운영자가 시설을 모니터링할 수 있도록 각 파이프의 온도, 압력 및 흐름 볼륨을 표시할 수 있습니다. 

[IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md) 및 IoT Hub는 HTTP, MQTT 및 AMQP와 같은 일반적인 [통신 프로토콜](../iot-hub/iot-hub-devguide-protocols.md)을 지원합니다.

IoT 디바이스는 브라우저 및 모바일 앱과 같은 다른 클라이언트에 비해 다른 특징을 가집니다. 디바이스 SDK는 백 엔드 서비스에 안전하고 안정적으로 디바이스를 연결하는 문제를 해결하는 데 도움이 됩니다.  IoT 디바이스의 이러한 특징은 구체적으로 다음과 같습니다.

* 인간 조작자가 없는 내장 시스템인 경우가 많습니다(전화기와 다름).
* 물리적 액세스에 많은 비용이 드는 원격 위치에 배포될 수 있습니다.
* 솔루션 백 엔드를 통해서만 연결 가능합니다.
* 전원 및 리소스 처리 제한이 있을 수 있습니다.
* 일시적이거나 느리거나 비용이 많이 드는 네트워크 연결이 있을 수 있습니다.
* 독점, 사용자 지정 또는 업계 특정 애플리케이션 프로토콜을 사용해야 할 수 있습니다.

## <a name="back-end-services"></a>백 엔드 서비스 

IoT 솔루션에서 백 엔드 서비스는 다음과 같은 기능을 제공합니다.

* 디바이스로부터 원격 분석 데이터를 대량으로 받고, 해당 데이터를 처리하고 저장하는 방법을 결정합니다.
* 실시간 또는 사후에 인사이트를 제공하기 위해 원격 분석 데이터를 분석합니다.
* 클라우드에서 특정 디바이스로 명령을 보냅니다. 
* 디바이스를 프로비저닝하고, 인프라에 연결할 수 있는 디바이스를 제어합니다.
* 디바이스의 상태를 제어하고, 활동을 모니터링합니다.
* 디바이스에 설치된 펌웨어를 관리합니다.

예를 들어, 오일 펌프장을 위한 원격 모니터링 솔루션에서 클라우드 백 엔드는 펌프의 원격 측정 기능을 사용하여 비정상적인 동작을 식별합니다. 백 엔드 서비스는 이상 징후를 식별하면 자동으로 디바이스에 명령을 다시 전송하여 시정조치를 취할 수 있습니다. 이 프로세스는 디바이스와 클라우드 간에 자동화된 피드백 루프를 생성하여 솔루션의 효율성을 크게 높입니다.

## <a name="azure-iot-examples"></a>Azure IoT 예제

조직에서 Azure IoT를 사용하는 방법에 대한 실제 사례는 [IoT를 위한 Microsoft 기술 사례 연구](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

몇 가지 실제 비즈니스 사례와 사용된 아키텍처는 [Microsoft Azure IoT 기술 사례 연구](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)를 참조하세요.

IoT DevKit를 통해 사용해 볼 수 있는 몇 가지 샘플 프로젝트는 [IoT DevKit 프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)를 참조하세요. 

다양한 서비스 및 사용 방법에 대한 포괄적인 설명은 [Azure IoT 서비스 및 기술](iot-services-and-technologies.md)을 참조하세요.

IoT 아키텍처에 대한 심층적인 설명은 [Microsoft Azure IoT 참조 아키텍처](https://aka.ms/iotrefarchitecture)를 참조하세요.
