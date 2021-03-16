---
title: Azure RTOS 개요에 대 한 Defender-IoT-마이크로 에이전트
description: IoT 용 Azure Defender의 일부로 Azure RTOS 지원 및 구현을 위한 Defender-IoT-마이크로 에이전트에 대해 자세히 알아보세요.
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
ms.date: 01/14/2021
ms.author: shhazam
ms.openlocfilehash: ae1ae941dcb1af73286a4865089b1be227c484fc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496051"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>개요: Azure RTOS 용 Defender-IoT-마이크로 에이전트 (미리 보기)

Azure Defender for IoT 마이크로 모듈은 Azure RTOS를 사용 하는 장치에 대 한 포괄적인 보안 솔루션을 제공 합니다. 이 서비스는 RTOS (실시간 운영 체제) 장치에서 일반적인 위협과 잠재적 악성 활동에 대 한 검사를 제공 합니다. Azure RTOS는 이제 기본 제공 되는 Azure IoT Defender-마이크로 에이전트와 함께 제공 됩니다.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="IoT Azure RTOS 용 Defender의 시각화.":::


Azure RTOS의 마이크로 모듈은 다음과 같은 기능을 제공 합니다.

- 악의적인 네트워크 활동 검색
- 사용자 지정 경고 기반 장치 동작 기준선 지정
- 장치 보안 예방 강화

## <a name="detect-malicious-network-activities"></a>악의적인 네트워크 작업 검색

각 장치의 인바운드 및 아웃 바운드 네트워크 활동이 모니터링 됩니다. IPv4 및 i p v 6에서 지원 되는 프로토콜은 TCP, UDP 및 ICMP입니다. IoT 용 Defender는 Microsoft 위협 인텔리전스 피드에 대해 이러한 각 네트워크 활동을 검사 합니다. 피드는 전 세계에 수집 된 수백만 개의 고유한 위협 지표로 실시간으로 업데이트 됩니다.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>사용자 지정 경고를 기반으로 하는 장치 동작 기준선 지정

기준 기준선을 통해 장치를 보안 그룹으로 클러스터링 하 고 각 그룹의 예상 동작을 정의할 수 있습니다. IoT 장치는 일반적으로 잘 정의 되 고 제한 된 시나리오에서 작동 하도록 설계 되었으므로 매개 변수 집합을 사용 하 여 예상 되는 동작을 정의 하는 기준선을 쉽게 만들 수 있습니다. 기준선의 모든 편차는 경고를 트리거합니다.

## <a name="improve-your-device-security-hygiene"></a>장치 보안 예방 강화

IoT에 권장 되는 인프라 Defender를 사용 하면 장치의 보안 상태에 영향을 줄 수 있는 환경에서 발생 하는 문제에 대 한 지식과 정보를 얻을 수 있습니다. 약한 IoT 장치 보안 상태에서 변경 되지 않은 상태로 유지 되는 경우 잠재적인 공격이 성공 하도록 허용할 수 있습니다. 보안은 항상 모든 조직 내의 가장 약한 링크로 측정 됩니다.

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS 장치 보호 시작 하기

Azure RTOS 용 Defender-마이크로 에이전트는 장치에 대 한 무료 다운로드로 제공 됩니다. IoT 클라우드 서비스용 Defender는 Azure 구독 당 30 일 평가판으로 사용할 수 있습니다. 시작 하려면 [Azure RTOS 용 Defender-IoT-마이크로 에이전트](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md)를 다운로드 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure RTOS 용 Defender-IoT-마이크로 에이전트에 대해 알아보았습니다. Defender-마이크로 에이전트에 대해 자세히 알아보고 시작 하려면 다음 문서를 참조 하세요.

- [Azure RTOS IoT Defender-마이크로 에이전트 개념](concept-rtos-security-module.md)
- [빠른 시작: Azure RTOS IoT Defender-마이크로 에이전트](quickstart-azure-rtos-security-module.md)
