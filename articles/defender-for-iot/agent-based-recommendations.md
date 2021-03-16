---
title: 에이전트 기반 권장 사항
titleSuffix: Azure Defender for IoT
description: 보안 권장 사항에 대 한 개념 및 IoT 장치용 Defender에 사용 되는 방법에 대해 알아봅니다.
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
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: e746f37fdf3b67467c1844ebea9191679d52d6d1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491268"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT 장치에 대 한 보안 권장 사항

Defender for IoT는 Azure 리소스 및 IoT 장치를 검색 하 고 보안 권장 사항을 제공 하 여 공격 노출 영역을 줄입니다.
보안 권장 사항은 보안 모범 사례를 준수 하는 고객을 지원 하기 위해 조치를 취할 수 있는 목표입니다.

이 문서에서는 IoT 장치에서 트리거할 수 있는 권장 사항 목록을 찾을 수 있습니다.

## <a name="agent-based-recommendations"></a>에이전트 기반 권장 사항

장치 권장 사항은 장치 보안 상태를 개선 하기 위한 정보 및 제안을 제공 합니다.

| Severity | 이름 | 데이터 원본 | Description |
|--|--|--|--|
| 중간 | 장치에서 포트 열기 | 클래식 Defender-마이크로 에이전트| 장치에서 수신 대기 끝점을 찾았습니다. |
| 중간 | 체인 중 하나에서 허용 되는 방화벽 정책을 찾았습니다. | 클래식 Defender-마이크로 에이전트| 허용 되는 방화벽 정책을 찾았습니다 (입/출력). 방화벽 정책에서는 기본적으로 모든 트래픽을 거부 하 고 장치에 대 한 필요한 통신을 허용 하는 규칙을 정의 합니다. |
| 중간 | 입력 체인에서 허용 되는 방화벽 규칙을 찾았습니다. | 클래식 Defender-마이크로 에이전트| 광범위 한 IP 주소 또는 포트에 대 한 허용 패턴이 포함 된 방화벽의 규칙이 있습니다. |
| 중간 | 출력 체인에서 허용 되는 방화벽 규칙을 찾았습니다. | 클래식 Defender-마이크로 에이전트| 광범위 한 IP 주소 또는 포트에 대 한 허용 패턴이 포함 된 방화벽의 규칙이 있습니다. |
| 중간 | 운영 체제 기준 유효성 검사에 실패 했습니다. | 클래식 Defender-마이크로 에이전트| 장치는 [Ci Linux 벤치 마크](https://www.cisecurity.org/cis-benchmarks/)를 준수 하지 않습니다. |

### <a name="agent-based-operational-recommendations"></a>에이전트 기반 운영 권장 사항

운영 권장 사항은 보안 에이전트 구성을 향상 시키기 위한 정보 및 제안을 제공 합니다.

| Severity | 이름 | 데이터 원본 | Description |
|--|--|--|--|
| 낮음 | 에이전트가 미달 사용 메시지를 보냅니다. | 클래식 Defender-마이크로 에이전트| 지난 24 시간 동안 10% 이상의 보안 메시지가 4kb 보다 작습니다. |
| 낮음 | 보안 쌍 구성이 최적이 아닙니다. | 클래식 Defender-마이크로 에이전트| 보안 쌍 구성이 최적이 아닙니다. |
| 낮음 | 보안 쌍 구성 충돌 | 클래식 Defender-마이크로 에이전트| 보안 쌍 구성에서 충돌을 식별 했습니다. |  |

## <a name="next-steps"></a>다음 단계

- IoT 서비스용 Defender [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md) 하는 방법 알아보기
- [장치 조사](how-to-investigate-device.md) 에 대 한 자세한 정보
