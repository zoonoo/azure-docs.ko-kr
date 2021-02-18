---
title: IoT Hub에 대 한 보안 권장 사항
description: 보안 권장 사항의 개념과 Defender for IoT Hub에 사용 되는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 4ae479fcff0514b42d46aef455d8a32d6ebfaf23
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636546"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub에 대 한 보안 권장 사항

Defender for IoT는 Azure 리소스 및 IoT 장치를 검색 하 고 보안 권장 사항을 제공 하 여 공격 노출 영역을 줄입니다.
보안 권장 사항은 보안 모범 사례를 준수 하는 고객을 지원 하기 위해 조치를 취할 수 있는 목표입니다.

이 문서에는 IoT Hub에서 트리거될 수 있는 권장 사항 목록이 있습니다.

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub의 기본 제공 권장 사항

권장 사항 경고는 환경의 보안 상태를 개선 하기 위한 작업에 대 한 통찰력 및 제안을 제공 합니다.

| 심각도 | 속성 | 데이터 원본 | Description |
|--|--|--|--|
| 높음 | 여러 장치에서 사용 하는 동일한 인증 자격 증명 | IoT Hub | IoT Hub 인증 자격 증명은 여러 장치에서 사용 됩니다. 이 프로세스는 합법적인 장치를 가장 하는 불법 장치를 나타낼 수 있습니다. 중복 자격 증명 사용은 악성 행위자에 의해 장치 가장의 위험을 늘립니다. |
| 중간 | 기본 IP 필터 정책은 거부 되어야 합니다. | IoT Hub | IP 필터 구성에는 허용 되는 트래픽에 대해 정의 된 규칙이 있어야 하며 기본적으로 다른 모든 트래픽은 기본적으로 거부 해야 합니다. |
| 중간 | IP 필터 규칙에 많은 IP 범위가 포함 되어 있습니다. | IoT Hub | 허용 IP 필터 규칙 원본 IP 범위가 너무 깁니다. 과도 한 허용 규칙은 악의적인 행위자에 게 IoT hub를 노출할 수 있습니다. |
| 낮음 | IoT Hub에서 진단 로그 사용 | IoT Hub | 로그를 사용하도록 설정하고 최대 1년간 보존합니다. 로그를 유지 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위해 활동 기록을 다시 만들 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- IoT 서비스용 Defender [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md) 하는 방법 알아보기
- [장치 조사](how-to-investigate-device.md) 에 대 한 자세한 정보
