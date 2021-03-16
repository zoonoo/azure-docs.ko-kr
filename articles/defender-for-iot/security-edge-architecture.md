---
title: Defender for IoT Defender-IoT-마이크로-에이전트 IoT Edge
description: Azure Defender for IoT Defender-IoT-마이크로 IoT Edge 에이전트의 아키텍처 및 기능을 이해 합니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 4b7377f74e24660945555c112fed4d77d2c5ddcc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495949"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>IoT Edge 용 Azure Defender Defender-마이크로 에이전트

[Azure IoT Edge](../iot-edge/index.yml) 는에 지에서 비즈니스 워크플로를 관리 하 고 수행 하기 위한 강력한 기능을 제공 합니다.
IoT 환경에서 IoT Edge를 재생 하는 주요 부분은 악의적인 행위자에 게 특히 유용 합니다.

Defender for IoT Defender-마이크로 에이전트는 IoT Edge 장치에 대 한 포괄적인 보안 솔루션을 제공 합니다.
IoT 용 Defender 모듈은 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 수집 하 고 집계 하 고 분석 하 여 실행 가능한 보안 권장 사항 및 경고로 변환 합니다.

Iot 장치에 대 한 IoT 보안 에이전트의 Defender와 마찬가지로, IoT Edge 모듈은 모듈 쌍을 통해 항상 사용자 지정이 가능 합니다.
자세히 알아보려면 [에이전트 구성](how-to-agent-configuration.md) 을 참조 하세요.

Defender for IoT Defender-IoT-마이크로 에이전트 IoT Edge는 다음과 같은 기능을 제공 합니다.

- 기본 운영 체제 (Linux) 및 IoT Edge 컨테이너 시스템에서 원시 보안 이벤트를 수집 합니다.

  사용 가능한 보안 데이터 수집기에 대 한 자세한 내용은 [IoT 에이전트 구성 용 Defender](how-to-agent-configuration.md) 를 참조 하세요.

- IoT Edge 배포 매니페스트를 분석 합니다.

- [IoT Edge 허브](../iot-edge/iot-edge-runtime.md#iot-edge-hub)를 통해 전송 되는 메시지에 원시 보안 이벤트를 집계 합니다.

- Defender-IoT-마이크로 에이전트 쌍을 사용 하 여 구성을 제거 합니다.

  자세히 알아보려면 [IoT 에이전트에 대 한 Defender 구성](how-to-agent-configuration.md) 을 참조 하세요.

Defender for IoT Defender-IoT-마이크로 에이전트 IoT Edge은 IoT Edge의 특권 모드에서 실행 됩니다.
모듈에서 운영 체제 및 기타 IoT Edge 모듈을 모니터링할 수 있도록 하려면 특권 모드가 필요 합니다.

## <a name="module-supported-platforms"></a>모듈 지원 플랫폼

Defender for IoT Defender-IoT-마이크로 에이전트 IoT Edge는 현재 Linux 에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Edge에 대 한 IoT Defender-IoT-마이크로 에이전트의 아키텍처 및 기능에 대해 알아보았습니다.

IoT 배포용 Defender를 계속 시작 하려면 다음 문서를 사용 합니다.

- [IoT Edge에 대 한 Defender-IoT-마이크로 에이전트](how-to-deploy-edge.md) 배포
- [Defender-마이크로 에이전트를 구성](how-to-agent-configuration.md) 하는 방법 알아보기
- Iot 용 IoT [defender](resources-manage-proprietary-protocols.md) 용 defender를 검토 합니다.
- [IoT Hub에서 IoT 서비스용 Defender를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법에 대해 알아봅니다.
- [IoT 용 DEFENDER FAQ](resources-frequently-asked-questions.md) 에서 서비스에 대 한 자세한 정보