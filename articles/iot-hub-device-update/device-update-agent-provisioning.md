---
title: Azure IoT Hub 에이전트에 대 한 장치 업데이트 프로 비전 | Microsoft Docs
description: Azure IoT Hub 에이전트에 대 한 장치 업데이트 프로 비전
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097757"
---
# <a name="device-update-agent"></a>디바이스 업데이트 에이전트

IoT Hub에 대한 디바이스 업데이트는 이미지 기반 및 패키지 기반의 두 가지 업데이트 형식을 지원합니다. 

* 이미지 업데이트는 디바이스의 최종 상태에서 더 높은 수준의 신뢰도를 제공합니다. 일반적으로 사전 프로덕션 환경과 프로덕션 환경 간에 이미지 업데이트 결과를 복제하는 것이 더 쉽습니다. 패키지 및 해당 종속성과 동일한 문제를 일으키지 않기 때문입니다. 원자성 특성 때문에 A/B 장애 조치(failover) 모델을 쉽게 채택할 수 있습니다. 
* 패키지 기반 업데이트는 디바이스의 특정 구성 요소 또는 애플리케이션만 변경 대상으로 하는 업데이트입니다. 따라서 대역폭 사용이 감소 하 고 업데이트를 다운로드 하 고 설치 하는 시간을 줄이는 데 도움이 됩니다. 패키지 업데이트는 일반적으로 업데이트를 적용할 때 디바이스의 가동 중지 시간을 최소화하고 이미지를 만드는 오버헤드를 방지할 수 있습니다. 

장치 업데이트 에이전트를 빌드, 실행 및 수정 하는 방법에 대 한 아래 링크를 따르세요.

## <a name="build-the-device-update-agent"></a>장치 업데이트 에이전트 빌드

지침에 따라 원본에서 장치 업데이트 에이전트를 [빌드합니다](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) .

## <a name="run-the-device-update-agent"></a>장치 업데이트 에이전트 실행

에이전트가 성공적으로 작성 되 면 에이전트를 [실행](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) 합니다.

## <a name="modifying-the-device-update-agent"></a>장치 업데이트 에이전트 수정

이제 에이전트를 이미지에 통합 하는 데 필요한 변경을 수행 합니다.  지침은 장치 업데이트 에이전트를 [수정](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) 하는 방법을 참조 하세요.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

문제가 발생 하는 경우 IoT Hub [문제 해결](troubleshoot-device-update.md) 을 위한 장치 업데이트 가이드를 검토 하 여 가능한 문제를 차단 해제 하 고 Microsoft에 제공 하는 데 필요한 정보를 수집할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 대 한 장치 업데이트를 쉽게 시연 하려면 미리 작성 된 이미지와 이진 파일을 사용 합니다.  

[이미지 업데이트: Raspberry Pi 3 B + 참조 Yocto 이미지로 시작](device-update-raspberry-pi.md)

[이미지 업데이트: Ubuntu (18.04 x64) 시뮬레이터 참조 에이전트를 사용 하 여 시작](device-update-simulator.md)

[패키지 업데이트: Ubuntu Server 18.04 x64 패키지 에이전트를 사용 하 여 시작](device-update-ubuntu-agent.md)

