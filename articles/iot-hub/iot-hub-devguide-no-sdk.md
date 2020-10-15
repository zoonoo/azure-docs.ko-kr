---
title: Azure IoT SDK 없이 개발 | Microsoft Docs
description: 개발자 가이드-Azure IoT SDK를 사용 하지 않고 장치 앱과 백 엔드 앱을 빌드하는 데 사용할 수 있는 항목에 대 한 정보 및 링크를 제공 합니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 3968f19329536169c3fb3eb1fbbaff99e99c293d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079651"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Azure IoT Hub SDK를 사용 하지 않고 개발

이 항목에서는 Azure IoT Sdk를 사용 하지 않고 장치 또는 백 엔드 앱을 개발 하려는 개발자를 위한 유용한 정보와 링크를 제공 합니다.

Microsoft는 Azure IoT SDK를 사용 하 여 강력한 조언를 합니다. Azure IoT 장치 및 서비스 Sdk는 널리 사용 되는 여러 플랫폼에 게시 됩니다. Sdk는 장치 연결 및 다시 연결을 포함 하 여 기본 통신 프로토콜의 복잡성을 대부분 처리 하는 편리한 계층을 제공 하 고 다시 시도 정책을 제공 합니다. Sdk는 IoT Hub에서 제공 하는 최신 기능 및 보안 업데이트를 제공 하도록 정기적으로 업데이트 됩니다. Sdk를 사용 하면 코드 유지 관리에 소요 되는 개발 시간과 시간을 줄일 수 있습니다. Azure IoT Sdk에 대해 자세히 알아보려면 [Azure Iot 장치 및 서비스 sdk](iot-hub-devguide-sdks.md)를 참조 하세요. Azure IoT SDK를 사용 하는 이점에 대 한 자세한 내용은 블로그 게시물을 사용 [하지 않는 경우 방지할 수 있는 Azure iot sdk 및 문제를 사용 하는 이점](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) 을 참조 하세요.

장치와의 통신을 위해 Websocket을 통한 amqp, Websocket을 통한 AMQP 및 websocket을 통한 AMQP를 지 원하는 IoT Hub 있지만 장치에서 지 원하는 경우 MQTT를 사용 하는 것이 좋습니다.

## <a name="development-prerequisites"></a>개발 필수 구성 요소

개발을 시작 하기 전에 장치 또는 백 엔드 앱에서 구현 하려는 IoT Hub 및 기능에 대 한 철저 한 지식이 있어야 합니다. 다음은 익숙한 항목의 매우 간략 한 목록입니다.

* IoT Hub에서 노출 한 끝점과 각 끝점에서 지원 되는 프로토콜을 이해 해야 합니다. 자세히 알아보려면 [IoT Hub 끝점](iot-hub-devguide-endpoints.md)을 참조 하세요.

* 장치 앱에 대해 선택 하는 프로토콜이 필요한 경우 MQTT를 사용 하는 것이 좋습니다. 그러나 프로토콜을 선택 하기 전에 각에서 적용 되는 제한 사항을 이해 해야 합니다. 자세히 알아보려면 [통신 프로토콜 선택](iot-hub-devguide-protocols.md)을 참조 하세요.

* IoT Hub 인증을 이해 하려면 [IoT Hub에 대 한 액세스 제어](iot-hub-devguide-security.md)를 참조 하세요.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>다른 프로토콜에 대 한 도움말

Azure IoT SDK 없이 다음 프로토콜을 사용 하는 데 도움이 필요 합니다.

* **Amqp**의 장치 또는 백 엔드 앱은 [amqp 지원](iot-hub-amqp-support.md)을 참조 하세요.

* **Mqtt**의 장치 앱에 대해서는 [mqtt 지원](iot-hub-mqtt-support.md)을 참조 하세요. 이 항목의 대부분은 MQTT 프로토콜을 직접 사용 하는 것을 처리 합니다. [IOT MQTT 샘플 리포지토리](https://github.com/Azure-Samples/IoTMQTTSample)를 사용 하는 방법에 대 한 정보도 포함 됩니다. 이 리포지토리에는 Eclipse Mosquitto 라이브러리를 사용 하 여 IoT Hub로 메시지를 보내는 C 샘플이 포함 되어 있습니다.

* **HTTPS**의 장치 또는 백 엔드 앱 [Azure IoT Hub REST api](https://docs.microsoft.com/rest/api/iothub/)를 참조 하세요. [개발 필수 구성 요소](#development-prerequisites)에서 설명한 대로 HTTPS로 x.509 CA (인증 기관) 인증을 사용할 수 없습니다.

장치의 경우 장치에서 지 원하는 경우 MQTT를 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [MQTT 지원](iot-hub-mqtt-support.md)
