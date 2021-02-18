---
title: 에이전트 기반 보안 사용자 지정 경고
titleSuffix: Azure Defender for IoT
description: IoT 장치의 기능 및 서비스에 대해 Defender를 사용 하 여 사용자 지정 가능한 보안 경고 및 권장 수정 사항에 대해 알아봅니다.
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
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: e885566dd067d70fd4800ca96b8729494464da85
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642515"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>IoT 장치용 Defender 사용자 지정 보안 경고

IoT 용 Defender는 고급 분석 및 위협 인텔리전스를 사용 하 여 IoT 솔루션을 지속적으로 분석 하 여 악의적인 활동을 경고 합니다.

사용자가 고유한 조직 배포 및 환경에서 잠재적인 손상에 대 한 가장 효율적인 지표로 경고를 제공 하도록 예상 되는 장치 동작에 대 한 지식을 바탕으로 사용자 지정 경고를 만드는 것이 좋습니다.

IoT 경고에 대 한 다음 Defender 목록은 예상 IoT 장치 동작에 따라 사용자가 정의할 수 있습니다. 각 경고를 사용자 지정 하는 방법에 대 한 자세한 내용은 [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md)를 참조 하세요.

## <a name="agent-based-security-custom-alerts"></a>에이전트 기반 보안 사용자 지정 경고

| 심각도 | 경고 이름 | 데이터 원본 | 설명 | 제안된 수정 사항 |
|--|--|--|--|--|
| 낮음 | 사용자 지정 경고-활성 연결 수가 허용 된 범위를 벗어남 | 클래식 보안 모듈, Azure RTOS | 특정 시간 범위 내의 활성 연결 수가 현재 구성된 허용 범위를 벗어납니다. | 디바이스 로그를 조사합니다. 연결이 시작된 위치를 알아보고, 무해하거나 악의적인 위치인지 확인합니다. 악의적인 위치이면 가능한 멀웨어를 제거하고 해당 원본을 파악합니다. 무해한 위치이면 해당 원본을 연결 허용 목록에 추가합니다. |
| 낮음 | 사용자 지정 경고-허용 되지 않는 IP에 생성 된 아웃 바운드 연결 | 클래식 보안 모듈, Azure RTOS | 아웃바운드 연결이 IP 허용 목록을 벗어난 IP에 만들어졌습니다. | 디바이스 로그를 조사합니다. 연결이 시작된 위치를 알아보고, 무해하거나 악의적인 위치인지 확인합니다. 악의적인 위치이면 가능한 멀웨어를 제거하고 해당 원본을 파악합니다. 무해한 위치이면 해당 원본을 IP 허용 목록에 추가합니다. |
| 낮음 | 사용자 지정 경고-실패 한 로컬 로그인 수가 허용 된 범위를 벗어남 | 클래식 보안 모듈, Azure RTOS | 특정 기간 내에 실패 한 로컬 로그인 수가 현재 구성 된 범위 및 허용 되는 범위를 벗어납니다. |  |
| 낮음 | 사용자 지정 경고-허용 된 사용자 목록에 없는 사용자의 로그인 | 클래식 보안 모듈, Azure RTOS | 사용자 허용 목록에 없는 로컬 사용자가 디바이스에 로그인했습니다. | 원시 데이터를 저장 하는 경우 log analytics 계정으로 이동 하 여 데이터를 사용 하 여 장치를 조사 하 고 원본을 확인 한 다음 해당 설정에 대 한 허용/차단 목록을 수정 합니다. 현재 원시 데이터를 저장하지 않는 경우 디바이스로 이동하여 해당 설정에 대한 허용/차단 목록을 수정합니다. |
| 낮음 | 사용자 지정 경고-허용 되지 않는 프로세스가 실행 되었습니다. | 클래식 보안 모듈, Azure RTOS | 디바이스에서 허용되지 않는 프로세스가 실행되었습니다. | 원시 데이터를 저장 하는 경우 log analytics 계정으로 이동 하 여 데이터를 사용 하 여 장치를 조사 하 고 원본을 확인 한 다음 해당 설정에 대 한 허용/차단 목록을 수정 합니다. 현재 원시 데이터를 저장하지 않는 경우 디바이스로 이동하여 해당 설정에 대한 허용/차단 목록을 수정합니다. |
|

## <a name="next-steps"></a>다음 단계

- [경고를 사용자 지정](quickstart-create-custom-alerts.md) 하는 방법 알아보기
- IoT 서비스용 Defender [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md) 하는 방법 알아보기
- [장치 조사](how-to-investigate-device.md) 에 대 한 자세한 정보
