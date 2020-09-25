---
title: IoT용 Azure Defender
description: IoT 용 Azure Defender에 대 한 자세한 정보
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 307916680d9a05a5083aea057b2ef4b855a4ff57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301808"
---
# <a name="introduction-to-azure-defender-for-iot"></a>IoT 용 Azure Defender 소개

보안 관리를 통합 하 고 하이브리드 클라우드 워크 로드 및 Azure IoT 솔루션에서 종단 간 위협 검색 및 분석을 가능 하 게 합니다.

IoT 용 Azure Defender는 다음을 제공 합니다.

- 장치 제조업체, 장치 유형, 네트워크에서 장치가 통신 하는 방법 등의 세부 정보를 포함 하는 **자산 검색 및 네트워크 매핑**
- CVEs, open ports 및 권한이 없는 인터넷 연결에 대 한 정보를 포함 하는 **취약성 관리**
- **지속적인 위협 모니터링**-대상 공격 또는 맬웨어에 대 한 비정상 또는 무단 작업을 나타내는 실시간 경고를 포함 합니다.

전체 세부 정보는 [전용 설명서](https://docs.microsoft.com/azure/asc-for-iot/overview)에서 확인할 수 있습니다.

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|[Azure Defender](security-center-pricing.md) 필요|
|필요한 역할 및 사용 권한:|컴퓨터의 NSGs에 대 한 쓰기 권한|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>IoT 용 Azure Defender는 어떤 장치를 안전 하 게 보호할 수 있나요?
Security Center, Azure Defender 및 CyberX 에이전트 없는 기술의 기능을 결합 하 여 다음과 같은 보안을 유지할 수 있습니다.

- IoT Hub을 통해 연결 된 **장치를 최적의** 합니다. 이렇게 하면 조직에서 통합 된 단일 솔루션으로 최신, 인터넷 기본 장치 및 기존 ICS/SCADA 장치를 모두 보호 하 여 IoT 이니셔티브를 가속화할 수 있습니다.
    - 새 장치를 등록 하 고 워크 로드 (리프 장치, Microsoft Edge 장치, IoT Hub)에 보안 정책을 적용 하 여 보안 표준 및 향상 된 보안 상태를 준수 하는지 확인 합니다.

- 제조, BMS (건물 관리 시스템), 생명 과학, 에너지 및 물 유틸리티, 석유 & 가스 및 물류와 같은 OT (운영 기술) 환경에서 사용 되는 **관리 되지 않는 brownfield 장치** 입니다. 
    - 관리 되지 않는 장치를 보호 하기 위해 Azure Defender IoT는 OT 환경에서 성능에 영향을 주지 않는 수동, 비 침입 네트워크 트래픽 분석 (NTA)에 대해 온-프레미스 센서를 사용 합니다. 
    - 또한 특수 IoT/OT 프로토콜에 대 한 심층적인 이해를 포함 하 고 있습니다 .이는 특허, IoT/OT 인식 동작 분석 및 기계 학습을 사용 하 여 규칙이 나 서명을 구성할 필요가 없도록 하 여 며칠 또는 몇 주가 아닌 몇 분 또는 몇 시간 내에 일반적인 배포를 수행 하는 것을 방지 합니다. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure 센티널과 IoT 용 azure Defender 통합
통합 IT/OT 보안 모니터링 및 거 버 넌 스를 사용 하도록 설정 하기 위해 Azure Defender for IoT는 기본적으로 [Azure 센티널](../sentinel/overview.md)과 통합 됩니다.

SecOps 팀은 다음을 수행할 수 있습니다.

- 센티널에 포함 된 OT 별 대화 충성도 플레이 북을 통해 IoT/OT 위협에 빠르게 검색 하 고 대응
- Microsoft 사내 IoT/OT 위협 인텔리전스 팀의 52 섹션에서 제공 하는 지속적인 업데이트 IoT/OT 관련 위협 인텔리전스를 활용 하세요.
- 기존 SOC 워크플로 및 Splunk, IBM QRadar 및 ServiceNow와 같은 타사 보안 도구와 IoT 용 Azure Defender 통합


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Security Center의 IoT 용 Azure Defender에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [IoT용 Azure Security Center 소개](../asc-for-iot/overview.md)
