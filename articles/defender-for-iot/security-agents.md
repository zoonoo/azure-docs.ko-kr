---
title: 보안 에이전트 개요
description: IoT 장치에서 IoT 보안 서비스 에이전트에 대 한 Azure Defender를 이해 하 고, 구성 하 고, 배포 하 고, 사용 하는 방법을 시작 하세요.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939748"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>IoT 장치 보안 에이전트로 Azure Defender 시작

IoT 용 Defender 보안 에이전트는 원격 연결 모니터링, 활성 응용 프로그램, 로그인 이벤트 및 운영 체제 구성 모범 사례와 같은 향상 된 보안 기능을 제공 합니다. 단일 서비스에서 장치 필드 위협 방지 및 보안 상태를 제어 합니다.

Linux 및 Windows 보안 에이전트에 대 한 참조 아키텍처는 c # 및 C에서 제공 됩니다.

IoT 용 Defender 보안 에이전트는 장치 운영 체제에서 원시 이벤트 수집을 처리 하 고, 비용을 절감 하 고, 장치 모듈 쌍을 통해 구성을 처리 합니다. 보안 메시지는 IoT Hub를 통해 IoT 분석 서비스용 Defender에 전송 됩니다.

다음 워크플로를 사용 하 여 IoT 보안 에이전트에 대 한 Defender를 배포 하 고 테스트 합니다.

1. [IoT Hub에 대 한 IoT 서비스용 Defender 사용](quickstart-onboard-iot-hub.md)
1. IoT Hub에 등록 된 장치가 없는 경우 [새 장치를 등록](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)합니다.
1. 장치에 대해 [azureiotsecurity 보안 모듈을 만듭니다](quickstart-create-security-twin.md) .
1. 실제 장치에를 설치 하는 대신 Azure 시뮬레이트된 장치에 에이전트를 설치 하려면 사용 가능한 영역에서 [새 AZURE VM (가상 머신)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 을 실행 합니다.
1. Iot 장치 또는 새 VM에 [iot 보안 에이전트 용 Defender를 배포](how-to-deploy-linux-cs.md) 합니다.
1. [Trigger_events](https://aka.ms/iot-security-github-trigger-events) 에 대 한 지침에 따라 공격에 대 한 무해 한 시뮬레이션을 실행 합니다.
1. 이전 단계에서 시뮬레이션 된 공격에 대 한 응답으로 Defender의 IoT 경고를 확인 합니다. 스크립트를 실행 한 후 5 분 후에 확인을 시작 합니다.
1. IoT Hub를 사용 하 여 [Log Analytics를 사용 하 여](how-to-security-data-access.md) [경고](concept-security-alerts.md), [권장 사항](concept-recommendations.md)및 심층 조사를 살펴보세요.

## <a name="next-steps"></a>다음 단계

- [솔루션](quickstart-configure-your-solution.md) 구성
- [보안 모듈 만들기](quickstart-create-security-twin.md)
- [사용자 지정 경고](quickstart-create-custom-alerts.md) 구성
- [보안 에이전트 배포](how-to-deploy-agent.md)
