---
title: 포함 파일
description: 포함 파일
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449684"
---
## <a name="view-device-telemetry"></a>디바이스 원격 분석 보기

장치에서 보낸 원격 분석을 볼 수 있습니다 합니다 **Device Explorer** 솔루션에는 페이지입니다.

1. 장치 목록에서 프로 비전 한 장치를 선택 합니다 **Device Explorer** 페이지입니다. 패널은 디바이스 원격 분석의 그림을 포함한 디바이스에 대한 정보를 표시합니다.

    ![디바이스 세부 정보 보기](media/iot-suite-visualize-connecting/devicesdetail.png)

1. **압력**을 선택하여 원격 분석 표시를 변경합니다.

    ![압력 원격 분석 보기](media/iot-suite-visualize-connecting/devicespressure.png)

1. 디바이스에 대한 진단 정보를 보려면 **진단** 아래로 스크롤합니다.

    ![디바이스 진단 보기](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>디바이스에서 작동

장치에서 메서드를 호출 하려면 사용 합니다 **Device Explorer** 원격 모니터링 솔루션에서 페이지입니다. 원격 모니터링 솔루션의 예를 들어 **냉각기** 구현 하는 장치를 **재부팅** 메서드.

1. 선택 **장치** 으로 이동 하는 **Device Explorer** 솔루션에는 페이지입니다.

1. 장치 목록에서 프로 비전 한 장치를 선택 합니다 **Device Explorer** 페이지:

    ![실제 디바이스 선택](media/iot-suite-visualize-connecting/devicesselect.png)

1. 장치에서 호출 하 여 메서드의 목록을 표시 하려면 선택 **작업**, 한 다음 **메서드**합니다. 여러 디바이스에서 작업이 실행되도록 예약하려면 목록에서 여러 디바이스를 선택하면 됩니다. **작업** 패널에 선택한 모든 디바이스에 공통된 메서드 형식이 표시됩니다.

1. 선택 **다시 부팅**, 작업 이름으로 설정할 **RebootPhysicalChiller** 를 선택한 후 **적용**:

    ![펌웨어 업데이트 예약](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 시뮬레이션된 디바이스가 메서드를 처리하는 동안 디바이스 코드를 실행하는 콘솔에 메시지 시퀀스가 표시됩니다.

> [!NOTE]
> 솔루션에서 작업의 상태를 추적 하려면 선택할 **작업 상태 보기**합니다.

## <a name="next-steps"></a>다음 단계

[원격 모니터링 솔루션 가속기 사용자 지정](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) 문서에서는 솔루션 가속기를 사용자 지정하는 몇 가지 방법에 대해 설명합니다.