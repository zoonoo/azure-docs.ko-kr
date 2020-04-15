---
title: 솔루션 아키텍처
description: IoT 서비스를 위한 Azure 보안 센터에서 정보 흐름에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311760"
---
# <a name="azure-security-center-for-iot-architecture"></a>IoT용 Azure Security Center 아키텍처

이 문서에서는 IoT 솔루션을 위한 Azure 보안 센터의 기능 시스템 아키텍처에 대해 설명합니다.

## <a name="azure-security-center-for-iot-components"></a>IoT 구성 요소를 위한 Azure 보안 센터

IoT용 Azure 보안 센터는 다음과 같은 구성 요소로 구성됩니다.

- IoT 허브 통합
- 장치 에이전트(선택 사항)
- 보안 메시지 SDK 보내기
- 분석 파이프라인

### <a name="azure-security-center-for-iot-workflows"></a>IoT 워크플로우를 위한 Azure 보안 센터

IoT용 Azure 보안 센터는 기본 제공 및 향상된 두 가지 기능 워크플로 중 하나에서 작동합니다.

### <a name="built-in"></a>기본 제공

**기본 제공** 모드에서IoT용 Azure 보안 센터는 IoT Hub에서 **보안** 옵션을 사용하도록 선택할 때 활성화됩니다. 실시간 모니터링, 권장 사항 및 경고를 제공하는 기본 제공 모드는 단일 단계 장치 가시성과 타의 추종을 불허하는 보안을 제공합니다. 빌드 모드는 모든 장치에 에이전트를 설치할 필요가 없으며 기록된 활동에 대한 고급 분석을 사용하여 필드 장치를 분석하고 보호합니다.

### <a name="enhanced"></a>향상된

**향상된** 모드에서는 IoT Hub에서 **보안** 옵션을 켜고 장치에 IoT 장치 에이전트용 Azure 보안 센터를 설치한 후 에이전트는 장치에서 원시 보안 이벤트를 수집, 집계 및 분석합니다. 원시 보안 이벤트에는 IP 연결, 프로세스 생성, 사용자 로그인 및 기타 보안 관련 정보가 포함될 수 있습니다. 또한 IoT 장치 에이전트용 Azure 보안 센터에서는 높은 네트워크 처리량을 방지하는 데 도움이 되는 이벤트 집계도 처리합니다. 에이전트는 매우 사용자 정의할 수 있으므로 가장 빠른 SLA에서 중요한 정보만 보내거나 광범위한 보안 정보 및 컨텍스트를 더 큰 세그먼트로 집계하여 더 높은 서비스 비용을 피할 수 있는 특정 작업에 사용할 수 있습니다.

![IoT용 Azure Security Center 아키텍처](./media/architecture/azure-iot-security-architecture.png)

장치 에이전트 및 기타 응용 프로그램은 **Azure 보내기 보안 메시지 SDK를** 사용하여 Azure IoT Hub로 보안 정보를 보냅니다. IoT Hub는 이 정보를 픽업하여 IoT 서비스를 위한 Azure 보안 센터로 전달합니다.

IoT 서비스에 대한 Azure 보안 센터를 사용하도록 설정하면 전달된 데이터 외에도 IoT Hub는 IoT용 Azure 보안 센터에서 분석을 위해 모든 내부 데이터를 보냅니다. 이 데이터에는 장치 클라우드 작업 로그, 장치 ID 및 허브 구성이 포함됩니다. 이 모든 정보는 IoT 분석 파이프라인을 위한 Azure 보안 센터를 만드는 데 도움이 됩니다.

또한 IoT 분석 파이프라인용 Azure 보안 센터는 Microsoft 및 Microsoft 파트너 내의 다양한 소스에서 추가 위협 인텔리전스 스트림을 수신합니다. IoT 전체 분석 파이프라인용 Azure 보안 센터는 서비스에서 만든 모든 고객 구성(예: 사용자 지정 경고 및 전송 보안 메시지 SDK 사용)과 함께 작동합니다.

IoT용 Azure 보안 센터는 분석 파이프라인을 사용하여 모든 정보 스트림을 결합하여 실행 가능한 권장 사항 및 경고를 생성합니다. 파이프라인에는 보안 연구원과 전문가가 만든 사용자 지정 규칙과 표준 장치 동작 및 위험 분석에서 편차를 검색하는 기계 학습 모델이 모두 포함되어 있습니다.

IoT 권장 사항 및 경고(분석 파이프라인 출력)에 대한 Azure 보안 센터는 각 고객의 Log Analytics 작업 영역에 기록됩니다. 작업 영역의 원시 이벤트와 경고 및 권장 사항을 포함하면 검색된 의심스러운 활동의 정확한 세부 정보를 사용하여 심층 조사 및 쿼리를 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 솔루션을 위한 Azure 보안 센터의 기본 아키텍처 및 워크플로에 대해 배웠습니다. IoT Hub에서 보안 솔루션을 시작하고 활성화하는 방법, 필수 구성 요소에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [서비스 필수 구성 요소](service-prerequisites.md)
- [시작](getting-started.md)
- [솔루션 구성](quickstart-configure-your-solution.md)
- [IoT Hub에서 보안 지원](quickstart-onboard-iot-hub.md)
- [IoT FAQ용 Azure 보안 센터](resources-frequently-asked-questions.md)
- [IoT용 Azure Security Center 보안 경고](concept-security-alerts.md)
