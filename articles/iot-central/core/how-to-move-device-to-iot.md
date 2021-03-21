---
title: IoT Hub에서 장치를 Azure IoT Central로 이동 하는 방법
description: IoT Hub에서 장치를 Azure IoT Central로 이동 하는 방법
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 543c3f1c72857098540cc2a77e8a0093b907b799
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210838"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>IoT Hub에서 Azure IoT Central로 장치를 전송 하는 방법

*이 문서는 운영자 및 장치 개발자에 게 적용 됩니다.*  

이 문서에서는 IoT Hub에서 Azure IoT Central 응용 프로그램으로 장치를 전송 하는 방법을 설명 합니다. 

장치는 먼저 DPS 끝점에 연결 하 여 응용 프로그램에 연결 하는 데 필요한 정보를 검색 합니다. 내부적으로 IoT Central 응용 프로그램은 IoT hub를 사용 하 여 장치 연결을 처리 합니다.  

장치는 연결 문자열을 사용 하 여 직접 또는 DPS를 사용 하 여 IoT hub에 연결할 수 있습니다. [Azure IoT Hub 장치 프로 비전 서비스 (DPS)](../../iot-dps/about-iot-dps.md) 는 IoT Central에 대 한 경로입니다.

## <a name="to-move-the-device-to-azure-iot-central"></a>장치를 Azure IoT Central로 이동 하려면

장치를 IoT Hub에서 IoT Central에 연결 하려면 다음을 사용 하 여 장치를 업데이트 해야 합니다.

* IoT Central 응용 프로그램의 [범위 ID](../../iot-dps/concepts-service.md) 입니다.
* [그룹 SAS](concepts-get-connected.md) 키 또는 x.509 인증서에서 파생 된 키 [입니다.](../../iot-hub/iot-hub-x509ca-overview.md)

IoT Central 상호 작용 하려면 장치에서 구현 하는 속성/원격 분석/명령을 모델링 하는 장치 템플릿이 있어야 합니다. 자세한 내용은 [IoT Central에 연결 하](concepts-get-connected.md) 고 [장치 템플릿 이란?](concepts-device-templates.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

디바이스 개발자라면 다음과 같은 몇 가지 단계를 살펴보세요.

- [자습서: 클라이언트 응용 프로그램을 만들어 Azure IoT Central 응용 프로그램에 연결](tutorial-connect-device.md) 에서 SAS 토큰을 사용 하는 방법을 보여 주는 샘플 코드를 검토 합니다.
- [IoT Central 응용 프로그램용 Node.js 장치 SDK를 사용 하 여 x.509 인증서](how-to-connect-devices-x509.md) 를 사용 하 여 장치를 연결 하는 방법에 대해 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
- [Azure IoT Central 응용 프로그램에서 새 IoT 장치 유형을 정의](./howto-set-up-template.md) 하는 방법을 알아봅니다.
- [Azure IoT Edge 장치 및 Azure IoT Central](./concepts-iot-edge.md) 에 대해 읽기