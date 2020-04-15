---
title: 보안 에이전트 개요
description: IoT 디바이스에서 IoT 보안 서비스 에이전트를 위한 Azure 보안 센터를 이해, 구성, 배포 및 사용하는 것을 시작하십시오.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310642"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>IoT 장치 보안 에이전트를 위한 Azure 보안 센터 시작

IoT 보안 에이전트용 Azure 보안 센터는 원격 연결, 활성 응용 프로그램, 로그인 이벤트 및 운영 체제 구성 모범 사례 모니터링과 같은 향상된 보안 기능을 제공합니다. 단일 서비스로 장치 필드 위협 보호 및 보안 상태를 제어할 수 있습니다.

C# 및 C 모두에서 Linux 및 Windows 보안 에이전트에 대한 참조 아키텍처가 제공됩니다.

IoT 보안 에이전트용 Azure 보안 센터는 장치 운영 체제의 원시 이벤트 수집, 비용을 줄이기 위한 이벤트 집계 및 장치 모듈 쌍집합을 통한 구성을 처리합니다. 보안 메시지는 IoT Hub를 통해 IoT 분석 서비스를 위한 Azure 보안 센터로 전송됩니다.

다음 워크플로를 사용하여 IoT 보안 에이전트용 Azure 보안 센터를 배포하고 테스트합니다.

1. [IoT 허브에 IoT 서비스를 위한 Azure 보안 센터 사용](quickstart-onboard-iot-hub.md)
1. IoT Hub에 등록된 장치가 없는 경우 [새 장치를 등록합니다.](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)
1. 장치에 대한 [azureiot보안 보안 모듈을 만듭니다.](quickstart-create-security-twin.md)
1. 실제 장치에 설치하는 대신 Azure 시뮬레이션 된 장치에 에이전트를 설치하려면 사용 가능한 영역에서 [새 Azure 가상 시스템(VM)을 스핀업합니다.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)
1. IoT 장치 또는 새 VM에 [IoT 보안 에이전트용 Azure 보안 센터를 배포합니다.](how-to-deploy-linux-cs.md)
1. [trigger_events](https://aka.ms/iot-security-github-trigger-events) 대한 지침을 따라 공격의 무해한 시뮬레이션을 실행합니다.
1. 이전 단계에서 시뮬레이션된 공격에 대한 응답으로 IoT에 대한 Azure 보안 센터 경고를 확인합니다. 스크립트를 실행한 후 5분 후에 확인을 시작합니다.
1. IoT Hub를 [사용하여 로그 분석을 사용하여](how-to-security-data-access.md) [경고,](concept-security-alerts.md)권장 [사항](concept-recommendations.md)및 심층 분석을 탐색합니다.

## <a name="next-steps"></a>다음 단계

- [솔루션](quickstart-configure-your-solution.md) 구성
- [보안 모듈 만들기](quickstart-create-security-twin.md)
- [사용자 지정 경고](quickstart-create-custom-alerts.md) 구성
- [보안 에이전트 배포](how-to-deploy-agent.md)
