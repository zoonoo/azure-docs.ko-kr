---
title: 에이전트 기반 솔루션 아키텍처
description: IoT 에이전트 기반 아키텍처 및 정보 흐름에 대 한 Azure Defender에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 51b1f639ada01eda06bdf6c938eef7dce2a379fc
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448611"
---
# <a name="agent-based-solution-for-device-builders"></a>디바이스 빌더를 위한 에이전트 기반 솔루션

이 문서에서는 IoT 에이전트 기반 솔루션에 대 한 Defender의 기능적 시스템 아키텍처를 설명 합니다. IoT 용 Azure Defender는 환경 요구 사항, 조직에 대 한 에이전트 없는 솔루션 및 장치 빌더를 위한 에이전트 기반 솔루션에 적합 한 두 가지 기능 집합을 제공 합니다.

## <a name="iot-hub-built-in-security"></a>IoT hub 기본 제공 보안

IoT 용 Defender는 생성 된 모든 새 IoT Hub에서 기본적으로 사용 하도록 설정 됩니다. Defender for IoT는 장치에 에이전트를 설치 하지 않고도 실시간 모니터링, 권장 사항 및 경고를 제공 하 고, 기록 된 IoT Hub 메타 데이터에 대 한 고급 분석을 사용 하 여 현장 장치 및 IoT hub를 분석 하 고 보호 합니다. 

## <a name="defender-for-iot-micro-agent"></a>IoT 마이크로 에이전트의 Defender 

IoT 마이크로 에이전트의 Defender는 깊이 있는 보안 보호 및 장치 동작에 대 한 가시성을 제공 합니다. 장치에서 원시 보안 이벤트를 수집, 집계 및 분석 합니다. 원시 보안 이벤트에는 IP 연결, 프로세스 만들기, 사용자 로그인 및 기타 보안 관련 정보가 포함 될 수 있습니다. IoT 용 Defender 장치 에이전트는 또한 높은 네트워크 처리량을 방지 하기 위해 이벤트 집계를 처리 합니다. 에이전트는 항상 사용자 지정이 가능 하므로, 가장 빠른 SLA에서 중요 한 정보만 보내거나 광범위 한 보안 정보 및 컨텍스트를 더 큰 세그먼트로 집계 하 여 더 높은 서비스 비용을 방지 하는 등의 특정 작업에 사용할 수 있습니다.

장치 에이전트 및 기타 응용 프로그램은 **azure send security MESSAGE SDK** 를 사용 하 여 azure IoT hub에 보안 정보를 보냅니다. IoT hub는이 정보를 가져와 IoT 서비스용 Defender에 전달 합니다.

IoT service 용 Defender를 사용 하도록 설정 하면 전달 된 데이터 외에도 iot hub는 IoT 용 Defender에서 분석을 위해 모든 내부 데이터를 전송 합니다. 이 데이터에는 장치-클라우드 작업 로그, 장치 id 및 허브 구성이 포함 됩니다. 이 모든 정보를 통해 IoT 분석 파이프라인에 대 한 Defender를 만들 수 있습니다.

또한 Defender for IoT 분석 파이프라인은 Microsoft 및 Microsoft 파트너 내 다양 한 소스에서 다른 위협 인텔리전스 스트림을 수신 합니다. IoT 용 Defender 전체 분석 파이프라인은 서비스에서 만들어진 모든 고객 구성 (예: 사용자 지정 경고 및 보안 메시지 SDK 전송 사용)과 함께 작동 합니다.

IoT 용 Defender는 분석 파이프라인을 사용 하 여 모든 정보 스트림을 결합 하 여 조치 가능한 권장 사항 및 경고를 생성 합니다. 파이프라인에는 보안 연구원 및 전문가가 만든 사용자 지정 규칙과 표준 장치 동작 및 위험 분석의 편차를 검색 하는 기계 학습 모델이 모두 포함 됩니다.

Defender for IoT 권장 사항 및 경고 (분석 파이프라인 출력)는 각 고객의 Log Analytics 작업 영역에 기록 됩니다. 작업 영역에 원시 이벤트를 포함 하 고 경고 및 권장 사항을 포함 하면 검색 된 의심 스러운 활동의 정확한 세부 정보를 사용 하 여 심층 조사 및 쿼리를 수행할 수 있습니다.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="마이크로 에이전트 아키텍처입니다.":::

## <a name="see-also"></a>참고 항목

[IoT 용 Defender FAQ](resources-frequently-asked-questions.md)

[시스템 필수 구성 요소](quickstart-system-prerequisites.md)
