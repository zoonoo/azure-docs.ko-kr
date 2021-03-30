---
title: 서비스 개요
description: Defender for IoT 기능 및 서비스에 대해 자세히 알아보고, Defender for IoT가 종합적인 IoT 보안을 제공하는 방식을 이해합니다.
ms.topic: overview
ms.date: 12/09/2020
ms.openlocfilehash: 15772ee21587ed2bc010e31174af6daac71dfc12
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786841"
---
# <a name="welcome-to-azure-defender-for-iot"></a>Azure Defender for IoT 시작

OT(운영 기술) 네트워크는 우리 사회의 가장 중요한 여러 요소를 움직이게 합니다. 그러나 이러한 기술 중 상당수는 설계 당시 보안을 염두에 두지 않았으며 기존의 IT 보안 컨트롤로 보호할 수 없습니다. 한편 IoT(사물 인터넷)는 수십억 개의 연결된 디바이스를 통해 새로운 혁신의 물결을 일으키면서 공격 노출 영역과 위험을 증가시키고 있습니다.  

Azure Defender for IoT는 IoT/OT 디바이스, 취약성 및 위협을 식별하는 통합 보안 솔루션입니다. 기존 IoT/OT 디바이스를 보호해야 하든 아니면 새로운 IoT 혁신의 보안을 구축해야 하든, 이 솔루션은 전체 IoT/OT 환경을 보호합니다.  

Azure Defender for IoT는 고객 환경의 요구 사항에 맞는 두 가지 기능 세트를 제공합니다.

IoT/OT 환경을 사용하는 최종 사용자 조직의 경우 Azure Defender for IoT는 다음과 같은 특징의 에이전트 없는 네트워크 계층 모니터링을 제공합니다.

- 신속하게 배포할 수 있습니다.
- 다양한 산업용 장비 및 SOC 도구와 쉽게 통합됩니다.
- IoT/OT 네트워크 성능 또는 안정성에 영향을 주지 않습니다. 

플랫폼 전체를 온-프레미스에 배포할 수도 있고 Azure에 연결된 하이브리드 환경에 배포할 수도 있습니다.  

IoT 디바이스 빌더의 경우 Azure Defender for IoT는 Linux 및 RTOS 같은 표준 IoT 운영 체제를 지원하는 경량 마이크로 에이전트도 제공합니다. 이 경량 에이전트는 Edge부터 클라우드까지 IoT/OT 이니셔티브에 보안을 기본 탑재하도록 도와줍니다. 유연하고 사용자 지정 가능한 배포를 위한 소스 코드를 제공합니다. 

## <a name="agentless-solution-for-organizations"></a>조직을 위한 에이전트 없는 솔루션 

이전 IoT 및 OT 디바이스는 에이전트를 지원하지 않으며 종종 패치가 적용되지 않고, 잘못 구성되고, IT 팀에게 보이지 않았습니다. 이러한 품질 때문에 회사 네트워크 깊숙이 침입하려는 위협 행위자의 손쉬운 목표물이 됩니다. 

회사 IT 네트워크용으로 개발된 기존 네트워크 보안 모니터링 도구는 IoT 및 OT 환경에서 발견되는 특수 프로토콜, 디바이스 및 M2M(컴퓨터 간) 동작을 깊이 이해하지 못하기 때문에 이러한 환경을 해결할 수 없습니다. 

Azure Defender for IoT의 에이전트 없는 모니터링 기능은 이러한 네트워크에 대한 가시성과 보안을 제공합니다. 따라서 이러한 환경의 주요 문제를 해결할 수 있습니다. 

### <a name="automatic-device-discovery"></a>자동 디바이스 검색  

에이전트 없는 수동 네트워크 모니터링을 사용하면 IoT/OT 네트워크에 아무 영향도 미치지 않고 모든 IoT/OT 디바이스, 이러한 디바이스의 세부 정보 및 통신 방법에 대한 완전한 인벤토리를 얻을 수 있습니다.  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>위험 및 취약성에 대한 선제적 가시성
 
IoT/OT 환경의 위험과 취약성을 식별합니다. 예를 들어 패치가 적용되지 않은 디바이스, 열려 있는 포트, 권한 없는 애플리케이션 및 무단 연결을 식별합니다. 디바이스 구성, PLC 코드 및 펌웨어의 변경 내용도 확인할 수 있습니다. 

### <a name="iotot-threat-detection"></a>IoT/OT 위협 탐지  

특수 IoT/OT 인식 위협 인텔리전스 및 동작 분석을 통해 비정상 또는 무단 작업을 탐지합니다. 고정 IOC에서 놓친 제로데이 맬웨어, 파일리스 맬웨어, 지상 생활 전술 등의 지능형 위협도 탐지할 수 있습니다. 

### <a name="unified-security-management-across-iotot"></a>IoT/OT의 통합 보안 관리

Azure Sentinel에 통합되어 전체 조직에 대한 정보를 제공합니다. Splunk, IBM QRadar, ServiceNow 등의 타사 도구를 비롯한 기존 워크플로에 통합되어 통합 IoT/OT 보안 거버넌스를 구현합니다. 

## <a name="agent-based-solution-for-device-builders"></a>디바이스 빌더를 위한 에이전트 기반 솔루션 

보안은 IoT를 구현하는 거의 모든 사람이 고민하는 문제입니다. IoT 디바이스는 엔드포인트 모니터링, 보안 상태 관리 및 위협 탐지에 대한 고유한 요구 사항이 있으며, 각각 매우 구체적인 성능 요구 사항이 있습니다. 

Azure Defender for IoT 보안 에이전트를 사용하면 새 IoT 디바이스 및 Azure IoT 프로젝트에 직접 보안을 구축할 수 있습니다. 마이크로 에이전트는 이진 패키지로 배포하거나 소스 코드를 수정하는 기능을 비롯한 유연한 배포 옵션을 제공합니다. 그리고 마이크로 에이전트는 Linux나 Azure RTOS 같은 표준 IoT 운영 체제에 사용할 수 있습니다.  

Azure Defender for IoT 마이크로 에이전트는 보안 상태 관리, 위협 탐지 및 통합 보안 관리를 위한 기타 Microsoft 보안 도구와의 통합에 대한 엔드포인트 가시성을 제공합니다. 

### <a name="security-posture-management"></a>보안 상태 관리

IoT 디바이스의 보안 상태를 선제적으로 모니터링합니다. Azure Defender for IoT는 디바이스 관련 권장 사항과 함께 CIS 벤치마크를 기반으로 보안 상태 권장 사항을 제공합니다. OS 구성, 방화벽 구성 및 사용 권한을 비롯한 운영 체제 보안에 대한 가시성을 제공합니다. 

### <a name="endpoint-iotot-threat-detection"></a>엔드포인트 IoT/OT 위협 탐지

봇네트, 무차별 암호 대입 시도, 크립토 마이너, 의심스러운 네트워크 활동 등의 위협을 탐지합니다. 조직 내에서 가장 중요한 위협을 대상으로 하는 사용자 지정 경고를 만듭니다. 

### <a name="flexible-distribution-and-deployment-models"></a>유연한 배급 및 배포 모델 

Azure Defender for IoT 마이크로 에이전트에 소스 코드가 포함되어 있으므로 마이크로 에이전트를 펌웨어에 통합하거나 필요한 것만 포함하도록 사용자 지정할 수 있습니다. 이진 패키지로 사용하거나 다른 Azure IoT 솔루션에 직접 통합할 수도 있습니다. 

## <a name="see-also"></a>참고 항목

[Azure Defender for IoT 아키텍처](architecture.md)