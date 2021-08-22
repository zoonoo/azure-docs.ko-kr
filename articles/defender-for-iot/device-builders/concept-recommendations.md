---
title: IoT Hub에 대한 보안 추천 사항
description: 보안 권장 사항의 개념과 Defender for IoT Hub에 사용되는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018279"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub에 대한 보안 추천 사항

Defender for IoT는 Azure 리소스 및 IoT 디바이스를 검색하고, 보안 권장 사항을 제공하여 공격 노출 영역을 줄입니다.
보안 권장 사항은 실행 가능하며, 고객이 보안 모범 사례를 준수하도록 지원하는 것을 목표로 합니다.

이 문서에서는 IoT Hub에서 트리거할 수 있는 권장 사항 목록을 찾을 수 있습니다.

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub의 기본 제공 권장 사항

권장 사항 경고는 환경의 보안 상태를 개선하기 위한 작업에 대한 인사이트 및 제안을 제공합니다.

| 심각도 | 이름 | 데이터 원본 | Description |
|--|--|--|--|
| 높음 | 동일한 인증 자격 증명이 여러 디바이스에서 사용됩니다. | IoT Hub | IoT Hub 인증 자격 증명이 여러 디바이스에서 사용됩니다. 이 프로세스는 합법적인 디바이스를 가장한 불법 디바이스를 표시할 수 있습니다. 중복 자격 증명 사용은 악의적인 행위자의 디바이스 가장 위험을 증가합니다. |
| 중간 | 기본 IP 필터 정책은 거부여야 합니다. | IoT Hub | IP 필터 구성에서는 허용된 트래픽에 대한 규칙이 정의되어야 하고, 기본값으로 다른 모든 트래픽을 거부해야 합니다. |
| 중간 | IP 필터 규칙은 큰 IP 범위를 포함합니다. | IoT Hub | IP 필터 허용 규칙의 원본 IP 범위가 너무 큽니다. 허용 범위가 과도하게 큰 규칙은 IoT 허브를 악의적인 행위자에게 노출할 수 있습니다. |
| 낮음 | IoT Hub에서 진단 로그 사용 | IoT Hub | 로그를 사용하도록 설정하고 최대 1년간 보존합니다. 로그를 보존하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md)하는 방법 알아보기
- [디바이스 조사](how-to-investigate-device.md)에 대한 자세한 정보
