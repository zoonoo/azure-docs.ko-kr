---
title: IoT 용 Azure Defender 아키텍처
description: IoT 아키텍처 및 정보 흐름에 대 한 Azure Defender에 대해 알아봅니다.
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
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: 4189cb8628db58343c816535836af82825014b7e
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096320"
---
# <a name="azure-defender-for-iot-architecture"></a>IoT 용 Azure Defender 아키텍처

이 문서에서는 IoT 용 Defender 솔루션의 기능적 시스템 아키텍처에 대해 설명 합니다.

## <a name="defender-for-iot-components"></a>IoT 용 Defender 구성 요소

IoT 용 Defender는 Azure 클라우드와 온-프레미스 구성 요소에 모두 연결 됩니다. 이 솔루션은 여러 원격 위치가 포함 된 크고 지리적으로 분산 된 환경에서 확장성을 위해 설계 되었습니다. 이 솔루션을 통해 국가, 지역, 사업부 또는 영역별로 다중 계층 분산 아키텍처를 사용할 수 있습니다. 

IoT 용 Azure Defender에는 다음 구성 요소가 포함 되어 있습니다. 

**클라우드 연결 배포**

- IoT 센서 VM 또는 어플라이언스 용 Azure Defender
- 클라우드 관리 및 Azure 센티널에 대 한 통합 Azure Portal
- 로컬 사이트 관리용 온-프레미스 관리 콘솔
- 포함 된 보안 에이전트 (옵션)

**Gapped (오프 라인) 배포**

- IoT 센서 VM 또는 어플라이언스 용 Azure Defender
- 로컬 사이트 관리를 위한 온-프레미스 관리 콘솔


![IoT 용 Defender 아키텍처](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>IoT 용 Azure Defender 센서

IoT 센서에 대 한 Defender는 네트워크 장치를 검색 하 고 지속적으로 모니터링 합니다. 센서는 IoT 및 OT 장치에서 수동 (에이전트 없는) 모니터링을 사용 하 여 ICS 네트워크 트래픽을 수집 합니다. 
 
용도-IoT 및 OT 네트워크를 위해 작성 된 에이전트 없는 기술은 네트워크에 연결 되는 동안 몇 분 이내에 IoT에 대 한 심층 가시성을 제공 합니다. 이는 비 침입, NTA (네트워크 트래픽 분석) 방법으로 인해 네트워크 및 네트워크 장치에 대 한 성능에 영향을 주지 않습니다. 
 
특허, IoT 및 OT에서 인식 하는 동작 분석 및 계층 7 DPI (심층 패킷 검사)를 활용 하면 기존 서명 기반 솔루션을 분석 하 여 비정상적인 또는 무단 활동을 기반으로 하는 고급 IoT 및 OT (fileless 맬웨어) 등의 위협 요소를 즉시 검색할 수 있습니다. 
  
IoT 센서에 대 한 Defender는 범위 포트 또는 네트워크 탭에 연결 하 고 IoT 및 OT 네트워크 트래픽에서 DPI를 즉시 수행 하기 시작 합니다. 
 
데이터 수집, 처리, 분석 및 경고는 센서에서 직접 발생 합니다. 이렇게 하면 메타 데이터만 관리 콘솔로 전송 되므로 대역폭이 낮거나 대기 시간이 긴 위치에 이상적으로 적합 합니다.

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

#### <a name="sensor-console"></a>센서 콘솔
센서 검색은 네트워크 맵, 자산 인벤토리 및 광범위 한 보고서 (예: 위험 평가 보고서, 데이터 마이닝 쿼리 및 공격 벡터)에서 보고 조사 하 고 분석할 수 있는 센서 콘솔에 표시 됩니다. 또한 콘솔을 사용 하 여 센서 엔진에서 검색 한 위협, 타사 시스템에 정보 전달, 사용자 관리 등을 확인 하 고 처리할 수 있습니다.

![IoT 센서 콘솔용 Defender](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>온-프레미스 관리 콘솔
온-프레미스 관리 콘솔을 사용 하면 SOC (security operations center) 운영자가 여러 센서에서 집계 된 경고를 단일 대시보드로 관리 및 분석 하 고 네트워크 상태를 전체적으로 볼 수 있습니다.

이 아키텍처는 SOC 수준에서 네트워크에 대 한 종합적인 통합 보기, 최적화 된 경고 처리 및 운영 네트워크 보안 제어를 제공 하 여 의사 결정 및 위험 관리가 이상으로 유지 되도록 합니다.

다중 테 넌 트, 모니터링, 데이터 분석 및 중앙 센서 원격 제어 외에도 관리 콘솔은 각 원격 어플라이언스에 대 한 추가 시스템 유지 관리 도구 (예: 경고 제외) 및 완전히 사용자 지정 된 보고 기능을 제공 합니다. 이러한 확장 가능한 아키텍처는 사이트 수준, 영역 수준 및 SOC 내의 전역 관리에서 로컬 관리를 모두 지원 합니다.

복구에 필요한 모든 구성 파일의 백업을 정기적으로 수신 하는 백업 콘솔을 제공 하는 고가용성 구성에 대해 관리 콘솔을 배포할 수 있습니다. 마스터 콘솔에 오류가 발생 하면 장애 조치 (failover) 없이 가용성을 유지 하기 위해 로컬 사이트 관리 기기가 자동으로 장애 조치 (failover) 되어 백업 콘솔과 동기화 됩니다.

#### <a name="azure-portal"></a>Azure portal

Azure의 IoT 포털 용 Defender는 다음을 도와 주는 데 사용 됩니다.   솔루션 어플라이언스 구매 ·   소프트웨어 설치 및 업데이트 ·   Azure에 센서 등록   위협 인텔리전스 패키지 업데이트

## <a name="embedded-security-agent-built-in-mode"></a>포함 된 보안 에이전트: 기본 제공 모드

**기본 제공** 모드에서 IoT 용 Defender는 IoT Hub에서 **보안** 옵션을 설정 하도록 선택할 때 사용 하도록 설정 됩니다. 실시간 모니터링, 권장 사항 및 경고를 제공 하는 기본 제공 모드는 단일 단계 장치 가시성 및 보안 불일치를 제공 합니다. 빌드 모드에서는 모든 장치에 에이전트를 설치할 필요가 없으며, 기록 된 활동에 대 한 고급 분석을 사용 하 여 현장 장치 및 IoT hub를 분석 하 고 보호 합니다.

## <a name="embedded-security-agent-enhanced-mode"></a>포함 된 보안 에이전트: 고급 모드

**고급** 모드에서는 IoT Hub의 **보안** 옵션을 설정 하 고 장치에 IoT 장치 에이전트를 설치 하 고 나면 에이전트는 장치에서 원시 보안 이벤트를 수집, 집계 및 분석 합니다. 원시 보안 이벤트에는 IP 연결, 프로세스 만들기, 사용자 로그인 및 기타 보안 관련 정보가 포함 될 수 있습니다. IoT 용 Defender 장치 에이전트는 또한 높은 네트워크 처리량을 방지 하기 위해 이벤트 집계를 처리 합니다. 에이전트는 항상 사용자 지정이 가능 하므로, 가장 빠른 SLA에서 중요 한 정보만 보내거나 광범위 한 보안 정보 및 컨텍스트를 더 큰 세그먼트로 집계 하 여 더 높은 서비스 비용을 방지 하는 등의 특정 작업에 사용할 수 있습니다.

장치 에이전트 및 기타 응용 프로그램은 **Azure send security MESSAGE SDK** 를 사용 하 여 Azure IoT Hub에 보안 정보를 보냅니다. IoT Hub이 정보를 가져와 IoT 서비스용 Defender에 전달 합니다.

IoT 용 Defender 서비스를 사용 하도록 설정 하면 전달 된 데이터 외에도 IoT 용 Defender에서 분석할 수 있도록 모든 내부 데이터를 전송 IoT Hub. 이 데이터에는 장치-클라우드 작업 로그, 장치 id 및 허브 구성이 포함 됩니다. 이 모든 정보를 통해 IoT 분석 파이프라인에 대 한 Defender를 만들 수 있습니다.

또한 Defender for IoT 분석 파이프라인은 Microsoft 및 Microsoft 파트너 내 다양 한 소스에서 추가 위협 인텔리전스 스트림을 받습니다. IoT 용 Defender 전체 분석 파이프라인은 서비스에서 만들어진 모든 고객 구성 (예: 사용자 지정 경고 및 보안 메시지 SDK 전송 사용)과 함께 작동 합니다.

IoT 용 Defender는 분석 파이프라인을 사용 하 여 모든 정보 스트림을 결합 하 여 조치 가능한 권장 사항 및 경고를 생성 합니다. 파이프라인에는 보안 연구원 및 전문가가 만든 사용자 지정 규칙과 표준 장치 동작 및 위험 분석의 편차를 검색 하는 기계 학습 모델이 모두 포함 됩니다.

Defender for IoT 권장 사항 및 경고 (분석 파이프라인 출력)는 각 고객의 Log Analytics 작업 영역에 기록 됩니다. 경고 및 권장 사항 뿐만 아니라 작업 영역에 원시 이벤트를 포함 하면 검색 된 의심 스러운 활동의 정확한 세부 정보를 사용 하 여 심층 조사 및 쿼리를 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 아키텍처 및 IoT 용 Defender 솔루션 워크플로에 대해 알아보았습니다. 필수 구성 요소, 시작 및 IoT Hub에서 보안 솔루션을 사용 하도록 설정 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [서비스 필수 구성 요소](service-prerequisites.md)
- [시작](getting-started.md)
- [솔루션 구성](quickstart-configure-your-solution.md)
- [IoT Hub에서 보안 사용](quickstart-onboard-iot-hub.md)
- [IoT 용 Defender FAQ](resources-frequently-asked-questions.md)
- [IoT 용 Defender 보안 경고](concept-security-alerts.md)
