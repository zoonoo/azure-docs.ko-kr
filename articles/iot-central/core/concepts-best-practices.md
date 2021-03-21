---
title: Azure IoT Central의 장치 개발 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 장치 연결에 대 한 모범 사례를 설명 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054707"
---
# <a name="best-practices-for-device-development"></a>장치 개발을 위한 모범 사례

*이 문서는 디바이스 개발자에게 적용됩니다.*

이러한 권장 사항은 기본 제공 재해 복구 및 자동 크기 조정 기능을 활용 하는 IoT Central 장치를 구현 하는 방법을 보여 줍니다.

다음 목록에서는 장치가 IoT Central에 연결 하는 경우의 상위 수준 흐름을 보여 줍니다.

1. DPS를 사용 하 여 장치를 프로 비전 하 고 장치 연결 문자열을 가져옵니다.

1. 연결 문자열을 사용 하 여 IoT Central의 내부 IoT Hub 끝점에 연결 합니다. IoT Central 응용 프로그램에서 데이터를 보내고 받을 수 있습니다.

1. 장치에서 연결 오류가 발생 하는 경우 오류 유형에 따라 연결을 다시 시도 하거나 장치를 다시 구축.

## <a name="use-dps-to-provision-the-device"></a>DPS를 사용 하 여 장치 프로 비전

DPS를 사용 하 여 장치를 프로 비전 하려면 IoT Central 응용 프로그램의 범위 ID, 자격 증명 및 장치 ID를 사용 합니다. 자격 증명 유형에 대 한 자세한 내용은 [x.509 그룹 등록](concepts-get-connected.md#x509-group-enrollment) 및 [SAS 그룹 등록](concepts-get-connected.md#sas-group-enrollment)을 참조 하세요. 장치 Id에 대해 자세히 알아보려면 [장치 등록](concepts-get-connected.md#device-registration)을 참조 하세요.

성공 하면 DPS는 장치에서 IoT Central 응용 프로그램에 연결 하는 데 사용할 수 있는 연결 문자열을 반환 합니다. 프로 비전 오류를 해결 하려면 [장치의 프로 비전 상태 확인](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)을 참조 하세요.

장치는 나중에 연결 하는 데 사용할 연결 문자열을 캐시할 수 있습니다. 그러나 장치는 [연결 실패를 처리할](#handle-connection-failures)수 있도록 준비 해야 합니다.

## <a name="connect-to-iot-central"></a>IoT Central에 연결

연결 문자열을 사용 하 여 IoT Central의 내부 IoT Hub 끝점에 연결 합니다. 연결을 사용 하 여 IoT Central 응용 프로그램에 원격 분석을 보내고, IoT Central 응용 프로그램과 속성 값을 동기화 하 고, IoT Central 응용 프로그램에서 보낸 명령에 응답할 수 있습니다.

## <a name="handle-connection-failures"></a>연결 실패 처리

크기 조정 또는 재해 복구를 위해 IoT Central 기본 IoT hub를 업데이트할 수 있습니다. 연결을 유지 하기 위해 장치 코드는 새 IoT Hub 끝점에 대 한 연결을 설정 하 여 특정 연결 오류를 처리 해야 합니다.

장치에서 연결할 때 다음 오류가 발생 하면 DPS로 프로 비전 단계를 다시 실행 하 여 새 연결 문자열을 가져옵니다. 이러한 오류는 장치가 사용 하는 연결 문자열이 더 이상 유효 하지 않음을 의미 합니다.

- IoT Hub 끝점에 연결할 수 없습니다.
- 만료 된 보안 토큰입니다.
- IoT Hub에서 장치를 사용할 수 없습니다.

장치에서 연결할 때 다음 오류가 발생 하는 경우 백오프 전략을 사용 하 여 연결을 다시 시도해 야 합니다. 이러한 오류는 장치가 사용 하는 연결 문자열이 여전히 유효 하지만 일시적인 조건에서 장치를 연결 하는 것을 중지 하는 것을 의미 합니다.

- 운영자 차단 장치.
- 서비스의 내부 오류 500입니다.

장치 오류 코드에 대해 자세히 알아보려면 [장치 연결 문제 해결](troubleshoot-connection.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

디바이스 개발자라면 다음과 같은 몇 가지 단계를 살펴보세요.

- [자습서: 클라이언트 응용 프로그램을 만들어 Azure IoT Central 응용 프로그램에 연결](tutorial-connect-device.md) 에서 SAS 토큰을 사용 하는 방법을 보여 주는 샘플 코드를 검토 합니다.
- [IoT Central 응용 프로그램용 Node.js 장치 SDK를 사용 하 여 x.509 인증서](how-to-connect-devices-x509.md) 를 사용 하 여 장치를 연결 하는 방법에 대해 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
- [Azure IoT Central 응용 프로그램에서 새 IoT 장치 유형을 정의](./howto-set-up-template.md) 하는 방법을 알아봅니다.
- [Azure IoT Edge 장치 및 Azure IoT Central](./concepts-iot-edge.md) 에 대해 읽기
