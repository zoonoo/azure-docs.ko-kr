---
title: IoT 용 Defender 질문과 대답
description: IoT 기능 및 서비스에 대 한 Azure Defender에 대 한 질문과 대답을 찾습니다.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778596"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>IoT 용 Azure Defender 질문과 대답

이 문서에서는 IoT 용 Defender에 대 한 질문과 대답 목록을 제공 합니다.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>IoT 보안을 위한 Azure의 고유한 가치 제안이란 무엇인가요?

IoT 용 Defender를 통해 기업은 기존 사이버 보안 보기를 전체 IoT 솔루션으로 확장할 수 있습니다. Azure는 비즈니스 솔루션의 종단 간 보기를 제공하여 엔터프라이즈 보안 상태 및 수집된 데이터를 기준으로 비즈니스 관련 작업 및 의사 결정을 수행할 수 있도록 합니다. Azure IoT, Azure IoT Edge 및 Azure Security Center를 사용하여 결합된 보안을 통해 필요한 보안을 제공하는 원하는 솔루션을 만들 수 있습니다.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>조직에서는 독점적인 비표준 산업용 프로토콜을 사용 합니다. 지원되나요? 

IoT 용 Azure Defender는 포괄적인 프로토콜 지원을 제공 합니다. 임베디드 프로토콜 지원과 더불어 독점 및 사용자 지정 프로토콜이 실행 되는 IoT 및 OT 장치와 모든 표준에서 벗어난 프로토콜을 보호할 수 있습니다. 개발자는 수평 ODE (Open Development Environment) SDK를 사용 하 여 정의 된 프로토콜에 따라 네트워크 트래픽을 디코딩하는 dissector 플러그 인을 만들 수 있습니다. 서비스에서 트래픽을 분석 하 여 전체 모니터링, 경고 및 보고 기능을 제공 합니다. 마감일 사용:
- 새 버전으로 업그레이드할 필요 없이 표시 유형 및 제어를 확장 합니다.
- 외부 플러그 인으로 사이트를 개발 하 여 독점 정보를 보호 합니다. 
- 경고, 이벤트 및 프로토콜 매개 변수에 대 한 텍스트를 지역화 합니다.

프로토콜을 플러그 인으로 개발 하기 위한 고유한 솔루션에는 새 프로토콜을 지원 하기 위해 전담 개발자 팀 또는 버전 릴리스가 필요 하지 않습니다. 개발자, 파트너 및 고객은 안전 하 게 프로토콜을 개발 하 고 수평을 사용 하 여 정보 및 지식을 공유할 수 있습니다. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Microsoft 파트너에서 하드웨어 어플라이언스를 구입 해야 하나요?
IoT 센서 용 Azure Defender는 [하드웨어 사양 가이드](./how-to-identify-required-appliances.md)에 설명 된 대로 특정 하드웨어 사양에서 실행 되며, 고객이 Microsoft 파트너 로부터 인증 된 하드웨어를 구입 하거나 제공 된 자료 (bom)를 사용 하 여 자체적으로 구매할 수 있습니다. 

인증 된 하드웨어는 드라이버 안정성, 패킷 삭제 및 네트워크 크기 조정에 대해 랩에서 테스트 되었습니다.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>규정은 시스템을 인터넷에 연결 하는 것을 허용 하지 않습니다. IoT 용 Defender를 계속 사용할 수 있나요?

예, 하실 수 있습니다. IoT 용 Azure Defender 플랫폼 온-프레미스 솔루션은 네트워크 트래픽 (범위, RSPAN 또는 탭을 통해)을 소극적으로 수집 하 여, OT 및 IoT 네트워크를 분석, 검색 및 지속적으로 모니터링 하는 물리적 또는 가상 센서 어플라이언스로 배포 됩니다. 대기업의 경우 여러 센서가 데이터를 온-프레미스 관리 콘솔로 집계할 수 있습니다.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>네트워크에서 모니터링 포트를 연결 해야 하는 위치는 어디 인가요?

IoT 용 Azure Defender 센서는 범위 포트 또는 네트워크 탭에 연결 하 고, 수동 (에이전트 없는) 모니터링을 통해 ICS 네트워크 트래픽 수집을 즉시 시작 합니다. 데이터 경로에 배치 되지 않고 장치를 적극적으로 검색 하지 않으므로 OT 네트워크에 대 한 영향은 0입니다.

예를 들면 다음과 같습니다.
- 단일 어플라이언스 (가상의 가상)는 상점 바닥 DMZ 계층에 있을 수 있으며 모든 매장 바닥 셀 트래픽이이 계층으로 라우팅됩니다.
- 또는 상점 바닥 DMZ 계층에 상주할 클라우드 또는 로컬 관리를 사용 하 여 각 매장 바닥 셀에서 작은 미니 센서를 찾습니다. 다른 어플라이언스 (가상 또는 실제)는 SCADA, Historian 또는 MES에 대 한 매장 바닥 DMZ 계층의 트래픽을 모니터링할 수 있습니다.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>IoT 용 Defender는 어떻게 경쟁사와 비교 하나요?

IoT 용 Azure Defender는 모든 IoT/OT 장치에서 포괄적인 보안을 제공 합니다. **최종 사용자 조직의** 경우 azure Defender for IoT는 에이전트 없는, 신속 하 게 배포 되는 네트워크 계층 보안을 제공 하 고 다양 한 소유 장비 및 레거시 Windows 시스템에서 작동 하며, Azure 센티널 및 기타 SOC 도구와 상호 운용 합니다. 온-프레미스 또는 Azure 연결 환경에 배포할 수 있습니다. **Iot 장치 빌더가** iot 용 Azure Defender는 새 IOT/OT 이니셔티브에 장치 계층 보안을 포함 하는 간단한 에이전트를 제공 합니다.

## <a name="do-i-have-to-be-an-azure-customer"></a>Azure 고객이 되어야 하나요?

아니요, 에이전트 없는 IoT 용 Azure Defender 버전의 경우 Azure 고객이 될 필요는 없습니다. 그러나 Azure 센티널에 경고를 전송 하려는 경우 네트워크 센서를 프로 비전 하 고 클라우드에서 상태를 모니터링 합니다. 그리고 자동 소프트웨어 및 위협 인텔리전스 업데이트를 활용 하는 경우 Azure IoT Hub를 통해 센서를 Azure에 연결 해야 합니다.

Azure Defender for IoT의 에이전트 기반 버전의 경우 Azure 고객 이어야 합니다.

## <a name="can-i-create-my-own-alerts"></a>경고를 직접 만들 수 있나요?

예, IP/MAC 주소, 프로토콜 유형, 클래스, 서비스, 함수, 명령 등을 비롯 한 여러 매개 변수를 기반으로 하는 사용자 지정 경고를 만들 수 있으며 페이로드에 포함 된 사용자 지정 태그 값도 만들 수 있습니다.  사용자 지정 경고 만들기 및 사용자 지정 경고를 만드는 방법에 대해 자세히 알아보려면 [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md) 를 참조 하세요.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>인터넷 연결 작동이 중지되면 어떻게 되나요?

센서와 에이전트는 장치가 실행 되는 동안 계속 실행 되 고 데이터를 저장 합니다. 데이터는 크기 구성에 따라 보안 메시지 캐시에 저장됩니다. 디바이스가 다시 연결되면 보안 메시지 보내기가 다시 시작됩니다.

## <a name="next-steps"></a>다음 단계

IoT 용 Defender를 시작 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- IoT 용 Defender [개요](overview.md) 읽기
- [시스템 필수 구성 요소](quickstart-system-prerequisites.md) 확인
- [IoT 용 Defender를 시작](getting-started.md) 하는 방법에 대 한 자세한 정보
- [IoT 보안 경고에 대 한 Defender](concept-security-alerts.md) 이해