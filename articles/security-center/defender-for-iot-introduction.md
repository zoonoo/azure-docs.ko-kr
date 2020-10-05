---
title: IoT용 Azure Defender
description: Azure Defender for IoT에 대해 알아보기
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448339"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Azure Defender for IoT 소개

보안 관리를 통합하고 하이브리드 클라우드 워크로드 및 Azure IoT 솔루션에서 엔드투엔드 위협 탐지 및 분석을 지원합니다.

Azure Defender for IoT는 다음을 제공합니다.

- **자산 검색 및 네트워크 매핑** - 디바이스 제조업체, 디바이스 유형 및 디바이스가 네트워크에서 통신하는 방법 등의 세부 정보 포함
- **취약성 관리** - CVE, 개방형 포트 및 무단 인터넷 연결에 대한 정보 포함
- **지속적인 위협 모니터링** - 대상 지정된 공격 또는 맬웨어 같은 비정상 또는 무단 활동을 나타내는 실시간 경고 포함

전체 정보는 [전용 설명서](https://docs.microsoft.com/azure/asc-for-iot/overview)에서 볼 수 있습니다.

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[Azure Defender](security-center-pricing.md) 필요|
|필요한 역할 및 권한:|머신의 NSG에 대한 쓰기 권한|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Azure Defender for IoT로 어떤 디바이스를 보호할 수 있나요?
Security Center, Azure Defender 및 CyberX Agentless Technology의 기능을 결합하여 다음을 보호할 수 있습니다.

- IoT Hub를 통해 연결된 **그린필드 디바이스**. 이렇게 하면 조직에서 통합된 단일 솔루션으로 최신, 인터넷 기반 디바이스 및 기존 ICS/SCADA 디바이스를 모두 보호하여 IoT 이니셔티브를 가속화할 수 있습니다.
    - 새 디바이스를 온보딩하고, 워크로드(리프 디바이스, Microsoft Edge 디바이스, IoT Hub)에 보안 정책을 적용하여 보안 표준 준수 및 향상된 보안 상태를 보장합니다.

- 제조, BMS(건물 관리 시스템), 생명 과학, 에너지 및 수도 설비, 석유 및 가스, 물류와 같은 OT(운영 기술) 환경에서 사용되는 **관리되지 않는 브라운필드 디바이스**. 
    - Azure Defender for IoT는 관리되지 않는 디바이스를 보호하기 위해 OT 환경에서 성능에 영향을 주지 않는 수동, 비침입 네트워크 트래픽 분석(NTA)용 온-프레미스 센서를 사용합니다. 
    - 또한 특허 받은 IoT/OT 인식 동작 분석 및 기계 학습과 특수 IoT/OT 프로토콜에 대한 심층 이해 기능이 통합되어 있어 규칙이나 서명을 구성할 필요가 없으므로 일반적인 배포가 며칠 또는 몇 주가 아닌 몇 분 또는 몇 시간 내에 이루어집니다. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Sentinel과 Azure Defender for IoT 통합
Azure Defender for IoT는 통합 IT/OT 보안 모니터링 및 거버넌스를 지원하기 위해 기본적으로 [Azure Sentinel](../sentinel/overview.md)과 통합됩니다.

SecOps 팀은 다음을 수행할 수 있습니다.

- Sentinel에 포함된 OT 관련 SOAR 플레이북을 통해 IoT/OT 위협을 빠르게 검색하고 대응
- Microsoft의 사내 IoT/OT 위협 인텔리전스 팀인 Section 52에서 제공하며 지속적으로 업데이트되는 IoT/OT 관련 위협 인텔리전스를 활용하세요.
- 기존 SOC 워크플로 및 Splunk, IBM QRadar 및 ServiceNow와 같은 타사 보안 도구와 Azure Defender for IoT 통합


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Security Center의 Azure Defender for IoT에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [IoT용 Azure Security Center 소개](../asc-for-iot/overview.md)
