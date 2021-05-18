---
title: IoT Hub에서 Azure IoT Central로 디바이스를 이동하는 방법
description: IoT Hub에서 Azure IoT Central로 디바이스를 이동하는 방법
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872734"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>IoT Hub에서 Azure IoT Central로 디바이스를 이동하는 방법

이 문서는 운영자 및 디바이스 개발자에게 적용됩니다.  

이 문서에서는 IoT Hub에서 Azure IoT Central 애플리케이션으로 디바이스를 이동하는 방법을 설명합니다. 

디바이스는 먼저 DPS 엔드포인트에 연결하여 애플리케이션에 연결하는 데 필요한 정보를 검색합니다. 내부적으로 IoT Central 애플리케이션은 IoT Hub를 사용하여 디바이스 연결을 처리합니다.  

디바이스는 DPS를 사용하거나 연결 문자열을 사용하여 직접 IoT Hub에 연결할 수 있습니다. [Azure IoT Hub DPS(Device Provisioning Service)](../../iot-dps/about-iot-dps.md)는 IoT Central 경로입니다.

## <a name="to-move-the-device-to-azure-iot-central"></a>Azure IoT Central로 디바이스를 이동하려면

IoT Hub에서 IoT Central로 디바이스를 연결하려면 다음을 사용하여 디바이스를 업데이트해야 합니다.

* IoT Central 애플리케이션의 [범위 ID](../../iot-dps/concepts-service.md)
* [그룹 SAS](concepts-get-connected.md) 키 또는 [X.509 인증서](../../iot-hub/iot-hub-x509ca-overview.md)에서 파생된 키

IoT Central을 조작하려면 디바이스에서 구현하는 속성/원격 분석/명령을 모델링하는 디바이스 템플릿이 있어야 합니다. 자세한 내용은 [IoT Central에 연결](concepts-get-connected.md) 및 [디바이스 템플릿이란?](concepts-device-templates.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

디바이스 개발자라면 다음과 같은 몇 가지 단계를 살펴보세요.

- [자습서: 클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)에서 SAS 토큰을 사용하는 방법을 보여 주는 샘플 코드를 검토합니다.
- [IoT Central 애플리케이션용 Node.js 디바이스 SDK를 사용하여 X.509 인증서로 디바이스를 연결하는 방법](how-to-connect-devices-x509.md)을 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
- [Azure IoT Central 애플리케이션에서 새 IoT 디바이스 유형을 정의](./howto-set-up-template.md)하는 방법을 알아봅니다.
- [Azure IoT Edge 디바이스 및 Azure IoT Central](./concepts-iot-edge.md)을 참조합니다.