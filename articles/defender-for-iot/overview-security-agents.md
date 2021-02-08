---
title: 보안 에이전트
description: IoT 장치에서 IoT 보안 서비스 에이전트에 대 한 Azure Defender를 이해 하 고, 구성 하 고, 배포 하 고, 사용 하는 작업을 시작 하세요.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: fcc2c7c3e724fccd005c8b6c8d1b01982f407c65
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809030"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>IoT 용 Azure Defender 장치 마이크로 에이전트 시작

IoT 용 Defender 보안 에이전트는 운영 체제 구성 모범 사례 모니터링과 같은 향상 된 보안 기능을 제공 합니다. 단일 서비스에서 장치 필드 위협 방지 및 보안 상태를 제어 합니다.

IoT 용 방어자 보안 에이전트는 장치 운영 체제에서 원시 이벤트 수집을 처리 하 고, 비용을 절감 하 고, 장치 모듈 쌍을 통해 구성을 처리 합니다. 보안 메시지는 IoT Hub를 통해 IoT 분석 서비스용 Defender에 전송 됩니다.

다음 워크플로를 사용 하 여 IoT 보안 에이전트에 대 한 Defender를 배포 하 고 테스트 합니다.

1. [IoT Hub에 대 한 IoT 서비스용 Defender를 사용 하도록 설정](quickstart-onboard-iot-hub.md)합니다.

1. IoT Hub에 등록 된 장치가 없는 경우 [새 장치를 등록](../iot-accelerators/iot-accelerators-device-simulation-overview.md)합니다.

1. 장치에 대해 [Defendde\마이크로 에이전트 모듈](quickstart-create-micro-agent-module-twin.md) 쌍을 만듭니다.

1. 실제 장치에를 설치 하는 대신 Azure 시뮬레이트된 장치에 에이전트를 설치 하려면 사용 가능한 영역에서 [새 AZURE VM (가상 머신)](../virtual-machines/linux/quick-create-portal.md) 을 실행 합니다.

1. Iot 장치 또는 새 VM에 [iot 보안 에이전트 용 Defender를 배포](how-to-deploy-linux-cs.md) 합니다.

1. [Trigger_events](https://aka.ms/iot-security-github-trigger-events) 에 대 한 지침에 따라 OS 기준 이벤트를 실행 합니다.

1. 이전 단계에서 시뮬레이션 된 OS 기준 검사 실패에 대 한 응답으로 Defender for IoT 권장 사항을 확인 합니다. 스크립트를 실행 한 후 30 분 이내에 확인을 시작 합니다.

## <a name="next-steps"></a>다음 단계

- [솔루션](quickstart-configure-your-solution.md) 구성
- [보안 모듈 만들기](quickstart-create-security-twin.md)
- [사용자 지정 경고](quickstart-create-custom-alerts.md) 구성
- [보안 에이전트 배포](how-to-deploy-agent.md)