---
title: IoT 에이전트에 대 한 Azure Defender 질문과 대답
description: IoT 에이전트의 Azure Defender에 대 한 질문과 대답을 찾습니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 9862519a2003eb373c43fef1b660986a8d830327
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094455"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>IoT 에이전트에 대 한 Azure Defender 질문과 대답

이 문서에서는 IoT 에이전트의 Defender에 대 한 질문과 대답 목록을 제공 합니다.


## <a name="do-i-have-to-install-an-embedded-security-agent"></a>포함 된 보안 에이전트를 설치 해야 하나요?

Iot 용 Defender를 사용 하도록 설정 하기 위해 IoT 장치에 에이전트를 설치 하는 것은 필수가 아닙니다. 다음 세 가지 옵션 중에서 선택하여 선택 항목에 따라 다양한 수준의 보안 모니터링 및 관리 기능을 사용할 수 있습니다.

- NTA (네트워크 트래픽 분석) 센서를 사용 하 여 모니터링 하 고, 네트워크 및 장치에서 성능에 영향을 주지 않는 IoT/OT 위험을 모니터링 하 고 심층 가시성을 제공 하는 수동, 비 침입 (에이전트 없는) 배포
- 수정 하거나 수정 하지 않고 IoT embedded 보안 에이전트에 대 한 Defender를 설치 합니다. 이 옵션은 디바이스 동작 및 액세스에 대한 가장 높은 수준의 보안 인사이트를 제공합니다.

- 사용자 고유의 에이전트를 만들고 IoT 보안 메시지 스키마 용 Defender를 구현 합니다. 이 옵션을 사용 하면 장치 보안 에이전트 위에 IoT 분석 도구에 대 한 Defender를 사용할 수 있습니다.

- IoT 디바이스에 보안 에이전트가 설치되어 있지 않습니다. 이 옵션을 사용하면 보안 모니터링 및 관리 작업을 줄이면서 IoT Hub 통신을 모니터링할 수 있습니다.

## <a name="what-does-the-defender-for-iot-agent-do"></a>IoT 에이전트의 Defender는 어떻게 합니까?

IoT 용 Defender 에이전트는 장치 구성, 동작 및 액세스를 위한 장치 수준 위협 범위 (구성을 검색 하 여)를 제공 하 고 & 연결을 처리 합니다. IoT 용 Defender 보안 에이전트는 비즈니스 관련 데이터 또는 활동을 검색 하지 않습니다.

IoT 보안 에이전트의 Defender는 오픈 소스 이며 GitHub에서 32 비트 및 64 비트 Windows 및 Linux 버전으로 제공 https://github.com/Azure/Azure-IoT-Security 됩니다.


## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>에이전트의 종속성 및 필수 조건은 무엇인가요?

IoT 용 Defender는 다양 한 플랫폼을 지원 합니다. 특정 디바이스에 대 한 지원을 확인하려면 [지원되는 디바이스 플랫폼](how-to-deploy-agent.md)을 참조하세요.

## <a name="which-data-is-collected-by-the-agent"></a>이 에이전트에서는 어떤 데이터를 수집하나요?

에이전트는 연결, 액세스, 방화벽 구성, 프로세스 목록 및 OS 기준을 수집합니다.

## <a name="how-much-data-will-the-agent-generate"></a>에이전트에서 생성하는 데이터의 양은 얼마나 되나요?

에이전트 데이터 생성은 디바이스, 애플리케이션, 연결 유형 및 고객 에이전트 구성에 따라 진행됩니다. 디바이스와 IoT 솔루션 간의 높은 가변성 때문에 먼저 랩 또는 테스트 설정에 에이전트를 배포하여 생성된 데이터의 양을 측정하면서 필요에 맞는 특정 구성을 관찰, 학습 및 설정하는 것이 좋습니다. 서비스를 시작한 후에는 IoT 용 Defender 에이전트에서 구성 및 사용자 지정 프로세스에 도움이 되는 에이전트 처리량을 최적화 하기 위한 운영 권장 사항을 제공 합니다.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>에이전트 메시지가 IoT Hub의 할당량을 사용하나요?

예. 에이전트 전송 데이터는 IoT Hub 할당량 계산에 포함됩니다.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>다음 단계 에이전트를 설치했으며 작업 또는 로그가 표시되지 않습니다.

1. [에이전트 유형이 디바이스의 지정된 OS 플랫폼에 맞는지](how-to-deploy-agent.md) 확인합니다.

1. [디바이스에서 에이전트가 실행되고 있는지](how-to-agent-configuration.md) 확인합니다.

1. IoT Hub에서 **보안**에 대해 [서비스를 사용하도록 설정했는지](quickstart-onboard-iot-hub.md) 확인합니다.

1. 장치가 [IoT 용 Defender 모듈을 사용 하 여 IoT Hub에서 구성](quickstart-create-security-twin.md)되었는지 확인 합니다.

활동 또는 로그를 아직 사용할 수 없는 경우 Defender에 대 한 자세한 내용은 IoT 파트너에 게 문의 하세요.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>인터넷 연결 작동이 중지되면 어떻게 되나요?

센서와 에이전트는 장치가 실행 되는 동안 계속 실행 되 고 데이터를 저장 합니다. 데이터는 크기 구성에 따라 보안 메시지 캐시에 저장됩니다. 디바이스가 다시 연결되면 보안 메시지 보내기가 다시 시작됩니다.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>에이전트가 디바이스 또는 기타 설치된 소프트웨어의 성능에 영향을 줄 수 있나요?

에이전트는 머신 리소스를 다른 애플리케이션/프로세스로 사용하며 일반적인 디바이스 작업을 중단해서는 안 됩니다. 에이전트가 실행되는 디바이스의 리소스 사용은 설정 및 구성에 따라 진행됩니다. 프로덕션 환경에서 배포를 시도하기 전에 다른 IoT 애플리케이션 및 기능과의 상호 운용성과 함께, 포함된 환경의 에이전트 구성을 테스트하는 것이 좋습니다.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>디바이스에서 유지 관리를 수행하고 있습니다. 에이전트를 끌 수 있나요?

에이전트를 끌 수 없습니다.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>에이전트가 제대로 작동하는지 테스트하는 방법이 있나요?

에이전트가 통신을 중지하거나 보안 메시지를 보내지 못하는 경우 **디바이스가 자동임** 경고가 생성됩니다.



## <a name="next-steps"></a>다음 단계

IoT 용 Defender를 시작 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- IoT 용 Defender [개요](overview.md) 읽기
- [서비스 필수 조건](service-prerequisites.md) 확인
- [시작](getting-started.md) 방법에 대한 자세한 정보
- [IoT 보안 경고에 대 한 Defender](concept-security-alerts.md) 이해
