---
title: Azure RTOS에 대 한 Defender의 기본 사항에 대 한 개념 설명
description: Azure의 Azure RTOS 개념 및 워크플로에 대 한 기본 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 83557930aeeccbb557382583e4d6666a000ce52c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779276"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Azure RTOS (미리 보기) 용 Defender-마이크로 에이전트

이 문서를 사용 하 여 기능 및 이점과 관련 구성 및 참조 리소스에 대 한 링크를 포함 하 여 Azure RTOS 용 Defender IoT-마이크로 에이전트를 보다 잘 이해할 수 있습니다. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-마이크로 에이전트

Azure RTOS 용 Defender-마이크로 에이전트는 NetX 듀오 제품의 일부로 Azure RTOS 장치에 대 한 포괄적인 보안 솔루션을 제공 합니다. NetX 듀오 제품 내에서 Azure RTOS는 Azure IoT Defender-마이크로 에이전트 기본 제공 기능을 제공 하 고, 정품 인증 된 후 실시간 운영 체제 장치에서 일반적인 위협에 대 한 검사를 제공 합니다. 

Azure RTOS 용 Defender-IoT-마이크로 에이전트는 백그라운드에서 실행 되며, 각 고객의 고유 연결을 사용 하 여 IoT Hub에 보안 메시지를 전송 하는 동안 원활한 사용자 환경을 제공 합니다. Azure RTOS 용 Defender-IoT-마이크로 에이전트는 기본적으로 사용 하도록 설정 되어 있습니다.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo는 강력 하 고 임베디드 실시간 및 IoT 응용 프로그램을 위해 특별히 설계 된 고급 산업 등급 TCP/IP 네트워크 스택입니다. Azure RTOS NetX 듀어은 보안 및 클라우드를 비롯 한 다양 한 프로토콜 집합을 제공 하는 이중 IPv4 및 IPv6 네트워크 스택입니다. [Azure RTOS NetX 듀오](/azure/rtos/netx-duo/) 솔루션에 대해 자세히 알아보세요.

모듈은 다음과 같은 기능을 제공 합니다.

- **악의적인 네트워크 작업 검색**
- **사용자 지정 경고를 기반으로 하는 장치 동작 기준**
- **장치 보안 예방 강화**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Azure RTOS 아키텍처에 대 한 Defender-IoT-마이크로 에이전트

Azure RTOS 용 Defender-IoT-마이크로 에이전트는 Azure IoT 미들웨어 플랫폼에 의해 초기화 되며 IoT Hub 클라이언트를 사용 하 여 허브로 보안 원격 분석을 보냅니다.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-마이크로 에이전트 상태 다이어그램 및 정보 흐름":::

Azure RTOS 용 Defender-IoT-마이크로 에이전트는 세 개의 수집기를 사용 하 여 다음 장치 활동 및 정보를 모니터링 합니다.
- 장치 네트워크 활동 **TCP**, **UDP** 및 **ICM**
- **Threadx** 및 **netx Duo** 버전의 시스템 정보
- 하트비트 이벤트

각 수집기는 우선 순위 그룹에 연결 되 고 각 우선 순위 그룹에는 **낮음**, **중간** 및 **높음** 의 가능한 값을 가진 고유한 간격이 있습니다. 간격은 데이터를 수집 하 고 전송 하는 시간 간격에 영향을 줍니다.

각 시간 간격은 구성 가능 하며, 솔루션을 추가로 [사용자 지정](how-to-azure-rtos-security-module.md)하기 위해 IoT 커넥터를 사용 하도록 설정 하 고 사용 하지 않도록 설정할 수 있습니다. 

## <a name="supported-security-alerts-and-recommendations"></a>지원 되는 보안 경고 및 권장 사항

Azure RTOS 용 Defender-IoT-마이크로 에이전트는 특정 보안 경고 및 권장 사항을 지원 합니다. 초기 구성을 완료 한 후 서비스에 대 한 [관련 경고 및 권장 값을 검토 하 고 사용자 지정](concept-rtos-security-alerts-recommendations.md) 해야 합니다.

## <a name="ready-to-begin"></a>시작할 준비가 되셨습니까?

Azure RTOS 용 Defender-마이크로 에이전트가 IoT 장치에 대 한 무료 다운로드로 제공 됩니다. IoT 클라우드 서비스용 Defender는 Azure 구독 당 30 일 평가판으로 사용할 수 있습니다. [지금 Defender-마이크로 에이전트를 다운로드 하 여](https://github.com/azure-rtos/azure-iot-preview/releases) 시작 해 보겠습니다. 

## <a name="next-steps"></a>다음 단계

- Azure RTOS [필수 구성 요소 및 설치](quickstart-azure-rtos-security-module.md)를 위한 Defender-마이크로 에이전트를 시작 합니다.
- Azure RTOS [보안 경고 및 권장 사항 지원](concept-rtos-security-alerts-recommendations.md)에 대 한 자세한 내용은 Defender-IoT-마이크로 에이전트를 확인 하세요. 
- Azure RTOS [참조 API](azure-rtos-security-module-api.md)에 대해 Defender-IoT-마이크로 에이전트를 사용 합니다.