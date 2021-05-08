---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 26aefab9b4637c3f3f6b0f0d1195068e2cd770ab
ms.sourcegitcommit: 12f15775e64e7a10a5daebcc52154370f3e6fa0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2021
ms.locfileid: "108001823"
---
<!-- All needs updating -->
Azure IoT Central 애플리케이션의 운영자는 다음을 수행할 수 있습니다.

* **개요** 페이지에서 두 자동 온도 조절기 구성 요소에서 보낸 원격 분석을 봅니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="디바이스 원격 분석 보기":::

* **정보** 페이지에서 디바이스 속성을 봅니다. 이 페이지에서는 디바이스 정보 구성 요소 및 두 개의 자동 온도 조절기 구성 요소에 있는 속성을 보여 줍니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="디바이스 속성 보기":::

## <a name="customize-the-device-template"></a>디바이스 템플릿 사용자 지정

솔루션 개발자는 온도 조절 디바이스가 연결되면 IoT Central이 자동으로 생성한 디바이스 템플릿을 사용자 지정할 수 있습니다.

디바이스와 연결된 고객 이름을 저장하는 클라우드 속성을 추가하려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿** 페이지의 **온도 조절** 디바이스 템플릿으로 이동합니다.

1. **온도 조절** 디바이스 템플릿에서 **클라우드 속성** 을 선택합니다.

1. **클라우드 속성 추가** 를 선택합니다. *고객 이름* 을 **표시 이름** 으로 입력하고 항목을 확장한 다음, **문자열** 을 **스키마** 로 선택합니다. 그런 다음 **저장** 을 선택합니다.

**Get Max-Min report** 명령이 IoT Central 애플리케이션에 표시되는 방식을 사용자 지정하려면 다음을 수행합니다.

1. 디바이스 템플릿에서 **사용자 지정** 을 선택합니다.

1. **getMaxMinReport(thermostat1)** 의 경우 *Get Max-Min report* 를 *Get thermostat1 status report* 로 바꿉니다.

1. **getMaxMinReport(thermostat2)** 의 경우 *Get Max-Min report* 를 *Get thermostat2 status report* 로 바꿉니다.

1. **저장** 을 선택합니다.

**대상 온도** 쓰기 가능 속성이 IoT Central 애플리케이션에 표시되는 방식을 사용자 지정하려면 다음을 수행합니다.

1. 디바이스 템플릿에서 **사용자 지정** 을 선택합니다.

1. **targetTemperature(thermostat1)** 의 경우 *대상 온도* 를 *대상 온도(1)* 로 바꿉니다.

1. **targetTemperature(thermostat2)** 의 경우 *대상 온도* 를 *대상 온도(2)* 로 바꿉니다.

1. **저장** 을 선택합니다.

**온도 조절** 모델의 자동 온도 조절기 구성 요소에는 **대상 온도** 쓰기 가능 속성이 포함되고, 디바이스 템플릿에는 **고객 이름** 클라우드 속성이 포함됩니다. 운영자가 다음 속성을 편집하는 데 사용할 수 있는 보기를 만듭니다.

1. **보기**, **디바이스 및 클라우드 데이터를 편집하는 중** 타일을 차례로 선택합니다.

1. 양식 이름으로 _속성_ 을 입력합니다.

1. **대상 온도(1)** , **대상 온도(2)** 및 **고객 이름** 속성을 선택합니다. 그런 다음, **섹션 추가** 를 선택합니다.

1. 변경 내용을 저장합니다.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="속성 값 업데이트 보기":::

## <a name="publish-the-device-template"></a>디바이스 템플릿 게시

운영자가 만든 사용자 지정을 보고 사용하려면 먼저 디바이스 템플릿을 게시해야 합니다.

**자동 온도 조절** 디바이스 템플릿에서 **게시** 를 선택합니다. **이 디바이스 템플릿을 애플리케이션에 게시** 패널에서 **게시** 를 선택합니다.

이제 운영자는 **속성** 보기를 사용하여 속성 값을 업데이트하고 디바이스 명령 페이지에서 **Get thermostat1 status report** 및 **Get thermostat2 status report** 라는 명령을 호출할 수 있습니다.

* **속성** 페이지에서 쓰기 가능한 속성 값을 업데이트합니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="디바이스 속성 업데이트":::

* **명령** 페이지에서 명령을 호출합니다. 상태 보고서 명령을 실행하는 경우 **다음 이후** 매개 변수를 실행하기 전에 이 매개 변수의 날짜와 시간을 선택합니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="명령 호출":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="명령 응답 보기":::
