---
title: Azure IoT Central 연산자 가이드
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central 운영자 역할의 개요를 제공 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669946"
---
# <a name="iot-central-operator-guide"></a>IoT Central 연산자 가이드

*이 문서는 연산자에 적용 됩니다.*

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central 응용 프로그램을 사용 하 여 IoT 장치를 관리 하는 운영자를 대상으로 합니다.

연산자:

- 응용 프로그램에 연결 된 장치를 모니터링 하 고 관리 합니다.
- 장치와 관련 된 문제를 해결 하 고 문제를 정책 미준수 상태.
- 새 장치를 프로 비전 합니다.

## <a name="monitor-and-manage-devices"></a>장치 모니터링 및 관리

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="장치 뷰를 표시 하는 스크린샷":::

장치를 모니터링 하기 위해 운영자는 장치 템플릿의 일부로 솔루션 빌더에서 정의한 장치 뷰를 사용할 수 있습니다. 이러한 보기에는 장치 원격 분석 및 속성 값이 표시 될 수 있습니다. 예를 들어 앞의 스크린샷에 표시 된 **개요** 뷰가 있습니다.

자세한 내용은 운영자가 장치 그룹 및 기본 제공 분석 기능을 사용할 수 있습니다. 자세히 알아보려면 analytics를 사용 하 여 [장치 데이터를 분석 하는 방법](howto-create-analytics.md)을 참조 하세요.

운영자는 개별 장치를 관리 하기 위해 장치 뷰를 사용 하 여 장치 및 클라우드 속성을 설정 하 고 장치 명령을 호출할 수 있습니다. 예를 들어 이전 스크린샷에서 장치 및 **명령** 보기 **관리** 를 포함 합니다.

장치를 대량으로 관리 하기 위해 운영자는 작업을 만들고 예약할 수 있습니다. 작업은 속성을 업데이트 하 고 여러 장치에서 명령을 실행할 수 있습니다. 자세히 알아보려면 [Azure IoT Central 응용 프로그램에서 작업 만들기 및 실행](howto-run-a-job.md)을 참조 하세요.

## <a name="troubleshoot-and-remediate-issues"></a>문제 해결 및 문제 해결

운영자는 응용 프로그램 및 해당 장치의 상태를 담당 합니다. [문제 해결 가이드](troubleshoot-connection.md) 를 통해 운영자는 일반적인 문제를 진단 하 고 수정할 수 있습니다. 운영자는 **장치** 페이지를 사용 하 여 문제가 해결 될 때까지 작동 하지 않는 것으로 보이는 장치를 차단할 수 있습니다.

## <a name="add-and-remove-devices"></a>디바이스 추가 및 제거

운영자는 개별적으로 또는 대량으로 IoT Central 응용 프로그램에 장치를 추가 하 고 제거할 수 있습니다. 자세히 알아보려면 [Azure IoT Central 응용 프로그램에서 장치 관리](howto-manage-devices.md)를 참조 하세요.

## <a name="personalize"></a>개인 설정

운영자는 자주 사용 하는 리소스에 대 한 링크를 포함 하는 IoT Central 응용 프로그램에서 개인 설정 된 대시보드를 만들 수 있습니다. 자세히 알아보려면 [대시보드 관리](howto-create-personal-dashboards.md#manage-dashboards)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

IoT Central 사용에 대해 자세히 알아보려면 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)부터 시작하여 빠른 시작을 완료하세요.
