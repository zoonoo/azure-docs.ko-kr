---
title: IoT 용 Defender 질문과 대답
description: IoT 기능 및 서비스에 대 한 Azure Defender에 대 한 질문과 대답을 찾습니다.
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
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939760"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>IoT 용 Azure Defender 질문과 대답

이 문서에서는 IoT 용 Defender에 대 한 질문과 대답 목록을 제공 합니다.

## <a name="does-azure-provide-support-for-iot-security"></a>Azure는 IoT 보안을 지원하나요?

Azure는 Azure Security Center를 통해 전체 보안 솔루션의 일부로 IoT 보안을 모니터링하고 관리하기 위한 통합된 보기를 제공합니다. 애플리케이션 개발자는 IoT Hub 보기를 사용하여 IoT 애플리케이션 보안을 관리할 수 있습니다.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>IoT 보안을 위한 Azure의 고유한 가치 제안이란 무엇인가요?

IoT 용 Defender를 통해 기업은 기존 사이버 보안 보기를 전체 IoT 솔루션으로 확장할 수 있습니다. Azure는 비즈니스 솔루션의 종단 간 보기를 제공하여 엔터프라이즈 보안 상태 및 수집된 데이터를 기준으로 비즈니스 관련 작업 및 의사 결정을 수행할 수 있도록 합니다. Azure IoT, Azure IoT Edge 및 Azure Security Center를 사용하여 결합된 보안을 통해 필요한 보안을 제공하는 원하는 솔루션을 만들 수 있습니다.

## <a name="who-is-defender-for-iot-made-for"></a>IoT에 대 한 Defender는 누구 인가요?

IoT 용 Defender는 Azure IoT Hub 보안 내에 통합 되며 일상적인 비즈니스 솔루션 보안 작업에 대 한 관리를 제공 합니다. IoT 용 Defender는 Azure Security Center 기능에 통합 되어 있으며, 전체 보안 솔루션의 일부로 IoT 보안을 모니터링 하 고 관리할 수 있는 통합 보기를 제공 합니다.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>IoT 용 Defender는 어떻게 경쟁사와 비교 하나요?

다른 솔루션은 고객이 자신의 솔루션을 만들 수 있도록 하는 일련의 기능을 제공 하지만 IoT 용 Defender는 관련 된 모든 Azure 리소스의 보안을 전체적으로 보여 주는 고유한 종단 간 IoT 보안 솔루션을 제공 합니다. Azure는 기존 디바이스 관리 도구와 쉽게 통합할 수 있도록 빠른 배포와 IoT Hub 모듈 쌍과의 완전한 통합을 지원합니다.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>이 서비스를 사용하려면 Azure Security Center 고객이 되어야 하나요?

아니요. 하지만 고객이면 좋습니다. Azure Security Center 하지 않으면, IoT 용 Defender는 제한 된 연결 리소스 데이터를 받고 잠재적 공격 노출, 위협 및 잠재적 공격에 대 한 제한 된 분석을 제공 합니다.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Azure IoT 고객이 되어야 하나요?

예. IoT 용 Defender는 Azure IoT 연결 및 인프라를 사용 합니다.

## <a name="do-i-have-to-install-an-agent"></a>에이전트를 설치해야 하나요?

Iot 용 Defender를 사용 하도록 설정 하기 위해 IoT 장치에 에이전트를 설치 하는 것은 필수가 아닙니다. 다음 세 가지 옵션 중에서 선택하여 선택 항목에 따라 다양한 수준의 보안 모니터링 및 관리 기능을 사용할 수 있습니다.

1. 수정 없이 IoT 보안 에이전트에 대 한 Defender를 설치 합니다. 이 옵션은 디바이스 동작 및 액세스에 대한 가장 높은 수준의 보안 인사이트를 제공합니다.

1. 사용자 고유의 에이전트를 만들고 IoT 보안 메시지 스키마 용 Defender를 구현 합니다. 이 옵션을 사용 하면 장치 보안 에이전트 위에 IoT 분석 도구에 대 한 Defender를 사용할 수 있습니다.

1. IoT 디바이스에 보안 에이전트가 설치되어 있지 않습니다. 이 옵션을 사용하면 보안 모니터링 및 관리 작업을 줄이면서 IoT Hub 통신을 모니터링할 수 있습니다.

## <a name="what-does-the-defender-for-iot-agent-do"></a>IoT 에이전트의 Defender는 어떻게 합니까?

IoT 용 Defender 에이전트는 장치 구성, 동작 및 액세스를 위한 장치 수준 위협 범위 (구성을 검색 하 여)를 제공 하 고 & 연결을 처리 합니다. IoT 용 Defender 보안 에이전트는 비즈니스 관련 데이터 또는 활동을 검색 하지 않습니다.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>IoT security agent에 대 한 Defender는 어디에서 얻을 수 있나요?

IoT 보안 에이전트의 Defender는 오픈 소스 이며 GitHub에서 32 비트 및 64 비트 Windows 및 Linux 버전으로 제공 https://github.com/Azure/Azure-IoT-Security 됩니다.

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>IoT 용 Defender 에이전트는 어디에 설치 되나요?

자세한 설치 및 에이전트 배포 정보는 GitHub https://github.com/Azure/Azure-IoT-Security 에서 찾을 수 있습니다.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>에이전트의 종속성 및 필수 조건은 무엇인가요?

IoT 용 Defender는 다양 한 플랫폼을 지원 합니다. 특정 디바이스에 대 한 지원을 확인하려면 [지원되는 디바이스 플랫폼](how-to-deploy-agent.md)을 참조하세요.

## <a name="which-data-is-collected-by-the-agent"></a>이 에이전트에서는 어떤 데이터를 수집하나요?

에이전트는 연결, 액세스, 방화벽 구성, 프로세스 목록 및 OS 기준을 수집합니다.

## <a name="how-much-data-will-the-agent-generate"></a>에이전트에서 생성하는 데이터의 양은 얼마나 되나요?

에이전트 데이터 생성은 디바이스, 애플리케이션, 연결 유형 및 고객 에이전트 구성에 따라 진행됩니다. 디바이스와 IoT 솔루션 간의 높은 가변성 때문에 먼저 랩 또는 테스트 설정에 에이전트를 배포하여 생성된 데이터의 양을 측정하면서 필요에 맞는 특정 구성을 관찰, 학습 및 설정하는 것이 좋습니다. 서비스를 시작한 후에는 IoT 용 Defender 에이전트에서 구성 및 사용자 지정 프로세스에 도움이 되는 에이전트 처리량을 최적화 하기 위한 운영 권장 사항을 제공 합니다.

## <a name="how-can-i-control-my-billing"></a>청구는 어떻게 제어할 수 있나요?

IoT 용 Defender는 구성 가능한 에이전트 검색, 데이터 버퍼 및 에이전트에서 생성 하는 데이터의 양을 늘리거나 줄이는 사용자 지정 경고를 만드는 기능을 제공 합니다.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>에이전트 메시지가 IoT Hub의 할당량을 사용하나요?

예. 에이전트 전송 데이터는 IoT Hub 할당량 계산에 포함됩니다.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>다음 단계 에이전트를 설치했으며 작업 또는 로그가 표시되지 않습니다.

1. [에이전트 유형이 디바이스의 지정된 OS 플랫폼에 맞는지](how-to-deploy-agent.md) 확인합니다.

1. [디바이스에서 에이전트가 실행되고 있는지](how-to-agent-configuration.md) 확인합니다.

1. IoT Hub에서 **보안**에 대해 [서비스를 사용하도록 설정했는지](quickstart-onboard-iot-hub.md) 확인합니다.

1. 장치가 [IoT 용 Defender 모듈을 사용 하 여 IoT Hub에서 구성](quickstart-create-security-twin.md)되었는지 확인 합니다.

활동 또는 로그를 아직 사용할 수 없는 경우 Defender에 대 한 자세한 내용은 IoT 파트너에 게 문의 하세요.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>인터넷 연결 작동이 중지되면 어떻게 되나요?

에이전트는 디바이스를 실행하는 동안 계속해서 데이터를 실행하고 저장합니다. 데이터는 크기 구성에 따라 보안 메시지 캐시에 저장됩니다. 디바이스가 다시 연결되면 보안 메시지 보내기가 다시 시작됩니다.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>디바이스를 다시 시작하는 경우 보안 에이전트가 자체 복구되나요?

보안 에이전트는 각 디바이스를 다시 시작하면 자동으로 다시 실행되도록 디자인되었습니다.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>에이전트가 디바이스 또는 기타 설치된 소프트웨어의 성능에 영향을 줄 수 있나요?

에이전트는 머신 리소스를 다른 애플리케이션/프로세스로 사용하며 일반적인 디바이스 작업을 중단해서는 안 됩니다. 에이전트가 실행되는 디바이스의 리소스 사용은 설정 및 구성에 따라 진행됩니다. 프로덕션 환경에서 배포를 시도하기 전에 다른 IoT 애플리케이션 및 기능과의 상호 운용성과 함께, 포함된 환경의 에이전트 구성을 테스트하는 것이 좋습니다.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>디바이스에서 유지 관리를 수행하고 있습니다. 에이전트를 끌 수 있나요?

에이전트를 끌 수 없습니다.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>에이전트가 제대로 작동하는지 테스트하는 방법이 있나요?

에이전트가 통신을 중지하거나 보안 메시지를 보내지 못하는 경우 **디바이스가 자동임** 경고가 생성됩니다.

## <a name="can-i-create-my-own-alerts"></a>경고를 직접 만들 수 있나요?

예. IP 주소, 열린 포트 등의 미리 결정된 동작 세트에 대해 사용자 지정 경고를 설정할 수 있습니다. 사용자 지정 경고에 대한 자세한 내용과 사용자 지정 경고를 만드는 방법에 대한 자세한 내용은 [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md)를 참조하세요.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>로그는 어디에서 볼 수 있나요? 로그를 사용자 지정할 수 있나요?

- 연결된 Log Analytics 작업 영역을 사용하여 경고 및 권장 사항을 확인합니다. 작업 영역에서 스토리지 크기와 기간을 구성합니다.

- 보안 에이전트의 원시 데이터를 Log Analytics 계정에 저장할 수도 있습니다. 이 옵션의 구성을 변경하기 전에 크기, 기간, 스토리지 요구 사항 및 관련 비용을 고려하세요.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>IoT 용 Defender를 모듈 id에 추가 해야 하는 이유는 무엇 인가요? 용도는?

IoT 용 Defender 모듈은 에이전트 구성 및 관리에 사용 됩니다.

## <a name="next-steps"></a>다음 단계

IoT 용 Defender를 시작 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- IoT 용 Defender [개요](overview.md) 읽기
- [서비스 필수 조건](service-prerequisites.md) 확인
- [시작](getting-started.md) 방법에 대한 자세한 정보
- [IoT 보안 경고에 대 한 Defender](concept-security-alerts.md) 이해
