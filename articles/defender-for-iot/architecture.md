---
title: 에이전트 없는 솔루션 아키텍처
description: IoT 에이전트 없는 아키텍처 및 정보 흐름에 대 한 Azure Defender에 대해 알아봅니다.
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
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 28449c4e80e22548d2f727e5ad330ab487528847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521903"
---
# <a name="azure-defender-for-iot-architecture"></a>IoT 용 Azure Defender 아키텍처

이 문서에서는 IoT 에이전트 없는 솔루션에 대 한 Defender의 기능적 시스템 아키텍처를 설명 합니다. IoT 용 Azure Defender는 환경 요구 사항, 조직에 대 한 에이전트 없는 솔루션 및 장치 빌더를 위한 에이전트 기반 솔루션에 적합 한 두 가지 기능 집합을 제공 합니다.

## <a name="agentless-solution-for-organizations"></a>조직을 위한 에이전트 없는 솔루션
### <a name="defender-for-iot-components"></a>IoT 용 Defender 구성 요소

IoT 용 Defender는 Azure 클라우드와 온-프레미스 구성 요소에 연결 합니다. 이 솔루션은 여러 원격 위치가 포함 된 크고 지리적으로 분산 된 환경에서 확장성을 위해 설계 되었습니다. 이 솔루션을 통해 국가, 지역, 사업부 또는 영역별로 다중 계층 분산 아키텍처를 사용할 수 있습니다. 

IoT 용 Azure Defender에는 다음 구성 요소가 포함 되어 있습니다. 

**클라우드 연결 배포**

- IoT 센서 VM 또는 어플라이언스 용 Azure Defender
- 클라우드 관리 및 Azure 센티널에 대 한 통합 Azure Portal
- 로컬 사이트 관리용 온-프레미스 관리 콘솔
- 포함 된 보안 에이전트 (옵션)

**Gapped (오프 라인) 배포**

- IoT 센서 VM 또는 어플라이언스 용 Azure Defender
- 로컬 사이트 관리를 위한 온-프레미스 관리 콘솔

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="IoT 용 Defender의 아키텍처입니다.":::

### <a name="azure-defender-for-iot-sensors"></a>IoT 용 Azure Defender 센서

IoT 센서에 대 한 Defender는 네트워크 장치를 검색 하 고 지속적으로 모니터링 합니다. 센서는 IoT 및 OT 장치에서 수동 (에이전트 없는) 모니터링을 사용 하 여 ICS 네트워크 트래픽을 수집 합니다. 
 
용도-IoT 및 OT 네트워크를 위해 작성 된 에이전트 없는 기술은 네트워크에 연결 되는 동안 몇 분 이내에 IoT에 대 한 심층 가시성을 제공 합니다. 이는 비 침입, NTA (네트워크 트래픽 분석) 방법으로 인해 네트워크 및 네트워크 장치에 대 한 성능에 영향을 주지 않습니다. 
 
특허, IoT 및 OT에서 인식 하는 동작 분석 및 계층 7 DPI (심층 패킷 검사)를 적용 하면 기존의 서명 기반 솔루션을 분석 하 여 비정상적인 또는 무단 활동을 기반으로 하는 고급 IoT 및 OT (fileless 맬웨어) 등의 위협 요소를 즉시 검색할 수 있습니다. 
  
IoT 센서에 대 한 Defender는 범위 포트 또는 네트워크 탭에 연결 하 고 IoT 및 OT 네트워크 트래픽에서 DPI를 즉시 수행 하기 시작 합니다. 
 
데이터 수집, 처리, 분석 및 경고는 센서에서 직접 발생 합니다. 이 프로세스를 사용 하면 메타 데이터만 관리 콘솔로 전송 되기 때문에 대역폭이 낮거나 대기 시간이 긴 위치에 이상적으로 적합 합니다.

센서에는 5 개의 분석 검색 엔진이 포함 되어 있습니다. 엔진은 실시간 및 미리 기록 된 트래픽 모두에 대 한 분석을 기반으로 경고를 트리거합니다. 다음 엔진을 사용할 수 있습니다. 

#### <a name="protocol-violation-detection-engine"></a>프로토콜 위반 검색 엔진
프로토콜 위반 검색 엔진은 ICS 프로토콜 사양을 위반 하는 패킷 구조 및 필드 값 (예: Modbus 예외 및 사용 되지 않는 함수 코드 경고의 시작)을 식별 합니다.

#### <a name="policy-violation-detection-engine"></a>정책 위반 검색 엔진
정책 위반 검색 엔진은 기계 학습을 사용 하 여 특정 함수 코드의 무단 사용, 특정 개체에 대 한 액세스 또는 장치 구성 변경 등의 기본 동작에서 발생 하는 모든 편차를 사용자에 게 알립니다. 예를 들어 DeltaV 소프트웨어 버전이 변경 되 고 권한 없는 PLC 프로그래밍 경고가 발생 합니다. 구체적으로 말하면, 정책 위반 엔진은, ISM (산업용 유한 상태 모델링) 이라는 특허 된 기법을 사용 하 여 상태 및 전환의 결정적 시퀀스로 ICS 네트워크를 모델링 합니다. 정책 위반 검색 엔진은 ICS 네트워크의 기준을 설정 하므로 플랫폼에서 원래 네트워크를 사용 하는 것이 아니라 원래 개발 된 일반적인 수학적 방법 또는 분석 보다 네트워크 기준을 더 짧게 만들 수 있습니다.

#### <a name="industrial-malware-detection-engine"></a>산업용 맬웨어 검색 엔진
산업용 맬웨어 검색 엔진은 Conficker, Black Energy, Havex, WannaCry, NotPetya 및 Triton와 같은 알려진 맬웨어가 있는지를 나타내는 동작을 식별 합니다. 

#### <a name="anomaly-detection-engine"></a>변칙 검색 엔진
변칙 검색 엔진은 비정상적인 M2M (컴퓨터 간) 통신 및 동작을 검색 합니다. ICS 네트워크를 상태와 전환의 결정적 시퀀스로 모델링 하 여 플랫폼은 일반적으로 사용 되는 것이 아니라 원래 개발 된 일반적인 수학적 방법 또는 분석 보다 학습 기간이 더 짧습니다. 또한 최소 가양성을 사용 하 여 이상 문제를 더 빠르게 검색 합니다. 변칙 검색 엔진 경고는 과도 한 SMB 로그인 시도를 포함 하 고 PLC 검색에서 검색 된 경고를 포함 합니다.

#### <a name="operational-incident-detection"></a>운영 인시던트 검색
운영 인시던트 검색은 장비 오류에 대 한 초기 신호를 나타낼 수 있는 간헐적인 연결과 같은 운영 문제를 검색 합니다. 예를 들어 장치는 연결이 끊어져 (응답 하지 않음) Siemens S7 stop PLC 명령이 경고를 보냈습니다.

### <a name="management-consoles"></a>관리 콘솔
하이브리드 환경에서 IoT 용 Azure Defender를 관리 하는 작업은 다음 두 관리 포털을 통해 수행 됩니다. 
- 센서 콘솔
- 온-프레미스 관리 콘솔
- Azure Portal

### <a name="sensor-console"></a>센서 콘솔
센서 검색은 네트워크 맵, 장치 인벤토리 및 광범위 한 보고서 범위 (예: 위험 평가 보고서, 데이터 마이닝 쿼리 및 공격 벡터)에서 보고 조사 하 고 분석할 수 있는 센서 콘솔에 표시 됩니다. 콘솔을 사용 하 여 센서 엔진에서 검색 한 위협, 파트너 시스템에 정보 전달, 사용자 관리 등을 보고 처리할 수도 있습니다.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="IoT 센서 콘솔용 Defender":::

### <a name="on-premises-management-console"></a>온-프레미스 관리 콘솔
온-프레미스 관리 콘솔을 사용 하면 SOC (security operations center) 운영자가 여러 센서에서 집계 된 경고를 단일 대시보드로 관리 및 분석 하 고 네트워크 상태를 전체적으로 볼 수 있습니다.

이 아키텍처는 SOC 수준에서 네트워크에 대 한 종합적인 통합 보기, 최적화 된 경고 처리 및 운영 네트워크 보안 제어를 제공 하 여 의사 결정 및 위험 관리가 이상으로 유지 되도록 합니다.

다중 테 넌 트, 모니터링, 데이터 분석 및 중앙 센서 원격 제어 외에도 관리 콘솔은 각 원격 어플라이언스에 대해 완전히 사용자 지정 된 보고 기능 및 경고 제외와 같은 추가 시스템 유지 관리 도구를 제공 합니다. 이 아키텍처는 사이트 수준, 영역 수준 및 SOC 내의 전역 관리에서 로컬 관리를 모두 지원 합니다.

복구에 필요한 모든 구성 파일의 백업을 정기적으로 수신 하는 백업 콘솔을 제공 하는 고가용성 구성에 대해 관리 콘솔을 배포할 수 있습니다. 주 콘솔에 오류가 발생 하면 장애 조치 (failover) 없이 가용성을 유지 하기 위해 로컬 사이트 관리 기기가 자동으로 장애 조치 (failover) 되어 백업 콘솔과 동기화 됩니다.

SOC 워크플로 및 실행 서적에 긴밀 하 게 통합 되어 있어 완화 작업 및 위협에 대 한 사이트 간 상관 관계를 쉽게 확인할 수 있습니다.

- 장치 관리, 위험 및 취약성 관리를 위한 단일 통합 플랫폼과 인시던트 대응을 통한 위협 모니터링을 통해 전체적인 복잡성을 줄입니다.

- 집계 및 상관 관계 – 모든 사이트에서 수집 된 데이터 및 경고를 표시, 집계 및 분석 합니다.

- 모든 센서 제어 – 단일 위치에서 모든 센서를 구성 하 고 모니터링 합니다.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="모든 경고 및 정보를 관리 합니다.":::

### <a name="azure-portal"></a>Azure Portal

Azure의 IoT 용 Defender 포털은 다음을 도와 주는 데 사용 됩니다.

- 솔루션 어플라이언스 구매

- 소프트웨어 설치 및 업데이트

- Azure에 센서 등록

- 위협 인텔리전스 패키지 업데이트

## <a name="next-steps"></a>다음 단계

[IoT 용 Defender FAQ](resources-frequently-asked-questions.md)

[시스템 필수 구성 요소](quickstart-system-prerequisites.md)
