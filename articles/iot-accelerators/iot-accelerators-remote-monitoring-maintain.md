---
title: 경고를 사용하여 원격 모니터링 솔루션에서 장치 문제 해결 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 경고를 사용하여 원격 모니터링 솔루션 가속기에 연결된 장치의 문제를 확인하고 해결하는 방법을 보여줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 8ea4a615cff02cad1030f10b408fe047f1208a60
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819223"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>자습서: 장치 문제 해결 및 수정

이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 연결된 IoT 장치의 문제를 확인하고 해결합니다. 솔루션 가속기 대시보드에서 경고를 사용하여 문제를 확인한 다음, 원격 작업을 실행하여 문제를 해결합니다.

Contoso는 필드에서 새로운 **프로토타입** 장치를 테스트하고 있습니다. Contoso 운영자로서 테스트하는 동안 **프로토타입** 장치가 대시보드에서 온도 경고를 예기치 않게 트리거하는 것을 알게 됩니다. 이제 이 결함이 있는 **프로토타입** 장치의 동작을 조사하고 문제를 해결해야 합니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 장치의 경고 조사
> * 장치의 문제 해결

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>경고 조사

**대시보드** 페이지에서 **프로토타입** 장치와 관련된 규칙에서 들어오는 예기치 않은 온도 경고가 있다는 것을 알게 됩니다.

[![대시보드에 표시되는 경고](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

문제를 자세히 조사하려면 경고 옆의 **경고 탐색** 옵션을 선택합니다.

[![대시보드에서 경고 탐색](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

경고의 세부 정보 보기는 다음을 보여 줍니다.

* 경고가 트리거된 경우
* 경고와 연결된 장치에 대한 상태 정보
* 경고와 연결된 장치의 원격 분석

[![경고 세부 정보](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

경고를 승인하려면 **경고 발생**을 모두 선택하고 **승인**을 선택합니다. 이 작업을 통해 운영자가 경고를 확인했고 작업 중인 것을 다른 작업자가 알 수 있습니다.

[![경고 승인](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

경고를 승인하면 발생 항목 상태가 **승인됨**으로 변경됩니다.

경고가 발생한 장치 목록에서 온도 경고를 발생시키는 **프로토타입** 장치를 확인할 수 있습니다.

[![경고를 유발하는 장치 나열](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>이 문제 해결

**프로토타입** 장치의 문제를 해결하려면 장치에서 **DecreaseTemperature** 메서드를 호출해야 합니다.

장치에 조치를 취하려면 경고가 발생한 장치 목록에서 해당 장치를 선택한 다음, **작업**을 선택합니다. **프로토타입** 장치 모델은 다음과 같은 6가지 메서드를 지원합니다.

[![장치에서 지원하는 메서드 보기](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

**DecreaseTemperature**를 선택하고 작업 이름을 **DecreaseTemperature**로 설정합니다. 그런 다음 **적용**을 클릭합니다.

[![온도를 낮추는 작업 만들기](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

작업의 상태를 추적하려면 **작업 상태 보기**를 클릭합니다. **작업** 보기를 사용하여 솔루션에서 모든 작업 및 메서드 호출을 추적합니다.

[![온도를 낮추는 작업 모니터링](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

**대시보드** 페이지의 원격 분석 데이터를 보면 장치의 온도가 낮아진 것을 확인할 수 있습니다.

[![온도 감소보기](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 경고를 사용하여 장치와 관련된 문제를 식별하고 문제를 해결하기 위해 해당 장치에 조치를 취하는 방법을 알아봤습니다. 물리적 장치를 솔루션 가속기에 연결하는 방법을 알아보려면 방법 문서를 참조하세요.

장치 문제를 관리하는 방법을 알아본 후, 권장되는 다음 단계는 [장치를 원격 모니터링 솔루션 가속기에 연결](iot-accelerators-connecting-devices.md)하는 방법을 알아보는 것입니다.
